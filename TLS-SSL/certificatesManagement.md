#Converting Java keystore and truststore
The first steps to easily handle your certificates from Go is to convert them to a set of PEM files.
Here are the commands to extract the Certificate Authority (CA) certificate:
``` 
$keytool -importkeystore -srckeystore kafka.server.truststore.jks -destkeystore server.p12 -deststoretype PKCS12
$openssl pkcs12 -in server.p12 -nokeys -out server.cer.pem

You can then convert your client keystore to be usable from Go, with similar commands:

```
$ keytool -importkeystore -srckeystore kafka.server.keystore.jks -destkeystore client.p12 -deststoretype PKCS12
$ openssl pkcs12 -in client.p12 -nokeys -out client.cer.pem 
$openssl pkcs12 -in client.p12 -nodes -nocerts -out client.key.pem