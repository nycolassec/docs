Podemos criar nosso arquivo de configuração em `/etc/httpd/conf.d/` que ele já será carregado automaticamente.
#### Modificando site padrão
Modificamos o nome para que ele não seja carregado quando não houver um arquivo index.
```sh
mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.conf.bak
```
#### Base funcional
```sh
$ vim /etc/httpd/conf.d/www.wsc.local.conf
<VirtualHost *:80>
	# Nome que corresponderá
    ServerName www.wsc.local
    
    # Pasta de onde os arquivos serão servidos
    DocumentRoot /var/www/www.wsc.local

	# Configuração da pasta
    <Directory /var/www/www.wsc.local>
		Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

	# Logs
    ErrorLog /var/log/httpd/files.wsc.local-error.log
    CustomLog /var/log/httpd/files.wsc.local-access.log combined
</VirtualHost>
```

>[!warning] Indexes
> Evite usar `Options Indexes`, pois isso permite que atacantes vejam arquivos e pastas
#### SSL com redirecionamento

```sh
$ vim /etc/httpd/conf.d/www.wsc.local.conf
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

    <Directory /var/www/www.wsc.local>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog /var/log/httpd/files.wsc.local-error.log
    CustomLog /var/log/httpd/files.wsc.local-access.log combined
</VirtualHost>
```
#### Apenas listagem
Podemos pedir ao Apache para que não renderize o conteúdo da pasta, somente mostre. É útil quando queremos listar conteúdo do FTP.
```sh
$ vim /etc/httpd/conf.d/www.wsc.local.conf
<VirtualHost *:80>
    # Nome que corresponderá
    ServerName www.wsc.local

    # Pasta de onde os arquivos serão servidos
    DocumentRoot /var/www/www.wsc.local

    # Configuração da pasta
    <Directory /var/www/www.wsc.local>
        Options Indexes FollowSymLinks
        DirectoryIndex disabled
        AllowOverride All
        Require all granted
    </Directory>

    # Logs
    ErrorLog /var/log/httpd/files.wsc.local-error.log
    CustomLog /var/log/httpd/files.wsc.local-access.log combined
</VirtualHost>
```
#### Autenticação .htaccess
```sh
# Instalamos o utilitário
$ dnf install httpd-tools

# Agora criamos um arquivo de senhas
$ htpasswd -c /var/www/www.wsc.local/.htpasswd usuario
New password: pwd123
Re-type new password: pwd123

$ htpasswd /var/www/www.wsc.local/.htpasswd nycolas
New password: P@ssw0rd
Re-type new password: P@ssw0rd
```

```sh
$ vim /etc/httpd/conf.d/www.wsc.local.conf
<VirtualHost *:80>
    # Nome que corresponderá
    ServerName www.wsc.local

    # Pasta de onde os arquivos serão servidos
    DocumentRoot /var/www/www.wsc.local

    # Configuração da pasta
    <Directory /var/www/www.wsc.local>
        Options FollowSymLinks
        AllowOverride AuthConfig
        Require all granted
    </Directory>

    # Logs
    ErrorLog /var/log/httpd/files.wsc.local-error.log
    CustomLog /var/log/httpd/files.wsc.local-access.log combined
</VirtualHost>
```

```sh
$ vim /var/www/www.wsc.local/.htaccess
AuthType Basic
AuthName "Área Restrita"
AuthUserFile /var/www/www.wsc.local/.htpasswd
Require valid-user
```
#### Modificar para outro usuário
```sh
$ useradd --system --no-create-home --shell /sbin/nologin webuser

$ vim /etc/httpd/conf/httpd.conf
User webuser
Group webuser

$ chown -R webuser:webuser /var/www
$ chmod -R 755 /var/www

$ ps aux | grep httpd
```
#### Negar acesso a arquivos pela extensão
```sh
$ vim /etc/httpd/conf.d/www.wsc.local.conf
<VirtualHost *:80>
    # Nome que corresponderá
    ServerName www.wsc.local

    # Pasta de onde os arquivos serão servidos
    DocumentRoot /var/www/www.wsc.local

    # Configuração da pasta
    <Directory /var/www/www.wsc.local>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    # Logs
    ErrorLog /var/log/httpd/files.wsc.local-error.log
    CustomLog /var/log/httpd/files.wsc.local-access.log combined
</VirtualHost>
```

Pode ser colocado diretamente em `<Directory /var/www/www.wsc.local>...</Directory>`
```sh
$ vim /var/www/www.wsc.local/.htaccess
<FilesMatch "\.bak$">
    Require all denied
</FilesMatch>
```
***
$$\#54 : Nycolas Ramos$$