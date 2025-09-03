- [ ] Client  : 10.0.10.10
- [ ] Server : 10.0.10.5
***
## Server
#### Instalação
Devemos instalar o `rsyslog` e validar se o serviço está operando.
```sh
$ dnf update -y
$ dnf install rsyslog -y
$ systemctl enable --now rsyslog
$ systemctl status rsyslog
```
#### SELinux
Qualquer pasta ou arquivo que será usado pelo `rsyslog` deve estar nesse contexto. Nesse caso iremos usar a pasta `/var/log/remote` para armazenar os logs remotos.
```sh
$ semanage fcontext -a -t var_log_t "/var/log/remote(/.*)?"
$ restorecon -Rv /var/log/remote
```
#### Permissão do firewall
Devemos permitir tráfego para a porta escolhida na configuração do `rsyslog`.
```sh
$ firewall-cmd --add-port=514/tcp --permanent # TCP
# ou
$ firewall-cmd --add-port=514/udp --permanent # UDP

$ firewall-cmd --reload
```
#### Recepção de logs
Podemos receber os logs via **UDP** ou via **TCP**, isso definimos habilitando o módulo `imtcp`(**TCP**) ou `imudp`(**UDP**).
```sh
$ vim /etc/rsyslog.conf
module(load="imtcp") # TCP
input(type="imtcp" port="514") # TCP
# ou
module(load="imudp") # UDP
input(type="imudp" port="514") # UDP
```
#### Organizar os logs
O `rsyslog` dispõe de vários campos para identificação do log. Nesse exemplo de configuração pegamos os logs que não são do host local (**`$fromhost-ip != '127.0.0.1'`**), tem o campo que identifica o programa iniciando com `httpd`(**`$programname startswith 'httpd'`**) e então enviamos para um arquivo nomeado com o IP do host e o valor do campo que identifica o programa (**`type="string" string="/var/log/remote/%FROMHOST-IP%/%PROGRAMNAME%.log"`**)
```sh
$ vim /etc/rsyslog.d/01-Apache.conf
# Defina o nome e onde será salvo os logs do template
template(name="ApacheLogs", type="string" string="/var/log/remote/%FROMHOST-IP%/%PROGRAMNAME%.log")

# Filtro para logs do Apache de hosts remotos
if $fromhost-ip != '127.0.0.1' and $programname startswith 'httpd' then{
	action(type="omfile" dynaFile="ApacheLogs")
	stop
}
```
###### Opções
- `%FROMHOST-IP%` : IP do host que enviou o log.
- `%PROGRAMNAME%`: Depende do que é informado no campo `programname` do syslog.
- `%syslogfacility-text%` : Facilidade do log.
- `%syslogseverity-text%` :  Severidade do log.
- `%msg%` : Mensagem contida no log.

###### Validar as configurações sem reiniciar o serviço
```sh
$ rsyslogd -N 1 -f /etc/rsyslog.d/01-Apache.conf
```

> [!tip] Para ter mais noções sobre os filtros vale apena ler a [documentação oficial](https://www.rsyslog.com/doc/configuration/filters.html) do `RSyslog`.

***
## Client
#### Instalação
Devemos instalar o `rsyslog` e validar se o serviço está operando.
```sh
$ dnf update -y
$ dnf install rsyslog -y
$ systemctl enable --now rsyslog
$ systemctl status rsyslog
```
#### Envio do logs
```sh
$ vim /etc/rsyslog.d/50-remote.conf
*.* @10.0.10.10:514 # UDP
# ou
*.* @@10.0.10.10:514 # TCP
```
#### Teste do envio
```sh
$ logger "Teste de envio de log para o servidor rsyslog central"
$ tail /var/log/messages
```

***
***
$$\#54 : Nycolas\ Ramos$$