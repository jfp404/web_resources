# WSL 2 Cisco AnyConnect Networking Workaround
## Overview

WSL 2 uses a Hyper-V Virtual Network adapter. Network connectivity works without any issue when a VPN is not in use. However when a Cisco AnyConnect VPN session is established Firewall Rules and Routes are added which breaks connectivity within the WSL 2 VM. This issue is tracked [WSL/issues/4277](https://github.com/microsoft/WSL/issues/4277)

Below outline steps to automatically configure the Interface metric on VPN connect and update DNS settings (/etc/resolv.conf) on connect/disconnect.


## Manual Configuration
### Set Interface Metrics
After connecting to the VPN, you'll want to modify the Interface Metric of the Cisco VPN Adapter
```powershell
PS C:\Users\gyurgyik> Get-NetAdapter | Where-Object {$_.InterfaceDescription -Match "Cisco AnyConnect"} | Set-NetIPInterface -InterfaceMetric 6000
```
Run the following command in Powershell with Administrative permission.

At this point you should have connectivity in your container (but without name resolution). You can test this by running `ping 8.8.8.8`. 

### Set DNS servers in Linux VM
Once connected the VPN determine the DNS servers that are configured:
```powershell
PS C:\Users\gyurgyik> (Get-NetAdapter | Where-Object InterfaceDescription -like "Cisco AnyConnect*" | Get-DnsClientServerAddress).ServerAddresses
10.10.0.124
10.10.0.132
```

Update /etc/resolv.conf
```shell
N-20S5PF20MB4R:~$ cat /etc/resolv.conf
nameserver 10.10.0.124
nameserver 10.10.0.132
```

Verify Connectivity
```shell
ping google.com -c 4
```

## Automatic Configuration

### Create Scripts
Save the following scripts to `%homepath%\wsl\scripts`

#### setCiscoVpnMetric.ps1
```powershell
Get-NetAdapter | Where-Object {$_.InterfaceDescription -Match "Cisco AnyConnect"} | Set-NetIPInterface -InterfaceMetric 6000
```

#### setDns.ps1
```powershell
$dnsServers = (Get-NetAdapter | Where-Object InterfaceDescription -like "Cisco AnyConnect*" | Get-DnsClientServerAddress).ServerAddresses -join ','
$searchSuffix = (Get-DnsClientGlobalSetting).SuffixSearchList -join ','

function set-DnsWsl($distro) {
    if ( $dnsServers ) {    
        wsl.exe -d $distro -u root /opt/wsl_dns.py --servers $dnsServers --search $searchSuffix
    }
    else {
        wsl.exe -d $distro -u root /opt/wsl_dns.py
    }
}

set-DnsWsl fedora
```
#### wsl_dns.py
Download from: https://gist.githubusercontent.com/pyther/20bba4aee1a7e1485dd065adbf736891/raw/bb00f46d30da1b1eb7ba0fec4e6946a654c7b186/wsl_dns.py


### Disable WSL Resolv Update
For each Linux instance:
1. Disable automatic updating of resolv.conf by WSL
    ```
    $ cat <<EOF > /etc/wsl.conf
    [network]
    generateResolvConf = false
    EOF
    ```
2. Restart/Shutdown WSL: `wsl --shutdown` (WARNING: this will kill all current sessions!)

### Copy wsl_dns.py to Linux VMs
For each VM, run:
```shell
$ cp /mnt/c/Users/$username/wsl/scripts/wsl_dns.py /opt/wsl_dns.py
$ chmod +x /opt/wsl_dns.py
```

### Create Scheduled Tasks
Windows Scheduled Tasks allows you to trigger an action when a certain log event comes in. The Cisco AnyConnect VPN client generates a number of log events.

We will create two tasks. The first task, will configure the interface metric when the VPN connects. The second task, will execute the dns update script inside of your Linux VM when the VPN Connects and Disconnects.

#### Cisco AnyConnect Events
- 2039: VPN Established and Passing Data
- 2061: Network Interface for the VPN has gone down
- 2010: VPN Termination
- 2041: The entire VPN connection has been re-established.

#### Procedure
1. Open Task Scheduler
2. Create a Folder called `WSL` (Optional, but easier to find rules later)
3. Create Rules
    1. Update AnyConnect Adapter Interface Metric for WSL2
        * General: Check: Run with highest privileges
        * Triggers:
            * On an Event, Log: `Cisco AnyConnect Secure Mobility Client`, Source: `acvpnagent`, Event ID: `2039`
            * On an Event, Log: `Cisco AnyConnect Secure Mobility Client`, Source: `acvpnagent`, Event ID: `2041`
        * Action: Start a program, Program: `Powershell.exe`, Add arguments: `-WindowStyle Hidden -NonInteractive -ExecutionPolicy Bypass -File %HOMEPATH%\wsl\scripts\setCiscoVpnMetric.ps1`
        * Condition: Uncheck: Start the task only if the computer is on AC power
    2. Update DNS in WSL2 Linux VMs
        * Triggers:
            * On an Event, Log: `Cisco AnyConnect Secure Mobility Client`, Source: `acvpnagent`, Event ID: `2039`
            * On an Event, Log: `Cisco AnyConnect Secure Mobility Client`, Source: `acvpnagent`, Event ID: `2010`
            * On an Event, Log: `Cisco AnyConnect Secure Mobility Client`, Source: `acvpnagent`, Event ID: `2061`
            * On an Event, Log: `Cisco AnyConnect Secure Mobility Client`, Source: `acvpnagent`, Event ID: `2041`
            * At log on: At log on of $USER 
        * Action: Start a program, Program: `Powershell.exe`, Add arguments: `-WindowStyle Hidden -NonInteractive -ExecutionPolicy Bypass -File %HOMEPATH%\wsl\scripts\setDns.ps1`
        * Condition: Uncheck: Start the task only if the computer is on AC power
4. Test: Connect to the VPN, a powershell window should pop-up briefly