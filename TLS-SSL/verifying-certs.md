
## How to verify certificates
It can be useful to check a certificate and key before applying them to your server. The following commands help verify the certificate, key, and CSR (Certificate Signing Request).

1. Check a certificate
    Check a certificate and return information about it (signing authority, expiration date, etc.):
    ```openssl x509 -in server.crt -text -noout``` 

2.  Check a key
    Check the SSL key and verify the consistency:
    ```openssl rsa -in server.key -check```

3. Check a CSR
Verify the CSR and print CSR data filled in when generating the CSR:
    ```openssl req -text -noout -verify -in server.csr```

4. Verify a certificate and key matches
These two commands print out md5 checksums of the certificate and key; the checksums can be compared to verify that the certificate and key match.

    ```openssl x509 -noout -modulus -in server.crt| openssl md5 ```

    ```openssl rsa -noout -modulus -in server.key| openssl md5 ```
