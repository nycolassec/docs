Testar se são da mesma chave
```sh
openssl x509 -noout -modulus -in /etc/ssl/ldap/ldap.crt | openssl md5 openssl rsa -noout -modulus -in /etc/ssl/ldap/ldap.key | openssl md5
```





















