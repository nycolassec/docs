## Conexão
#### Server
```powershell
# Talvez precise mudar o perfil da interface 
Set-NetConnectionProfile -InterfaceAlias "Ethernet" -NetworkCategory Private

# Habilitando acesso remoto
Enable-PSRemoting -Force
winrm quickconfig
Set-Item WSMan:\localhost\Client\TrustedHosts -Value * -Force
Enable-WSManCredSSP -Role Server

# Adicione o usuário que será usado nos grupos a seguir
Add-LocalGroupMember -Group "Hyper-V Administrators" -Member "Administrator"
Add-LocalGroupMember -Group "Remote Management Users" -Member "Administrator"
```
#### Client
```powershell
# Talvez precise mudar o perfil da interface 
Set-NetConnectionProfile -InterfaceAlias "Ethernet" -NetworkCategory Private

# Adicione o host Hyper‑V aos hosts confiáveis do WinRM
Start-Service WinRM
Set-Item WSMan:\localhost\Client\TrustedHosts -Value "10.0.10.5"
Stop-Service WinRM

# Carregue as credenciais no cliente
cmdkey /add:10.0.10.5 /user:Administrator /pass:AdministratorPass
runas /user:10.0.10.5\Administrator /netonly "mmc virtmgmt.msc"

```

***
## Transferência
#### Server
```powershell
Enter-PSSession -ComputerName 10.0.10.5 -Credential Administrator

New-Item -ItemType Directory -Path "C:\Users\Administrator\Documents\isos"

New-SmbShare -Name isos -Path "C:\Users\Administrator\Documents\isos\" -FullAccess "Everyone"

netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
```
#### Client
```powershell
net use Z: \\10.0.10.5\isos /user:10.0.10.5\Administrator AdministratorPass
```
***
$$\#54 : Nycolas Ramos$$