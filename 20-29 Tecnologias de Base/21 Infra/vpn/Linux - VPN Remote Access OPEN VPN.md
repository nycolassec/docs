### Instalação
```sh
dnf update -y
dnf install epel-release -y
dnf install openvpn easy-rsa -y
```
***
## PKI
Pelo foco da documentação ser a VPN uma root CA será utilizada para assinar os certificados do servidor e dos clients.
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
$ openssl dhparam -out ./pki/dh.pem 2048
$ openvpn --genkey secret ./pki/ta.key
```
#### Move to server
Pra facilitar nosso trabalho podemos transferir os certificados e as chaves para a pasta de configuração de servidor do `openvpn`.
```sh
cp -pR /usr/share/easy-rsa/3/pki/{issued,private,ca.crt,dh.pem,ta.key} /etc/openvpn/server/
```
***
## Configure VPN Server
```conf
# Geral
local   10.0.10.5
port    1194
proto   udp
dev     tun

# Certificados e chaves
ca          chain-ca-wsc.local.crt
cert        vpn-server.wsc.local.cert.pem
key         vpn-server.wsc.local.key.pem
dh          vpn-server.wsc.local.dh.pem
tls-crypt   vpn-server.wsc.local.ta.key 0

# Configuração de rede   
server                  10.8.0.0 255.255.255.0
ifconfig-pool-persist   ipp.txt
topology                subnet

# Rotas
push "route 192.168.10.0 255.255.255.0"
push "redirect-gateway def1 bypass-dhcp"
push "dhcp-option DNS 10.0.10.5"

; client-to-client # Permite que os clients se comuniquem dentro do túnel da VPN
; duplicate-cn     # Permite que vários clients utilizem o mesmo certificado

# Cipher
cipher  AES-256-CBC
auth    SHA256

# Persistência
keepalive 10 120
persist-key
persist-tun

# Logs
status      server-status.log
log         openvpn.log
log-append  openvpn.log
verb        3
```
***
## Client
```conf
# Geral
client
proto   udp
remote  10.0.10.5 1194
dev     tun

# Persistência
resolv-retry infinite
nobind
persist-key
persist-tun

# Certificados e chaves
ca          chain-ca.wsc.local.key.pem
cert        vpn-client.wsc.local.cert.pem
key         vpn-client.wsc.local.cert.pem
tls-crypt   vpn-server.wsc.local.ta.key 1
remote-cert-tls server

# Cipher
data-ciphers-fallback AES-256-CBC
auth SHA256
auth-nocahe
```