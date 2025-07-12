## Root CA
Esta configuração irá cobrir somente o necessário para uma CA utilizável. Aqui será definido 3 extensões, uma para a CA uma para certificado de Server e uma para certificado de Client.
```sh
$ mkdir /root/pki/ && cd /root/pki/

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
certificate     = $dir/certs/root-ca.wsc.local.cert.pem         # The CA certificate

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
req_extensions          = v3_req # The extensions to add to a certificate request

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


##### SERVER Cert #####
[ server_cert ]
basicConstraints        = CA:false
keyUsage                = digitalSignature,cRLSign,keyCertSign
extendedKeyUsage        = serverAuth
subjectAltName          = @alt_names_server

[ alt_names_server ]
DNS.1   = files.wsc.local
DNS.2   = www.wsc.local
DNS.3   = files.wsc.local
```
#### Cadeia de diretórios
```sh
mkdir -p ./{certs,crl,newcerts,private,csr,keys}
chmod 700 ./private
touch index.txt
echo 1000 > serial
echo 1000 > crlnumber
```
#### Chave e Certificado da CA
```sh
# Criamos a chave privada da CA
$ openssl genrsa -aes256 -out ./private/ca-root.wsc.local.key.pem 4096

# Criamos o certificado da CA
$ openssl req -new -x509 -days 3650 -extensions v3_root_ca \
  -config ./openssl.cnf \
  -key ./private/root-ca.wsc.local.key.pem \
  -out ./certs/root-ca.wsc.local.cert.pem
```
#### Certificado Server
```sh
# Criando a chave do certificado
$ openssl genrsa -out ./keys/ftp.wsc.local.key.pem 4096

# Criando a CSR
$ openssl req -config /root/PKI/openssl.cnf \
  -key ./keys/ftp.wsc.local.key.pem \
  -new -nodes -out ./csr/ftp.wsc.local.csr.pem

# Gerando o certificado a partir da CSR
$ openssl ca -config /root/PKI/openssl.cnf \
  -extensions server_cert -days 825 -notext -md sha256 \
  -in ./csr/ftp.wsc.local.csr.pem \
  -out ./certs/ftp.wsc.local.cert.pem
```

```sh
$ openssl req -newkey rsa:4096 \
  -nodes \
  -keyout ./keys/ftp.wsc.local.key.pem \
  -out ./csr/ftp.wsc.local.csr.pem \
  -config /root/PKI/openssl.cnf \
  -extensions server_cert

# Gerando o certificado a partir da CSR
$ openssl ca -config /root/PKI/openssl.cnf \
  -extensions server_cert -days 825 -notext -md sha256 \
  -in ./csr/ftp.wsc.local.csr.pem \
  -out ./certs/ftp.wsc.local.cert.pem
```
#### Certificado Client
```sh
# Criando a chave do certificado
$ openssl genrsa -out ./keyclient-01.wsc.local.key.pem 4096

# Criando a CSR
$ openssl req -config /root/PKI/openssl.cnf \
  -extensions client_cert \
  -key ./keys/client-01.wsc.local.key.pem \
  -new -nodes -out ./csr/client-01.wsc.local.csr.pem

# Gerando o certificado a partir da CSR
$ openssl ca -config /root/PKI/openssl.cnf \
  -extensions client_cert \
  -days 825 -md sha256 -batch \
  -in ./csr/client-01.wsc.local.csr.pem \
  -out ./certs/client-01.wsc.local.cert.pem
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
#### Mover para a pasta de certificados confiáveis
```sh
$ cp /root/certs/ca.cert.pem /etc/pki/ca-trust/source/anchors/ca.cert.crt
$ update-ca-trust extract

# Para listar os certificados de CA conficaveis
$ trust list
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