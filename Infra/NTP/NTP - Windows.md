

```powershell
net stop w32time

# Configurar o servidor NTP externo
w32tm /config /manualpeerlist:"[SERVER IP]" /reliable:yes /update

# Reiniciar o serviço
net start w32time

# conferir os peers sincronizados
w32tm /query /peers

# Forçar sincronização imediata
w32tm /resync

# Verificar status
w32tm /query /status
w32tm /query /configuration
```

###### Caso o domínio esteja criado, devemos adicionar a flag "syncfromflags"
```powershell
w32tm /config /manualpeerlist:10.128.24.2 /reliable:yes /syncfromflags:manual /update

w32tm /resync
```

***
$$\#54 : Nycolas Ramos$$