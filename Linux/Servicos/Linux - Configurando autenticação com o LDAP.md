Algumas RFCs são interessantes antes de serem lidas, para facilitar o entendimento de quais opções temos ao configurar o LDAP, sendo elas a RFC 4519(LDAP) e 2307(NIX).

---
## Servidor

#### Instalação e inicialização :
```shell
# Adicionando outro repositório
dnf update -y
dnf install epel-release -y
dnf update -y

## Instalação dos pacotes
dnf install openldap openldap-clients -y
dnf install openldap-servers  -y

# Regra de firewall
firewall-cmd --add-service=ldap --permanent
firewall-cmd --reload

## Inicialização do serviço
systemctl enable slapd
systemctl start slapd

# Cria uma senha criptografada. Salve em algum aequivo para ser usada
slappasswd
```

### Diretórios
Todos os arquivos de configuração do OpenLdap ficam armazenados em `/etc/openldap/`;
![[Pasted image 20250204091347.png]]

Enquanto os schemas e os arquivos que servem como o banco de dados (**.ldif**) ficam em `/etc/openldap/schema/`;
![[Pasted image 20250204091612.png]]

> [!important] LDIF
> Os arquivos .ldif servem como um banco de dados baseados nos schemas

E os arquivos de configuração gerais são armazenados em `/etc/openldap/slapd.d/cn=config/`
![[Pasted image 20250204092427.png]]

### Modificando
O ideal é que não modifiquemos os arquivos **.ldif** manualmente, e sim, criarmos nossos arquivos **.ldif** e usarmos o ``ldapmodify`` para aplicar essas modificações.

> [!important] Organização
> Os arquivos listados a seguir podem ser armazenados em qualquer lugar de acordo com a sua organização.
##### db.ldif
Aqui passamos o arquivo de configuração geral do ldap.
```ldif
dn: olcDatabase={2}mdb,cn=config
changetype: modify
replace: olcSuffix
olcSuffix: dc=corp,dc=com

dn: olcDatabase={2}mdb,cn=config
changetype: modify
replace: olcRootDN
olcRootDN: cn=ldap_admin,dc=corp,dc=com

dn: olcDatabase={2}mdb,cn=config
changetype: modify
replace: olcRootPW
olcRootPW: {SSHA}4bFXTwrh3YpjVSa8LYglsB1IoOuzvPJ6
```
Modificamos o nome do domínio, o administrador e a senha gerada como o `slappasswd`

```shell
ldapmodify -Y EXTERNAL -H ldapi:/// -f db.ldif
```
![[Pasted image 20250204103105.png]]
Para aplicarmos as modificações incluídas em um arquivo usamos o ``ldapmodify`` e a url ``ldapi:///`` que indica o lacalhost.

##### monitor.ldif
```ldif
dn: olcDatabase={1}monitor,cn=config
changetype: modify
replace: olcAccess
olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth" read by dn.base="cn=ldap_admin,dc=corp,dc=com" read by * none
```
Aqui também indicamos qual o usuário administrador do sistema de diretórios.

```shell
ldapmodify -Y EXTERNAL -H ldapi:/// -f monitor.ldif
```
![[Pasted image 20250204103905.png]]
### Configuração dos certificados
Nessa LAB estarei usando um certificado autoassinado, isso indica que o host será a CA também. Os arquivos devem ter as permissões adequadas.
##### Gerando os arquivos de certificado
```sh
# Gerando a chave privada
openssl genpkey -algorithm RSA -out /etc/ssl/openldap/ldap-server.key -aes256

# Gerando o certificado criptografado
openssl req -new -x509 -key /etc/ssl/openldap/ldap-server.key -out /etc/ssl/openldap/ldap-server.crt.crypt -days 3650

# Descriptografando o certificado
openssl rsa -in /etc/ssl/openldap/ldap-server.key.crypt -out /etc/ssl/openldap/ldap-server.key

# Permissões
chown ldap:ldap /etc/ssl/openldap/*
chmod 600 /etc/ssl/openldap/*
```
Precisamos descriptografar o certificado, pois o **sssd** não suporta certificados criptografados.
##### cert.ldif
```sh
# Comando ldapmodify para adicionar configurações TLS no OpenLDAP
dn: cn=config
changetype: modify

add: olcTLSCertificateFile
olcTLSCertificateFile: /etc/ssl/openldap/ldap-server.crt

add: olcTLSCertificateKeyFile
olcTLSCertificateKeyFile: /etc/ssl/openldap/ldap-server.key

add: olcTLSCACertificateFile
olcTLSCACertificateFile: /etc/ssl/openldap/ldap-server.crt
```
Caso essas instruções já existam, podemos trocar o ``add`` pelo ``replace``.
```sh
ldapmodify -Y EXTERNAL -H ldapi:/// -f cert.ldif
```
##### /etc/openldap/ldap.conf
```conf
BASE    dc=corp,dc=com
URI     ldap://ldap.corp.com
SASL_NOCANON    on
TLS_CACERT /etc/ssl/openldap/ldap-server.crt
```

### Criando entradas
##### Imports padrão
```shell
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif 
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif
```
Alguns schemas dependem de outros schemas, então primeiro importamos os schemas que costumam ser gerais.
##### dc.ldif
```ldif
dn: dc=corp,dc=com
dc: corp
objectClass: top
objectClass: domain

dn: cn=ldap_admin,dc=corp,dc=com
objectClass: organizationalRole
cn: ldap_admin
description: LDAP Manager
```
Aqui indicamos um novo domínio e o caminho do administrador.
```shell
ldapadd -x -W -D "cn=ldap_admin,dc=corp,dc=com" -f dc.ldif
```
![[Pasted image 20250204112921.png]]
##### country.ldif
```ldif
dn: c=BR,dc=corp,dc=com
objectClass: country
c: BR
```
Aqui coloquei apenas um país.
```shell
ldapadd -x -W -D "cn=ldap_admin,dc=corp,dc=com" -f country.ldif
```
![[Pasted image 20250204113044.png]]
##### organization.ldif
```ldif
dn: o=filial-SP,c=BR,dc=corp,dc=com
objectClass: organization
o: filial-SP

dn: o=filial-DF,c=BR,dc=corp,dc=com
objectClass: organization
o: filial-DF
```
Aqui criei duas organization.
```shell
ldapadd -x -W -D "cn=ldap_admin,dc=corp,dc=com" -f organization.ldif
```
![[Pasted image 20250204113138.png]]
##### organizational_unit.ldif
```ldif
dn: ou=Marketing,o=filial-DF,c=BR,dc=corp,dc=com
objectClass: organizationalUnit
ou: Marketing

dn: ou=Ti,o=filial-DF,c=BR,dc=corp,dc=com
objectClass: organizationalUnit
ou: Ti

dn: ou=Marketing,o=filial-SP,c=BR,dc=corp,dc=com
objectClass: organizationalUnit
ou: Marketing

dn: ou=Ti,o=filial-SP,c=BR,dc=corp,dc=com
objectClass: organizationalUnit
ou: Ti
```
Aqui criei duas unidades organizacionais para cada organization.
```shell
ldapadd -x -W -D "cn=ldap_admin,dc=corp,dc=com" -f organizational_unit.ldif
```
![[Pasted image 20250204113737.png]]
##### account.ldif
```ldif
dn: uid=nycolas.ramos,ou=Ti,o=filial-DF,c=BR,dc=corp,dc=com
objectClass: top
objectClass: account
objectClass: posixAccount
objectClass: shadowAccount
cn: nycolas.ramos
uid: nycolas.ramos
uidNumber: 1100
gidNumber: 1100
homeDirectory: /home/nycolas.ramos
loginShell: /bin/bash
gecos: User: Nycolas Ramos (Ti)
userPassword: {crypt}x

dn: uid=davi.ramos,ou=Marketing,o=filial-DF,c=BR,dc=corp,dc=com
objectClass: top
objectClass: account
objectClass: posixAccount
objectClass: shadowAccount
cn: davi.ramos
uid: davi.ramos
uidNumber: 1101
gidNumber: 1101
homeDirectory: /home/davi.ramos
loginShell: /bin/bash
gecos: User: Davi Ramos (Marketing)
userPassword: {crypt}x


dn: uid=eryck.cardoso,ou=Ti,o=filial-SP,c=BR,dc=corp,dc=com
objectClass: top
objectClass: account
objectClass: posixAccount
objectClass: shadowAccount
cn: eryck.cardoso
uid: eryck.cardoso
uidNumber: 1102
gidNumber: 1102
homeDirectory: /home/eryck.cardoso
loginShell: /bin/bash
gecos: User: Eryck Carsoso (Ti)
userPassword: {crypt}x

dn: uid=marcos.cardoso,ou=Marketing,o=filial-SP,c=BR,dc=corp,dc=com
objectClass: top
objectClass: account
objectClass: posixAccount
objectClass: shadowAccount
cn: marcos.cardoso
uid: marcos.cardoso
uidNumber: 1103
gidNumber: 1103
homeDirectory: /home/marcos.cardoso
loginShell: /bin/bash
gecos: User: Marcos Cardoso (Marketing)
userPassword: {crypt}x
```
Aqui fiz a criação de 4 usuários, um para cada ``ou``, o ``{crypt}x`` indica que a senha sera setada depois.
```shell
ldapadd -x -W -D "cn=ldap_admin,dc=corp,dc=com" -f account.ldif
```
![[Pasted image 20250204113829.png]]
##### Mudando as senhas
```shell
ldappasswd -S -W -D "cn=ldap_admin,dc=corp,dc=com" -x "uid=nycolas.ramos,ou=Ti,o=filial-DF,c=BR,dc=corp,dc=com"

ldappasswd -S -W -D "cn=ldap_admin,dc=corp,dc=com" -x "uid=davi.ramos,ou=Marketing,o=filial-DF,c=BR,dc=corp,dc=com"

ldappasswd -S -W -D "cn=ldap_admin,dc=corp,dc=com" -x "uid=eryck.cardoso,ou=Ti,o=filial-SP,c=BR,dc=corp,dc=com"

ldappasswd -S -W -D "cn=ldap_admin,dc=corp,dc=com" -x "uid=marcos.cardoso,ou=Marketing,o=filial-SP,c=BR,dc=corp,dc=com"
```
##### Fazendo buscas
```shell
ldapsearch -H ldapi:/// -x uid=*.cardoso -b "dc=corp,dc=com"
```
![[Pasted image 20250204133134.png]]

```shell
ldapsearch -H ldapi:/// -x o=* -b "c=BR,dc=corp,dc=com"
```
![[Pasted image 20250204133552.png]]

---
## Client
##### Pacotes
```shell
dnf update -y

dnf install openldap-clients sssd sssd-ldap -y

dnf install oddjob-mkhomedir -y
systemctl enable oddjob-mkhomedir
systemctl start oddjob-mkhomedir

authselect select sssd with-mkhomedir
```
Provavelmente alguns pacotes são instalados por padrão, mas esses são os pacotes necessários. Após a instalação dos pacotes, algumas configurações já serão setadas automaticamente, mas irei percorrer para mostrar como elas devem estart.
##### /etc/openldap/ldap.conf
```conf
URI ldap://ldap.corp.com
BASE dc=corp,dc=com
```

##### Pesquisa
```shell
ldapsearch -h 172.29.21.221 -x o=* -b "dc=corp,dc=com"
```
![[Pasted image 20250204134940.png]]
Fazemos essa pesquisa para ver se o `ldap` está encontrando o servidor.

#### Definindo o mecanismo de autenticação
```sh
authselect select sssd with-mkhomedir
```
Aqui definimos que usaremos o ``sss`` para autenticação, com o ``mkhomedir``, que irá criar o diretório ``home`` caso o usuário não tenha.
##### /etc/nsswitch.conf
Esse arquivo indica qual a sequência que ele deve tentar autenticar.
```conf
passwd:     files sss systemd
group:      files sss systemd
netgroup:   sss files
automount:  sss files
services:   sss files
```
Essas diretivas devem ter o sss.

```shell
cat /etc/pam.d/system-auth
```
![[Pasted image 20250205094044.png]]
Todas essas configurações foram criadas automaticamente.

#### Configurações do sssd
```conf
[sssd]
services = nss, pam
config_file_version = 2
domains = LDAP

[domain/LDAP]
id_provider = ldap
auth_provider = ldap
chpass_provider = ldap

ldap_uri = ldap://172.29.21.221
ldap_search_base = dc=corp,dc=com

ldap_default_bind_dn = cn=ldap_admin,dc=corp,dc=com ldap_default_authtok = P@ssw0rd

ldap_schema = rfc2307bis

ldap_tls_reqcert = never
ldap_id_use_start_tls = True

access_provider = simple
simple_allow_users = nycolas.ramos

cache_credentials = true
enumerate = true
```
Com essa configuração, embora não usemos o ``ldaps``, nossa comunicação será criptografada. E somente o usuário ``nycolas.ramos`` poderá autenticar.

Embora eu não tenha colocado, algumas opções podem ser uteis:
```conf
[domain/LDAP]
# Atributos de usuários e grupos
ldap_user_search_base = ou=users,dc=exemplo,dc=com
ldap_group_search_base = ou=groups,dc=exemplo,dc=com

# Atributos POSIX
ldap_user_name = uid
ldap_group_name = cn
ldap_uid_number = uidNumber
ldap_gid_number = gidNumber
ldap_home_directory = homeDirectory
ldap_login_shell = loginShell
ldap_gecos = gecos
ldap_group_members = memberUid

```

##### Bloqueando usuários ou grupos
```conf
[domain/LDAP]
allow_users = user1, user2
deny_users = user3, user4

simple_allow_groups = allowed_users
simple_deny_groups = denied_users
```

##### Permissões
```shell
sudo chmod 600 /etc/sssd/sssd.conf
sudo chown root:root /etc/sssd/sssd.conf
```