```conf
# Geral
local 10.0.10.5
port 1194
proto udp
dev tun

# Caminhos para certificados e chaves
ca /etc/openvpn/ca/ca.crt
cert /etc/openvpn/server/server.crt
key /etc/openvpn/server/server.key
dh /etc/openvpn/dh.pem
tls-auth /etc/openvpn/ta.key

# Configuração de rede
server 10.8.0.0 255.255.255.0
ifconfig-pool-persist ipp.txt

# Rotas / Push
push "redirect-gateway def1 bypass-dhcp"
push "route 192.168.10.0 255.255.255.0"
push "dhcp-option DNS 10.0.10.5"

# Persistência
keepalive 10 120
persist-key
persist-tun

# Limitações de segurança
user nobody
group nobody

# Cipher / TLS settings
cipher AES-256-CBC
auth SHA256
tls-version-min 1.2

# Logs
status /var/log/openvpn/server-status.log
log /var/log/openvpn/openvpn.log
verb 3

```