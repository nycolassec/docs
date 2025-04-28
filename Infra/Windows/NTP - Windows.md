
#### Client
```powershell
net stop w32time

# Configurar o servidor NTP externo
w32tm /config /syncfromflags:manual /manualpeerlist:"IP_do_seu_servidor_linux"

# Definir como servidor confiável (para PDC Emulator)
w32tm /config /reliable:yes

# Reiniciar o serviço
net start w32time

# Forçar sincronização imediata
w32tm /resync

# Verificar status
w32tm /query /status
w32tm /query /configuration
```