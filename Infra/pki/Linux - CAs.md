## Cenário
Aqui irei cobrir um cenário básico mas comumente cobrado no contexto de PKI.
**RootCA** : Será usada apenas para assinar a **SubCA**.
**SubCA** : Será usada para assinar os certificados finais.
## Root CA
Após criarmos o diretório `/pki/root` e `/pki/sub` podemos usar como modelo uma configuração já existente no sistema. 
```sh
$ mkdir -p /pki/root && cd /pki/root
$ cat /etc/ssl/openssl.cnf | grep '=\|\[\|^$' > openssl.cnf
```

Agora editamos de acordo com o que precisamos.
```sh
$ vim openssl.cnf
[ ca ]
default_ca      = CA_default            # The default ca section

[ CA_default ]
dir             = /pki/root             # Where everything is kept
certs           = $dir/certs            # Where the issued certs are kept
crl_dir         = $dir/crl              # Where the issued crl are kept
database        = $dir/index.txt        # database index file.
new_certs_dir   = $dir/newcerts         # default place for new certs.

serial          = $dir/serial           # The current serial number
private_key     = $dir/private/root-ca.wsc.local.key.pem # The private key
certificate     = $dir/certs/root-ca.wsc.local.cert.pem         

default_days    = 3650                  # how long to certify for
default_crl_days= 30                    # how long before next CRL
default_md      = sha256                # use SHA-256 by default
preserve        = no                    # keep passed DN ordering

policy          = policy_loose

[ policy_loose ]
countryName             = supplied
stateOrProvinceName     = supplied
organizationName        = supplied
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional


##### CSR #####
[ req ]
default_bits            = 4096
distinguished_name      = req_distinguished_name
string_mask             = utf8only
req_extensions          = v3_req # The extensions to add to a certificate request
default_md              = sha256

[ req_distinguished_name ]
countryName                     = Country Name (2 letter code)
stateOrProvinceName             = State or Province Name (full name)
localityName                    = Locality Name (eg, city)
organizationName                = Organization Name (eg, company)
organizationalUnitName          = Organizational Unit Name (eg, section)
commonName                      = Common Name (eg, your name or your server\'s hostname)


##### ROOT ann SUB Cert #####
[ v3_root_ca ]
subjectKeyIdentifier    = hash
authorityKeyIdentifier  = keyid:always,issuer
basicConstraints        = critical,CA:true
keyUsage                = critical,digitalSignature,cRLSign,keyCertSign

[ v3_sub_ca ]
subjectKeyIdentifier    = hash
authorityKeyIdentifier  = keyid:always,issuer
basicConstraints        = critical,CA:true,pathlen:0
keyUsage                = critical,digitalSignature,cRLSign,keyCertSign
```
#### Cadeia de diretórios
```sh
mkdir -p ./{certs,crl,newcerts,private,csr,keys}
chmod 700 ./private
touch index.txt
echo 1000 > serial
echo 1000 > crlnumber
```
#### Chave e Certificado
```sh
# Criamos a chave privada da CA
$ openssl genrsa -aes256 -out ./private/root-ca.wsc.local.key.pem 4096

# Criamos o certificado da CA
$ openssl req -new -x509 -days 3650 -extensions v3_root_ca \
  -config ./openssl.cnf \
  -key ./private/root-ca.wsc.local.key.pem \
  -out ./certs/root-ca.wsc.local.cert.pem
```
***
## Sub CA
```sh
[ ca ]
default_ca      = CA_default            # The default ca section

[ CA_default ]
dir             = /pki/sub              # Where everything is kept
certs           = $dir/certs            # Where the issued certs are kept
crl_dir         = $dir/crl              # Where the issued crl are kept
database        = $dir/index.txt        # database index file.
new_certs_dir   = $dir/newcerts         # default place for new certs.

serial          = $dir/serial           # The current serial number
private_key     = $dir/private/sub-ca.wsc.local.key.pem # The private key
certificate     = $dir/certs/sub-ca.wsc.local.cert.pem  # The CA certificate

default_days    = 3650                  # how long to certify for
default_crl_days= 30                    # how long before next CRL
default_md      = sha256                # use SHA-256 by default
preserve        = no                    # keep passed DN ordering

policy          = policy_loose

[ policy_loose ]
countryName             = supplied
stateOrProvinceName     = supplied
organizationName        = supplied
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional


##### CSR #####
[ req ]
default_bits            = 4096
distinguished_name      = req_distinguished_name
string_mask             = utf8only
# x509_extensions       = v3_ca # Extension to add when the -x509 option is used
default_md              = sha256

[ req_distinguished_name ]
countryName                     = Country Name (2 letter code)
stateOrProvinceName             = State or Province Name (full name)
localityName                    = Locality Name (eg, city)
organizationName                = Organization Name (eg, company)
organizationalUnitName          = Organizational Unit Name (eg, section)
commonName                      = Common Name (eg, your name or your server\'s hostname)


##### ROOT ann SUB Cert #####
[ v3_req ]
subjectKeyIdentifier    = hash
basicConstraints        = CA:true
keyUsage                = digitalSignature,cRLSign,keyCertSign

[ v3_sub_ca ]
subjectKeyIdentifier    = hash
authorityKeyIdentifier  = keyid:always,issuer
basicConstraints        = critical,CA:true,pathlen:0
keyUsage                = critical,digitalSignature,cRLSign,keyCertSign


##### SERVER #####
[ server_cert ]
basicConstraints        = CA:false
keyUsage                = digitalSignature, keyEncipherment
extendedKeyUsage        = serverAuth
subjectAltName          = @alt_names_server

[ alt_names_server ]
DNS.1   = files.wsc.local
DNS.2   = www.wsc.local
DNS.3   = server.wsc.local
IP.1    = 10.0.10.5

[ client_cert ]
basicConstraints        = CA:false
keyUsage                = digitalSignature
extendedKeyUsage        = clientAuth
subjectAltName          = @alt_names_client

[ alt_names_client ]
DNS.1   = client.wsc.local
IP.1    = 10.0.10.101
```
#### Cadeia de diretórios
```sh
mkdir -p ./{certs,crl,newcerts,private,csr,keys}
chmod 700 ./private
touch index.txt
echo 1000 > serial
echo 1000 > crlnumber
```
#### Chave e Certificado
```sh
# Criamos a chave privada da CA
$ openssl genrsa -aes256 -out ./private/sub-ca.wsc.local.key.pem 4096

# CSR
$ openssl req -config ./openssl.cnf -new -sha256 \
  -key ./private/sub-ca.wsc.local.key.pem \
  -out ../root/csr/sub-ca.wsc.local.csr.pem

# Assinando o certificado
$ cd ../root
$ openssl ca -config ./openssl.cnf \
  -extensions v3_sub_ca -days 3650 -notext -md sha256 \
  -in ../csr/sub-ca.wsc.local.csr.pem \
  -out ../sub/certs/sub-ca.wsc.local.cert.pem

# Concatenando
cat ./root/certs/root-ca.wsc.local.cert.pem >> ../sub/certs/sub-ca.wsc.local.cert.pem
```
***
## Server e Client
#### Certificado Server
```sh
# Criando a chave do certificado
$ openssl genrsa -out ./keys/server.wsc.local.key.pem 4096

# Criando a CSR
$ openssl req -config ./openssl.cnf -new -nodes \
  -key ./keys/server.wsc.local.key.pem \
  -out ./csr/server.wsc.local.csr.pem \
  -extensions server_cert

# Gerando o certificado a partir da CSR
$ openssl ca -config ./openssl.cnf \
  -extensions server_cert -days 825 -notext -md sha256 \
  -in ./csr/server.wsc.local.csr.pem \
  -out ./certs/server.wsc.local.cert.pem

# Concatenando
cat ./sub/certs/sub-ca.wsc.local.cert.pem >> ./certs/server.wsc.local.cert.pem
```
#### Certificado Client
```sh
# Criando a chave do certificado
$ openssl genrsa -out ./keys/client-01.wsc.local.key.pem 4096

# Criando a CSR
$ openssl req -config ./openssl.cnf -new -nodes \
  -key ./keys/client-01.wsc.local.key.pem \
  -out ./csr/client-01.wsc.local.csr.pem \
  -extensions client_cert

# Gerando o certificado a partir da CSR
$ openssl ca -config ./openssl.cnf \
  -extensions client_cert -days 825 -notext -md sha256 \
  -in ./csr/client-01.wsc.local.csr.pem \
  -out ./certs/client-01.wsc.local.cert.pem

# Concatenando
cat ./sub/certs/sub-ca.wsc.local.cert.pem >> ./certs/client-01.wsc.local.cert.pem
```
#### Validação
```sh

# Verificando o campo "Subject Alternative Name"
$ openssl x509 -in ftp.wsc.local.cert.pem -text -noout | grep -A2 "Subject Alternative Name"

# Verifica se o certificado foi assinado pela CA
$ openssl verify -verbose -CAfile /root/PKI/certs/ca.cert.pem ftp.wsc.local.cert.pem

# Aqui podemos especificar a sub ca também
$ openssl verify -CAfile /root/PKI/certs/ca.cert.pem \
  -untrusted intermediate.cert.pem \
  ftp.wsc.local.cert.pem
```
***
## Adicionais
#### Mover para a pasta de certificados confiáveis
```sh
$ cp /root/certs/ca.cert.pem /etc/pki/ca-trust/source/anchors/ca.cert.crt
$ update-ca-trust extract

# Para listar os certificados de CA conficaveis
$ trust list
```
#### Chave e CSR em um comando
```sh
$ openssl req -newkey rsa:2048 -nodes -keyout server.key -out server.csr \
  -subj "/C=BR/ST=SaoPaulo/L=SaoPaulo/O=MyOrg/CN=meuservidor.com"

$ openssl req -newkey rsa:2048 -nodes -keyout server.key -out server.csr \
  -subj "/C=BR/ST=SaoPaulo/L=SaoPaulo/O=MyOrg/CN=meuservidor.com" \
  -addext "subjectAltName=DNS:server.wsc.local,IP:192.168.1.100"
```
#### Adicionar **Subject Alternative Names** no comando
Se tivermos que assinar uma variedade de certificados, cada um com um SAN diferente, podemos usar o parâmetro `-addext`. Caso usemos assim não precisamos passa a diretiva `subjectAltName` e por consequência nem a seção `alt_names_server`.
```sh
$ openssl req -newkey rsa:4096 -sha256 -nodes \
  -keyout server.wsc.local.key -out server.wsc.local.csr \
  -subj "/C=BR/ST=DF/L=Brasilia/O=WSC/OU=TI/CN=server.wsc.local" \
  -addext "subjectAltName = DNS:server.wsc.local, DNS:www.wsc.local, IP:10.0.10.5"
```
---
## Certificado auto assinado
Por vezes teremos de criar cerificados auto assinados para testes, podemos então cria-lo de forma simples.
```sh
openssl req -newkey rsa:4096 -nodes -x509 \
-keyout key.pem -out cert.pem -days 365 \
-subj "/C=BR/ST=DF/L=Brasilia/O=WSC/OU=TI/CN=wsc.local" \
-addext "subjectAltName = DNS:auto.wsc.local,IP:10.0.10.5"
```

***
$$\#54 : Nycolas Ramos$$