#Category : Endpoint Forensics #Difficult : Medium #Time :  00:35:10
***
#### Info
Aqui recebemos uma imagem de disco para fazer nossas investigações. Podemos usar ferramentas com o **Autopsy**, **FTK Imager** ou **The Sleuth Kit**. Nesse caso usarei o **Autopsy**
![[Pasted image 20250505173801.png]]
***
#### 1 - What was the malicious executable file name that the user downloaded?
Como foi um arquivo baixado, podemos olhar as pastas **Downloads** dos usuários.
![[Pasted image 20250505174458.png]]
Temos então um arquivo baixado na pasta de **Downloads** do usuário `Public`

**Answer:** `Sysinternals.exe` 
***
#### 2 - When was the last time the malicious executable file was modified?
O tempo indicado no **Autopsy** está errado, podemos então olhar o hive em `c:\Windows\System32\config\SYSTEM`. Primeiro o exportamos, selecionando e indo em `Extract File(s)`.

Agora fazemos o parser com o `AppCompatCacheParser.exe`. Usando o `HIVE` como entrada e uma pasta para a saída.
```powershell
C:\Users\Forense\Documents\tools\Zimmerman\net6\AppCompatCacheParser.exe -f .\SYSTEM --csv .\SYSTEM-CSV\
```

Agora abrimos os arquivo no `TimeLineExplorer` e buscamos por `sysinternals`
![[Pasted image 20250505180721.png]]

**Answer:** `2022-11-15 21:18`
***
#### 3 - What is the SHA1 hash value of the malware?
Por algum motivo, tirar o `SHA1` do arquivo em `Downloads` não funciona, aparentemente ele está corrompido. Podemos então usar o hive em `c:\Windows\AppCompat\Programs\Amcache.hve`, ele armazena informações sobre arquivos executáveis.

Primeiro o exportamos, o selecionando e indo em `Extract File(s)`.

Após isso, usamos o `AmcacheParser` para convertermos para `csv`.
```powershell
C:\Users\Forense\Documents\tools\Zimmerman\net6\AmcacheParser.exe -f .\Amcache.hve --csv .\AMCACHE-CSV\
```

Agora abrimos os arquivos `csv` e procuramos por `sysinternals`.
![[Pasted image 20250505181743.png]]


**Answer:** `fa1002b02fc5551e075ec44bb4ff9cc13d563dcf`
***
#### 4 - Based on the Alibaba vendor, what is the malware's family?
Agora vamos no `VirusTotal` e usamos o hash obtido para fazer a busca. E então podemos ver qual a família do malware.
![[Pasted image 20250505181914.png]]

**Answer:** `rozena`
***
#### 5 - What is the first mapped domain's Fully Qualified Domain Name (FQDN)?
Aqui podemos tentar ver o histórico do `Powershell` no arquivo `c:\Users\IEUser\Roaming\Microsoft\Windows\Powershell\PSReadLine`. Nem sempre teremos esse arquivo, pois ele costuma ser limpo.
![[Pasted image 20250505182520.png]]

Também poderíamos resolver essa questão olhando para o arquivo de hosts em `c:\Windows\System32\drivers\etc\hosts`.


**Answer:** `www.malware430.com`
***
#### 6 - The mapped domain is linked to an IP address. What is that IP address?
Conseguimos ver o `IP` no artefato da questão anterior. Também poderíamos resolver essa questão olhando para o arquivo de hosts em `c:\Windows\System32\drivers\etc\hosts`
![[Pasted image 20250505183642.png]]

**Answer:** `192.168.15.10`
***
#### 7 - What is the name of the executable dropped by the first-stage executable?
No `VirusTotal` em `BEHAVIOR > Process and service actions > Processes created` temos qual programa foi executado após o malware ser ativado, e qual serviço ele inicia.
![[Pasted image 20250505184100.png]]

**Answer:** `vmtoolsIO.exe`
***
#### 8- What is the name of the service installed by 2nd-stage executable?
Ainda em `BEHAVIOR > Process and service actions > Processes created` temos qual serviço é iniciado no segundo estágio.
![[Pasted image 20250505184117.png]]

**Answer:** `VMwareIOHelperService`
***
![[Pasted image 20250505174201.png]]