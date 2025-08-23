## Configuração
Podemos criar nosso arquivo de configuração em `/etc/nginx/conf.d/` que ele já será carregado automaticamente, seu nome deve terminar com `.conf`.
###### Base funcional
Esse é o mínimo para que o `nginx` possa executar seu site. Toda configuração deve ser feita dentro do objeto `server{}`
```nginx
server {
    listen 80;
    server_name www.wsc.local;
    root /var/www/www.wsc.local;
    index index.html index.htm;
}
```
###### Permissões de acesso
Permite ou nega acesso de acordo com alguns critérios
```nginx
# Permitir acesso a todos (padrão)
allow all;

# Negar acesso a todos
deny all;

# Permitir apenas de IPs específicos
allow 10.0.10.0/24;
deny all;
```
###### SSL com redirecionamento
Não precisamos do redirecionamento para acessar o `https`, mas isso força a sua utilização.
```nginx
server {
    listen 80;
    server_name www.wsc.local;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl;
    server_name www.wsc.local;
    root /var/www/www.wsc.local;

    # TLS
    ssl_certificate /root/PKI/certs/www.wsc.local.cert.pem;
    ssl_certificate_key /root/PKI/key/www.wsc.local.key.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
}
```
###### Habilitar o HSTS (HTTP Strict Transport Security)
Podemos remover o "**includeSubDomains**" caso tenhamos subdomínios que não terão certificados válidos.
```nginx
server {
    listen 80;
    server_name www.wsc.local;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl;
    server_name www.wsc.local;
    root /var/www/www.wsc.locall;

    # TLS
    ssl_certificate /root/PKI/certs/www.wsc.local.cert.pem;
    ssl_certificate_key /root/PKI/key/www.wsc.local.key.pem;
    ssl_protocols TLSv1.2 TLSv1.3;

    # HSTS
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
}
```
###### Listagem de diretórios
A listagem é controlada pela diretiva `autoindex`.
```nginx
# Desativar listagem (padrão)
autoindex off;

# Ativar listagem
location /downloads {
	index nothing_here;
	autoindex on;
}
```
###### Negar acesso a arquivos pela extensão
Aqui negamos o acesso a qualquer arquivo que termine com `.bak`.
```nginx
location ~ \.bak$ {
    deny all;
    return 403;
}
```
- `~*` case insensitive
- `~` case sensitive
###### Mensagens de erros HTTP
A diretiva `internal;` impede o acesso pela `url`
```nginx
# Páginas de erro personalizadas
error_page 404 /404.html;
error_page 500 502 503 504 /50x.html;

location = /404.html {
    internal;                    # Impede o acesso pela URL  
    root /var/www/www.wsc.local; # Onde encontrar o arquivo
}

location = /50x.html {
    internal;
    root /var/www/www.wsc.local;
}
```
###### Autenticação .htpasswd
```sh
# Instalar utilitário (mesmo do Apache)
$ sudo yum install httpd-tools    # CentOS/RHEL

# Criar arquivo de senhas
$ sudo htpasswd -c /etc/nginx/.htpasswd nycolas
# Atualizar a senha do usuário
$ sudo htpasswd /etc/nginx/.htpasswd nycolas
```

```nginx
# Configuração de autenticação
location /restrito {
    auth_basic "Área Restrita";
    auth_basic_user_file /etc/nginx/.htpasswd;
    
    try_files $uri $uri/ /index.html;
}
```

> [!danger] Diferenças do Apache  
> Lembre-se que o NGINX não suporta arquivos .htaccess - todas as configurações devem estar nos arquivos de configuração principais
###### Modificar o usuário do nginx
```sh
# Criar usuário
$ sudo useradd --system --no-create-home --shell /sbin/nologin webuser

# Modificar configuração principal
$ sudo vim /etc/nginx/nginx.conf
user webuser webuser;

# Alterar proprietário dos arquivos
$ sudo chown -R webuser:webuser /var/www
$ sudo chmod -R 755 /var/www
```
###### Logs
```nginx
access_log /var/log/nginx/www.wsc.local-access.log;
error_log /var/log/nginx/www.wsc.local-error.log;
```

> [!warning] Autoindex  
> Evite usar `autoindex on`, pois isso permite que atacantes vejam arquivos e pastas

***
$$\#54 : Nycolas Ramos$$