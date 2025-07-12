## Remote logging
### Server  - 10.0.10.10
#### Instalação
```sh
$ dnf update -y
$ dnf install rsyslog -y
$ systemctl enable --now rsyslog
```
#### Recepção de logs
```sh
$ vim /etc/rsyslog.conf
module(load="imtcp") # TCP
input(type="imtcp" port="514") # TCP
# ou
module(load="imudp") # UDP
input(type="imudp" port="514") # UDP
```
#### Permissão do firewall
```sh
$ firewall-cmd --permanent --add-port=514/tcp # TCP
# ou
$ firewall-cmd --permanent --add-port=514/udp # UDP

$ firewall-cmd --reload
```
#### Organizar os logs
```sh
$ vim /etc/rsyslog.d/01-template.conf
$template RemoteLogs,"/var/log/remote/%HOSTNAME%/%PROGRAMNAME%.log"
if $fromhost-ip != '127.0.0.1' then ?RemoteLogs
& ~
```
##### Opções
- `%HOSTNAME%`: pode ser substituído por `%FROMHOST-IP%` se quiser usar IPs em vez de nomes.
- `%PROGRAMNAME%`: depende do que é informado no campo `programname` do syslog.
- Outros tokens possíveis: `%syslogfacility-text%`, `%msg%`, `%syslogseverity-text%`, etc.
#### SELinux
```sh
$ semanage fcontext -a -t var_log_t "/var/log/remote(/.*)?"
$ restorecon -Rv /var/log/remote
```
***
### Client - 10.0.10.5
```sh
$ dnf update -y
$ dnf install rsyslog -y
$ systemctl enable --now rsyslog
```
#### Envio do slogs
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
## Logrotate
Queremos que o sistema mantenha apenas 7 dias de log.
```sh
$ vim /etc/systemd/journald.conf
[Journal]
Storage=persistent
MaxRetentionSec=7day

$ systemctl restart systemd-journald
```

Aqui nós rotacionamos os logs diariamente, renomeamos o arquivo com a data e mantemos 30 dias de histórico
```sh
$ vim /etc/logrotate.d/limitar-logs
/var/log/*
/var/log/*/*
{
    daily
    dateext
    dateformat -%Y%m%d
    rotate 30
    maxage 30
    compress
    missingok
    notifempty
    create
    olddir /old_logs/
}
```

```sh
Faça um teste **sem aplicar mudanças**:
$ logrotate /etc/logrotate.d/limitar-logs --debug

Para aplicar manualmente:
$ logrotate -f /etc/logrotate.d/limitar-logs

```

***
$$\#54 : Nycolas Ramos$$

Configurar o sistema para deixar apenas 7 dias de logs e Configurar uma rotação diária de logs comprimidos para um mês de logs, usando arquivos nomeados com a data. Tudo no logrotate