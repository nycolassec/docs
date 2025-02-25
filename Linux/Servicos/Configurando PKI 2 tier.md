# Criando uma CA

# Root Pair

O primeiro par criptográfico é o **root pair**. Ele consiste na **root key(ca.cert.pem)** e no **root certificate(ca.crt.pem)**

## Root Directory

Deverá ser feita a escolha de um diretório para armazenar os arquivos da root ca (/root/ca).

```bash
mkdir /root/ca
```

Criaremos a estrutura dos diretórios. Os arquivos index.txt e serial irão atuar como um banco de dados para rastrear certificados assinados.

```bash
cd /root/ca
mkdir certs crl newcerts private
touch index.txt
echo 1000 > serial
```

## Configuration file

Criaremos um arquivo de configuração para a nossa root CA (/root/ca/openssl.cnf), apesar de serem muitas opções podemos separa-las em seções o que facilitará nosso entendimento.

### [ ca ]

```bash
[ ca ]
default_ca = CA_default
```

Aqui indicamos quais serão as configurações padrões, nesse caso indicamos outra seção.

### [ CA_default ]

```bash
[ CA_default ]
# Diretório e localização dos arquivos
dir = /root/ca
certs = $dir/certs
crl_dir = $dir/crl
new_certs_dir = $dir/newcerts
database = $dir/index.txt
serial = $dir/serial

# root key e o root certificate
private_key = $dir/private/ca.key.pem
certificate = $dir/certs/ca.cert.pem

# Lista de revogação de certificados
crlnumber = $dir/crlnumber
crl = $dir/crl/ca.crl.pem
crl_extensions = crl_ext
default_crl_days = 30

default_md = sha256

name_opt = ca_default
cert_opt = ca_default
default_days = 375
preserve = no
policy = policy_strict
```

Usaremos policy_strict, pois é a política mais restritiva e só usaremos a root CA para assinar certificados de intermediate CAs.

### [ policy_strict ]

```bash
[ policy_strict ]
countryName = match
stateOrProvinceName = match
organizationName = match
organizationalUnitName = optional
commonName = supplied
emailAddress = optional
```

### [ policy_loose ]

```bash
[ policy_loose ]
countryName = optional
stateOrProvinceName = optional
localityName = optional
organizationName = optional
organizationalUnitName = optional
commonName = supplied
emailAddress = optional
```

policy_loose será aplicado na intermediate CA, pois irá assinar os certificados de terceiros.

### [ req ]

```bash
[ req ]
default_bits = 2048
distinguished_name = req_distinguished_name
string_mask = utf8only
default_md = sha256

# Entensão a ser adicionada quando a opção -x509 é usada
x509_extensions = v3_ca
```

Essas opções de req serão aplicadas ao criar tanto um certificado quanto uma requisição de certificado.

### [ req_distinguished_name ]

```bash
[ req_distinguished_name ]
commonName = Common Name
countryName = Country Name (2 letter code)
stateOrProvinceName = State or Province Name
localityName = Locality Name
0.organizationName = Organization Name
organizationalUnitName = Organizational Unit Name
emailAddress = Email Address

# Opcionalmente podemos especicar o valor padrão de cada opção:
countryName_default             = GB
stateOrProvinceName_default     = England
localityName_default            =
0.organizationName_default      = Alice Ltd
#organizationalUnitName_default =
#emailAddress_default           =
```

A seção de **[ req_distinguished_name ]** declara as informações normalmente necessárias em uma solicitação de assinatura.

### [ v3_ca ]

```bash
[ v3_ca ]
subjectKeyIdentifier   = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints       = critical, CA:true
keyUsage               = critical, digitalSignature, cRLSign, keyCertSign
```

Essas configurações serão aplicadas quando criarmos o certificado raiz.

### [ v3_intermediate_ca ]

```bash
[ v3_intermediate_ca ]
subjectKeyIdentifier   = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints       = critical, CA:true, pathlen:0
keyUsage               = critical, digitalSignature, cRLSign, keyCertSign
```

Aplicaremos essas configurações ao criarmos os certificados intermediários.

- **pathlen:0** : Garante que não haverá autoridades certificadoras abaixo da CA intermediária.

### [ usr_cert ]

```bash
[ usr_cert ]
basicConstraints       = CA:FALSE
nsCertType             = client, email
nsComment              = "OpenSSL Generated Client Certificate"
subjectKeyIdentifier   = hash
authorityKeyIdentifier = keyid,issuer
keyUsage               = critical, nonRepudiation, digitalSignature, keyEncipherment
extendedKeyUsage       = clientAuth, emailProtection
```

Aplicaremos essa extensão ao assinar certificados de usuários.

### [ server_cert ]

```bash
[ server_cert ]
basicConstraints       = CA:FALSE
nsCertType             = server
nsComment              = "OpenSSL Generated Server Certificate"
subjectKeyIdentifier   = hash
authorityKeyIdentifier = keyid,issuer:always
keyUsage               = critical, nonRepudiation, digitalSignature, keyEncipherment
extendedKeyUsage       = serverAuth
```

Aplicaremos essa extensão ao assinar certificados de servidor, como aqueles de servidores web.

### [ crl_ext ]

```bash
[ crl_ext ]
authorityKeyIdentifier = keyid:always
```

Essa extensão é aplicada automaticamente ao criar listas de revogação de certificados.

### [ oscp ]

```bash
[ ocsp ]
basicConstraints       = CA:FALSE
subjectKeyIdentifier   = hash
authorityKeyIdentifier = keyid,issuer
keyUsage               = critical, digitalSignature
extendedKeyUsage       = critical, OCSPSigning
```

Essa extensão será aplicada ao assinar o certificado do OCSP

---

## Root key e Root certificate

### Criação da chave raiz

```bash
cd /root/ca
openssl genrsa -aes256 -out private/ca.key.pem 4096

chmod 400 private/ca.key.pem
```

### Crição do certificado raiz

Sempre que usarmos a opção **req** devemos especificar o arquivo de configuração com a opção -config. Caso não seja especificado o OpenSSL irá usar o padrão **/etc/pki/tls/openssl.cnf**.

```bash
openssl req -config openssl.cnf -key private/ca.key.pem -new -x509 -days 7300 -sha256 -extensions v3_ca -out certs/ca.cert.pem

chmod 444 certs/ca.cert.pem

openssl x509 -noout -text -in certs/ca.cert.pem
```

---

# **Intermediate pair**

## Intermediate directory

Devemos criar um diretório para as configurações da intermediate CA.

```bash
mkdir /root/ca/intermediate

cd /root/ca/intermediate
mkdir certs crl csr newcerts private
chmod 700 private
touch index.txt
echo 1000 > serial
```

Devemos adicionar um número ao arquivo de crl, para controle das revogações.

```bash
echo 1000 > /root/ca/intermediate/crlnumber
```

Podemos copiar o arquivo de configuração da root CA para a intermediate CA. Devemos alterar apenas cinco opções.

```bash
[ CA_default ]
dir         = /root/ca/intermediate
private_key = $dir/private/intermediate.key.pem
certificate = $dir/certs/intermediate.cert.pem
crl         = $dir/crl/intermediate.crl.pem
policy      = policy_loose
```

## Intermediate key e Intermediate Certificate

### Criação da chave intermediária

```bash
cd /root/ca
openssl genrsa -aes256 -out intermediate/private/intermediate.key.pem 4096

chmod 400 intermediate/private/intermediate.key.pem
```

### Criação do certificado intermediário

Use a chave intermediária para criar uma solicitação de assinatura de certificado (CSR). Os detalhes devem geralmente corresponder à CA raiz. O **Common Name** , no entanto, deve ser diferente.

> Devemos especificar o arquivo de configuração **intermediate/openssl.cnf**
> 

```bash
cd /root/ca

openssl req -config intermediate/openssl.cnf -new -sha256 \
-key intermediate/private/intermediate.key.pem \
-out intermediate/csr/intermediate.csr.pem
```

Para criar um certificado intermediário, use a CA raiz com a extensão **v3_intermediate_ca** para assinar o CSR intermediário. Na assinatura devemos especificar o arquivo de configuração da CA raiz

### Assinando o certificado intermediário

```bash
cd /root/ca

openssl ca -config openssl.cnf -extensions v3_intermediate_ca \
-days 3650 -notext -md sha256 -in intermediate/csr/intermediate.csr.pem \
-out intermediate/certs/intermediate.cert.pem

chmod 444 intermediate/certs/intermediate.cert.pem
```

### Verificando o certificado intermediário

Verificando as informações do certificado

```bash
openssl x509 -noout -text -in intermediate/certs/intermediate.cert.pem
```

Verifique o certificado intermediário em relação ao certificado raiz. Um **OK** indica que a cadeia de confiança está intacta.

```bash
openssl verify -CAfile certs/ca.cert.pem intermediate/certs/intermediate.cert.pem

```

### Criando a cadeia de confiança

Para criar a cadeia de certificados CA, concatene os certificados intermediário e raiz juntos. Caso o certificado da nossa CA esteja instalado no cliente não precisamos concatenar o certificado.

```bash
cat intermediate/certs/intermediate.cert.pem certs/ca.cert.pem > \
    intermediate/certs/ca-chain.cert.pem
chmod 444 intermediate/certs/ca-chain.cert.pem
```

---

# Assinando certificados de clientes e servidores

Para assinar usaremos nossa CA intermediária.

## Criando uma chave

Os certificados de servidor e cliente normalmente expiram após um ano, então podemos usar 2048 bits com segurança.Você pode querer omitir a opção `-aes256` de criar uma chave sem uma senha.

```bash
cd /root/ca
openssl genrsa -aes256 -out intermediate/private/www.corp.com.key.pem 2048
chmod 400 intermediate/private/www.corp.com.key.pem
```

Se o cliente tiver já tiver gerado as chaves, podemos pular essa parte.

## Criar um certificado

primeiro criamos uma solicitação de assinatura com a chave privada. O common name do servidor deve ser o seu FQDN enquanto o do usuário pode ser qualquer identificador exclusivo como o email.

Em certificado de servidores devemos fornecer a extensão **subjectAltName**, para que um certificado seja válido. Podemos usar o opçãp **-addext** conforme no exemplo.

Para manter esta extensão quando a CA intermediária assinar este CSR, `copy_extensions = copy` deve estar presente na seção `[ CA_default ]` em `intermediate/openssl.cnf` 

```bash
cd /root/ca
openssl req -config intermediate/openssl.cnf \
-key intermediate/private/www.corp.com.key.pem \
-new -sha256 -out intermediate/csr/www.corp.com.csr.pem \
-addext "subjectAltName = DNS:corp.com,DNS:www.corp.com"
```

Para criar um certificado use a CA intermediária para assinar o CSR. Se for usada para assinar um certificado de um servidor use a extensão `[ server_cert ]` caso for para um usuário use a `[ usr_cert ]`. Os certificado normalmente recebem a validade de um ano.

```bash
cd /root/ca

openssl ca -config intermediate/openssl.cnf -extensions server_cert \
-days 375 -notext -md sha256 -in intermediate/csr/www.corp.com.csr.pem \
-out intermediate/certs/www.corp.cert.pem

chmod 444 intermediate/certs/www.corp.com.cert.pem
```

O arquivo `intermediate/index.txt` deve conter uma linha referente a este novo certificado.

## Verificando o certificado

```bash
openssl x509 -noout -text -in intermediate/certs/certs/www.corp.com.cert.pem
```

O **Issuer** é a CA intermediária. O **Subject** se refere ao certificado em si.

A saída também mostrará as **extensões X509v3** . Ao criar o certificado, você usou a extensão `server_cert` ou `usr_cert`. As opções da seção de configuração correspondente serão refletidas na saída.

Use o arquivo de cadeia de certificados da CA que criamos anteriormente ( `ca-chain.cert.pem`) para verificar se o novo certificado tem uma cadeia de confiança válida.

```bash
openssl verify -CAfile intermediate/certs/ca-chain.cert.pem \
intermediate/certs/www.corp.com.cert.pem
```

## Implantação do certificado

Ao implantar em um aplicativo de servidor (por exemplo, Apache), você precisa disponibilizar os seguintes arquivos:

- ca-chain.cert.pem
- www.corp.com.cert.pem
- www.corp.com.key.pem

## Pacotes de certificado

Alguns navegadores podem reclamar se você tentar importar um certificado de cliente e sua chave no formato pem, então você precisará agrupá-los. Você pode até querer adicionar ao pacote outros certificados significativos usando a opção `-certfile`.

```bash
openssl pkcs12 -export  -out intermediate/certs/client.full.pfx \
-inkey intermediate/private/client.key.pem -in intermediate/certs/client.cert.pem \
-certfile intermediate/certs/intermediate.cert.pem -certfile certs/ca.cert.pem
```