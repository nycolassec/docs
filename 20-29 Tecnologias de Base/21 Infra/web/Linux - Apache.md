Podemos criar nosso arquivo de configuração em `/etc/httpd/conf.d/` que ele já será carregado automaticamente, seu nome deve terminar com `.conf`.
## Modificando site padrão
Modificamos o nome para que ele não seja carregado quando não houver um arquivo index.
```sh
mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.conf.bak
$ vim /etc/httpd/conf.d/www.wsc.local.conf
```
## Configurações
###### Base funcional
Esse é o mínimo para que o apache possa executar seu site. Toda configuração deve ser feita dentro de uma tag `<VirtualHost>` e `</VirtualHost>`.
```xml
<VirtualHost *:80>
	ServerName www.wsc.local
	DocumentRoot /var/www/www.wsc.local
</VirtualHost>
```
###### Configurações do diretório
`Options FollowSymLinks` permite que o apache siga links simbólicos e o `AllowOverride All` permite o **.htaccess** sobrescrever a configuração do Apache, pode ser `none.`
```xml
<Directory /var/www/www.wsc.local>
	Options FollowSymLinks
    AllowOverride All           # Qualquer configuração será sobrescrita
    Require all granted         # Permite acesso a todos
</Directory>
```
###### Require
Permite ou nega acesso de acordo com alguns critérios
```md
- Require all denied → Acesso negado a todos
- Require all granted → Acesso permitido a todos
- Require valid-user → Apenas usuários autenticados  
- Require user joao maria → Apenas usuários específicos
- Require ip 10.0.10.0/24 → Apenas de IPs específicos
```
###### SSL com redirecionamento
Não precisamos do redirecionamento para acessar o `https`, mas isso força a sua utilização.
```xml
<VirtualHost *:80>
    ServerName www.wsc.local
    Redirect permanent / https://www.wsc.local/ # Redirecionamento
</VirtualHost>

<VirtualHost *:443>
    ServerName www.wsc.local
    DocumentRoot /var/www/www.wsc.local

	# TLS
    SSLEngine on
    SSLCertificateFile /root/PKI/certs/www.wsc.local.cert.pem
    SSLCertificateKeyFile /root/PKI/key/www.wsc.local.key.pem
</VirtualHost>
```
###### Habilitar o HSTS (HTTP Strict Transport Security)
Podemos remover o "**includeSubDomains**" caso tenhamos subdomínios que não terão certificados válidos.
```xml
<VirtualHost *:80>
    ServerName www.wsc.local
    Redirect permanent / https://www.wsc.local/ # Redirecionamento
</VirtualHost>

<VirtualHost *:443>
    ServerName www.wsc.local
    DocumentRoot /var/www/www.wsc.local

	# TLS
    SSLEngine on
    SSLCertificateFile /root/PKI/certs/www.wsc.local.cert.pem
    SSLCertificateKeyFile /root/PKI/key/www.wsc.local.key.pem

    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
</VirtualHost>
```
###### Apenas listagem
Podemos pedir ao Apache para que não renderize o conteúdo da pasta, somente mostre. É útil quando queremos listar conteúdo do FTP.
```xml
<Directory /var/www/www.wsc.local>
	DirectoryIndex disabled # Não indexa arquivos
</Directory>
```
###### Negar acesso a arquivos pela extensão
Aqui negamos o acesso a qualquer arquivo que termine com `.bak`, pode ser colocado dentro de `.htaccess` se `AllowOverride` permitir.
```xml
<FilesMatch "\.bak$">
    Require all denied
</FilesMatch>
```
###### Mensagem para erros http
Para especificarmos um arquivo partimos do root do site( **/** ), para especificar uma mensagem direto no arquivo de configuração colocamos somente a mensagem.
```xml
<VirtualHost *:80>
        ServerName www.wsc.local
        DocumentRoot /var/www/www.wsc.local
        ErrorDocument 403 /error.html
        <FilesMatch "\.bak$">
                Require all denied
        </FilesMatch>
</VirtualHost>
```
###### Autenticação .htpasswd
```sh
# Instalamos o utilitário
$ dnf install httpd-tools

# Agora criamos um arquivo de senhas
$ htpasswd -c /var/www/www.wsc.local/.htpasswd nycolas
New password: pwd123
Re-type new password: pwd123

# Atualizar a senha do usuário
$ htpasswd /var/www/www.wsc.local/.htpasswd nycolas
New password: P@ssw0rd
Re-type new password: P@ssw0rd
```

```xml
<VirtualHost *:80>
    ServerName www.wsc.local
    DocumentRoot /var/www/www.wsc.local

    <Directory /var/www/www.wsc.local>
        AllowOverride AuthConfig # utiliza a autenticação definida no .htaccess
    </Directory>
</VirtualHost>
```

```sh
$ vim /var/www/www.wsc.local/.htaccess
AuthType Basic
AuthName "Área Restrita"
AuthUserFile /var/www/www.wsc.local/.htpasswd
Require valid-user
```
###### Modificar para outro usuário
```sh
$ useradd --system --no-create-home --shell /sbin/nologin webuser

$ vim /etc/httpd/conf/httpd.conf
User webuser
Group webuser

$ chown -R webuser:webuser /var/www
$ chmod -R 755 /var/www

$ ps aux | grep httpd
```
###### Logs
Onde os logs vão ser armazenados
```xml
ErrorLog /var/log/httpd/files.wsc.local-error.log
CustomLog /var/log/httpd/files.wsc.local-access.log combined
```

>[!warning] Indexes
> Evite usar `Options Indexes`, pois isso permite que atacantes vejam arquivos e pastas

***
$$\#54 : Nycolas Ramos$$