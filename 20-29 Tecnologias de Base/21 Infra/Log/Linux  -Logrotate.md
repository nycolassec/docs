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
Faça um teste sem aplicar mudanças:
$ logrotate /etc/logrotate.d/limitar-logs --debug

Para aplicar manualmente:
$ logrotate -f /etc/logrotate.d/limitar-logs
```