#### Creating CSR request (https://www.golinuxcloud.com/openssl-generate-csr/)

0. To generate a Certificate Signing request you would need a private key. Ideally I would use two different commands to generate each one separately but here let me show you single command to generate both private key and CSR
```
     openssl req -new -newkey rsa:2048 -nodes  -keyout ban27.key -out ban27.csr
```

1. Non interactive way, we need to provide the ssl conf file

```
# cat server_cert.cnf
[req]
distinguished_name = req_distinguished_name
prompt = no
[req_distinguished_name]
C = IN
ST = Karnataka
L = Bengaluru
O = GoLinuxCloud
OU = R&D
CN = ban27.example.com
```

2. We will use the same command as earlier and add -config server_cert.cnf to make sure you are not prompted for any input.
```openssl req -new -newkey rsa:2048 -nodes -keyout server.key -out ban27.csr -config server_cert.cnf ```

3. Verifiy 
```openssl req -noout -text -in ban27.csr```  


#### Create Certificate Authority and sign a certificate with Root CA (https://www.golinuxcloud.com/create-certificate-authority-root-ca-linux/)

Create Key
```openssl genrsa -des3 -passout file:mypass.enc -out ca.key 4096```

Verify Key
```openssl rsa -noout -text -in ca.key -passin file:mypass.enc```

Create CSR
```openssl req -new -x509 -days 365 -key ca.key -out ca.cert.pem -passin file:mypass.enc -config csr.conf```

Verify CSR
```openssl x509 -noout -text -in ca.cert.pem```

Generate a server key and request for signing (CSR)
This step creates a server key, and a request that you want it signed (the .csr file) by a Certificate Authority
```openssl genrsa -des3 -passout file:mypass.enc -out server.key 4096```

We now generate a Certificate Signing Request which contains some of the info that we want to be included in the certificate. To prove ownership of the private key, the CSR is signed with the subject's private key server.key.Think carefully when inputting a Common Name (CN) as you generate the .csr file below. This should match the DNS name, or the IP address you specify in your Apache configuration.
```openssl req -new -key server.key -out server.csr -passin file:mypass.enc -config csr.conf```

OpenSSL verify server key content
```openssl rsa -noout -text -in server.key -passin file:mypass.enc```

OpenSSL verify Certificate Signing Request (CSR)
```openssl req -noout -text -in server.csr```

Sign a certificate with CA
```openssl x509 -req -days 365 -in server.csr -CA ca.cert.pem -CAkey ca.key -CAcreateserial -out server.crt -passin file:mypass.enc```

OpenSSL verify server certificate
```openssl x509 -noout -text -in server.crt```