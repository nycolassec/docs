#Difficult : Medium
#OS : Windows
***
#### 1 - What is the the web messaging app the employee used to talk to the attacker?
Podemos começar analisando o arquivo que guarda as notificações dos usuários em `challenge\Users\OMEN\AppData\Local\Microsoft\Windows\Notifications\`, o arquivo que armazena deve terminar com `.db`. Podemos analisa-lo com `DB Browser for SQLite` e olharmos a tabela `Notification`.

No registro `20`, temos o único aplicativo de mensagens desse banco de dados.
![[Pasted image 20250414194754.png]]

**Answer : `telegram`**
***
#### 2 - What is the password for the protected ZIP file sent by the attacker to the employee?
No mesmo registro, na visualização `text`, podemos ver a senha usada.
![[Pasted image 20250414195035.png]]

Podemos validar tentando extrair o arquivo `project templet test.zip`
![[Pasted image 20250414195558.png]]

**Answer : `@1122d`**
***
#### 3 - What domain did the attacker use to download the second stage of the malware?
Após a extração obtivemos 2 arquivos, sendo um deles, um ``link``, que podemos passar pelo `LECmd`. Vou copiar o arquivo de `link` par o diretório `Documents` apenas para facilitar o comando.

Temos várias informações interessantes, mas o que me chamou a atenção foi o `Arguments`, que mostra o comando que foi executado. Podemos identificar o domínio.
```powershell
.\LECmd.exe -f ..\..\..\templet.lnk
```
![[Pasted image 20250414200815.png]]

Usei o `neovim` para deixar o arquivo mais legível.![[Pasted image 20250414201846.png]]

Não sou um especialista em `PowerShell` mas analisando um pouco, vejo que tem um `wget` que faz uma requisição no domínio especificado em `$NpzibtULgyi`. Também utilizei da [análise](https://0xtoxin.github.io/malware%20analysis/KrakenKeylogger-pt1/?source=post_page-----1fc0ef5e283b---------------------------------------#powershell-script) de `0xtoxin` para identificar que a `URL` completa seria `https://masherofmasters.cyou/chin/se1.hta`.

**Answer : `masherofmasters.cyou`**
***
#### 4- What is the name of the command that the attacker injected using one of the installed LOLAPPS on the machine to achieve persistence?
Em `challenge\Users\OMEN\AppData\Roaming` temos a pasta de um aplicativo não comum, o `Greenshot`. Pesquisei por ele em [lolapps-project](https://lolapps-project.github.io/#)para ver se encontrava algo.

Pelo que me parece, se pode modificar o arquivo de inicialização do `Greenhorn` para obter persistência utilizando o comando `CommandLine`.
![[Pasted image 20250414204723.png]]

Então eu filtro o arquivo `challenge\Users\OMEN\AppData\Roaming\Greenshot\Greenshot.ini` pela string`CommandLine`.
```powershell
Select-String challenge\Users\OMEN\AppData\Roaming\Greenshot\Greenshot.ini -Pattern CommandLine
```
![[Pasted image 20250414205316.png]]

**Answer : `jlhgfjhdflghjhuhuh`**
***
#### 5 - What is the complete path of the malicious file that the attacker used to achieve persistence?
Podemos filtrar pelo comando que achamos na questão anterior, para achar o arquivo usado para manter persistência.

```powershell
Select-String challenge\Users\OMEN\AppData\Roaming\Greenshot\Greenshot.ini -Pattern jlhgfjhdflghjhuhuh
```
![[Pasted image 20250414205911.png]]

**Answer : `C:\Users\OMEN\AppData\Local\Temp\templet.lnk`**
***
#### 6 - What is the name of the application the attacker utilized for data exfiltration?
Aqui fiz uma breve pesquisa no ``MITRE`` sobre técnicas de `c2` para exfiltração com software de acesso remoto.

Aqui ele deu alguns exemplos, mas o único que se adequa nos artefatos é o `Anydesk`. 
![[Pasted image 20250415074619.png]]

**Answer : `anydesk`**
***
#### 7 - What is the IP address of the attacker?
Aqui podemos ver o que parece ser o seu arquivo de logs do `Anydesk` em `challenge\Users\OMEN\AppData\Roaming\AnyDesk\ad.trace`, para tentar identificar o `IP` do atacante.
![[Pasted image 20250415074842.png]]

Então abri o arquivo `ad.trace` no `Timeline Explorer` e filtrei por logged. Então encontrei o `IP` que fez a conexão.
![[Pasted image 20250415080226.png]]

**Answer : `77.232.122.31`**
***

![[Pasted image 20250415080335.png]]