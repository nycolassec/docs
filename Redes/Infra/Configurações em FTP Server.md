#### Credenciais FTP
```IOS
IOS# configure terminal

IOS(config)# ip ftp username cisco
IOS(config)# ip ftp password cisco
IOS(config)# end

IOS# wr
```

#### Copiando as configurações
```IOS
IOS# configure terminal
IOS# copy running-config ftp:

Address or name of remote host []? 192.168.0.8
Destination filename [IOS-confg]? ios-conf

IOS# wr
```

## Referências

CISCO - https://www.cisco.com/c/pt_br/support/docs/ios-nx-os-software/ios-software-releases-122-mainline/46741-backup-config.html
