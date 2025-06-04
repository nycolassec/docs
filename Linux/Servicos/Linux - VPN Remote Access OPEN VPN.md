### Instalação
```sh
dnf update -y
dnf install epel-release -y
dnf install openvpn easy-rsa -y
```

### CA
Pelo foco dessa documentação ser a configuração da `VPN` e não da `CA`, essa parte será feita com o `easy-rsa`.
#### CA Build
Aqui criaremos 
```sh
# EASY RSA
cd /usr/share/easy-rsa/3

# PKI
./easyrsa init-pki
./easyrsa build-ca
```
#### Certificates
primeiro criamos o do servidor, o nome passado será o `commonName`.
```sh
./easyrsa build-server-full vpn.olimpiada.com nopass
```
![[Pasted image 20250603075728.png]]

Agora criamos o certificado dos clientes.
```sh
./easyrsa build-client-full nycolas.ti.olimpiada.com nopass
```
![[Pasted image 20250603080018.png]]
#### Diffie Hellman & TLS Key
Agora geramos nosso parâmetro do `Diffie Hellman` e a chave para autenticação `TLS`
```sh
./easyrsa gen-dh
openvpn --genkey secret ./pki/ta.key
```
#### Move to server
Pra facilitar nosso trabalho podemos transferir os certificados e as chaves para a pasta de configuração de servidor do `openvpn`.
```sh
cp -pR /usr/share/easy-rsa/3/pki/{issued,private,ca.crt,dh.pem,ta.key} /etc/openvpn/server/
```
### Configure VPN Server
```conf
port        1194
proto       udp
dev         tun

# 
ca          ca.crt
cert        issued/vpn.olimpiada.com.crt
key         private/vpn.olimpiada.com.key
dh          dh.pem

# A rede que será usada no túnel
server      10.0.30.0 255.255.255.0

keepalive   10 120

tls-auth    ta.key 0

# Opções de persistência
persist-key
persist-tun

# Caminhos de log
status      /var/log/openvpn.log
log         /var/log/openvpn.log
log-append  /var/log/openvpn.log

# Verbosidade do log
verb        3
```
