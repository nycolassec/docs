#Difficult : Easy
#Tools : EvtxECmd, Time Line Explorer
***
#### Parse
![[Pasted image 20250317151144.png]]
Os arquivos fornecidos são logs do sistema **Windows**, podemos analisa-los com o **Event Viewer** do Windows ou podemos usar alguma ferramenta externa, que nesse caso será **Time Line Explorer** do Eric Zimmerman.

Primeiro converto os arquivos de Eventos do Windows em um CSV com a ferramenta **EvtxECmd**.
```powershell
PS C:\Users\Forense> .\Downloads\net6\EvtxeCmd\EvtxECmd.exe -d .\Documents\Event-Logs\ --csv Documents\Event-Logs\ --csvf Documents\Event-Logs\All.csv
```

Após abrir o Time Line Explorer e importar o arquivo **All.csv**, podemos começar a análise.
![[Pasted image 20250317170047.png]]
***
#### 1 - When did the cyberjunkie user first successfully log into his computer? (UTC)
Aqui podemos filtrar pelo ID d evento **4648**(indica que um logon foi realizado com credenciais explícitas), e pelo nome de usuário **cyberjunkie**.

![[Pasted image 20250317191445.png]]
Então agora colocamos a resposta em UTC.

**Answer:** 27/03/2023 14:37:09

> [! ] Disclaimer
>A partir da próxima questão, todas as pesquisas terão o filtro da data setadas para a data que descobrimos na questão 1 (**27/03/2023**), pois só estamos analisando os ocorridos após o login do usuário.

***
#### 2 - The user tampered with firewall settings on the system. Analyze the firewall event logs to find out the Name of the firewall rule added?
Aqui podemos filtrar pelo ID de evento **2004**(que indica a criação de uma nova regra de firewall). Podemos notar o nome de uma ferramenta normalmente usada por atores de ameaça.

![[Pasted image 20250317192040.png]]

**Answer:** Metasploit C2 Bypass
***
##### 3 - Whats the direction of the firewall rule?
Aqui ainda no mesmo ID de evento podemos somente observer o campo de **Payload Data 3**.

![[Pasted image 20250317192955.png]]

**Answer:** Outbound
***
#### 4 - The user changed audit policy of the computer. Whats the Subcategory of this changed policy?
Aqui podemos filtrar pelo ID de evento**4719**(que indica a modificação de uma política de auditoria).
![[Pasted image 20250317201936.png]]
Por padrão ele não mostra o nome da sub-categoria, mas podemos pesquisar na internet pela sua GUID.

![[Pasted image 20250318081301.png]]

![[Pasted image 20250317202016.png]]

**Answer:** Other Object Access Events
***
#### 5 - The user "cyberjunkie" created a scheduled task. Whats the name of this task?
Aqui podemos filtrar pelo ID de evento **4698**(Indica a criação de uma scheduled task), e observar o payload Data1.

![[Pasted image 20250318082120.png]]

**Answer:** HTB-AUTOMATION
***
##### 6 - Whats the full path of the file which was scheduled for the task?
Ainda no mesmo evento podemos achar o nome do arquivo no **Payload Data2**.

![[Pasted image 20250318082438.png]]

**Answer:** C:\Users\CyberJunkie\Desktop\Automation-HTB.ps1
***
##### 7 - What are the arguments of the command?
Ainda no mesmo evento podemos achar os argumentos do arquivo no **Payload Data2**.

![[Pasted image 20250318082438.png]]

**Answer:** -A cyberjunkie\@hackthebox.eu
***
##### 8 - The antivirus running on the system identified a threat and performed actions on it. Which tool was identified as malware by antivirus?
Podemos observar no ID de evento **1116**(Indica a detecção de um Malware.

![[Pasted image 20250318082915.png]]

**Answer:** Sharphound
***
##### 9 - Whats the full path of the malware which raised the alert?
Ainda no mesmo evento, podemos ver o campo **Executable Info**.

![[Pasted image 20250318083440.png]]

**Answer:** C:\Users\CyberJunkie\Downloads\SharpHound-v1.1.0.zip
***
##### 10 - What action was taken by the antivirus?
Aqui podemos filtra pelo ID de evento **1117**(que indica que a plataforma de anti-malware performou alguma ação contra uma ameaça), e observar o campo **Payload**.

![[Pasted image 20250318083830.png]]

**Answer:** Quarantine
***
##### 11 - The user used Powershell to execute commands. What command was executed by the user?
Aqui podemos filtra pelo ID de evento **4104**(indica a execução de algum script), e olharmos o campo **Payload Data2**.

![[Pasted image 20250318084251.png]]

**Answer:** Get-FileHash -Algorithm md5 .\Desktop\Automation-HTB.ps1
***
##### 12 - We suspect the user deleted some event logs. Which Event log file was cleared?
Aqui podemos filtrar pelo ID de evento **104**(Indica a limpeza dos logs de auditoria), e olhar o campo **Payload**.

![[Pasted image 20250318085059.png]]

**Answer:** Microsoft-Windows-Windows Firewall With Advanced Security/Firewall
***

![[Pasted image 20250318085655.png]]


