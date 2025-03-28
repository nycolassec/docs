#Difficult : Easy
#Tools : Wireshark, VirusTotal
***
#### Info
Neste cenário recebemos apenas um arquivo de tráfego de rede.
![[Pasted image 20250326083156.png]]
***
#### 1 - From what domain is the VBS script downloaded?
Como sempre, começo analisando as `Statistics` das conversas ``TCP``, sempre procurando por alguma anomalia.
![[Pasted image 20250326083954.png]]

Todos os pacotes são direcionados a um ``IP`` na porta ``80``, menos um fluxo que é direcionado na porta ``445`` que indica ``SMB``. Podemos seguir seu fluxo.
```
tcp.stream eq 0
```
![[Pasted image 20250326084518.png]]

Uma requisição do arquivo `AZURE_DOC_OPEN.vbs` em ``escuelademarina.com``

**Answer** : escuelademarina.com
***
#### 2 - What was the IP address associated with the domain in question #1 used for this attack?
```
tcp.stream eq 0
```
![[Pasted image 20250326084518.png]]
Podemos pegar o ``IP`` que está recebendo a requisição.

**Answer** : 165.22.16.55
***
#### 3 - What is the filename of the VBS script used for initial access?
```
tcp.stream eq 0
```
![[Pasted image 20250326084518.png]]
Podemos pegar o nome do arquivo que foi requisitado.

**Answer** : AZURE_DOC_OPEN.vbs
***
#### 4 - What was the URL used to get a PowerShell script?
No final do fluxo TCP, o arquivo VBS faz uma requisição de um script powershell.
```
tcp.stream eq 0
```
![[Pasted image 20250326085734.png]]

**Answer** : badbutperfect\.com/nrwncpwo
***
#### 5 - What likely legit binary was downloaded to the victim machine?
No final do último ``Stream TCP`` o host fez uma requisição para ``badbutperfect.com``, como todas as outras conversas são para este domínio, podemos analisar o ``Stream TCP`` de forma sequencial.
```
tcp.stream eq 1
```
![[Pasted image 20250326091018.png]]

Aqui uma requisição foi feita usando o ``PowerShell`` para baixar alguns arquivos, mas o único que tem o nome de que parece de um Software legítimo é `AutoHotKey.exe`.

**Answer** : AutoHotKey.exe
***
#### 6 - From what URL was the malware used with the binary from question #5 downloaded?
```
tcp.stream eq 1
```
![[Pasted image 20250326092102.png]]
O binário utilizado junto com o `AutoHotKey.exe` foi o  `script.ahk`, que foi baixado de `http://badbutperfect.com/jvtobaqj`.

**Answer** : http:\//badbutperfect.com/jvtobaqj
***
#### 7 - What filename was the malware from question #6 given on disk?
```
tcp.stream eq 1
```
![[Pasted image 20250326092102.png]]
Aqui seria o nome no ``-OutFile``

**Answer** : ``script.ahk``
***
#### 8 - What is the TLSH of the malware?
Primeiro exportamos o arquivo do malware, e mandamos para a análise no [Virus Total](https://www.virustotal.com/gui/home/upload)
![[Pasted image 20250326095432.png]]

Depois do envio, podemos ir na aba `Details`.
![[Pasted image 20250326095735.png]]

**Answer** :  T15E430A36DBC5202AD8E3074270096562FE7DC0215B4B32659C9EF16835CF6FF9B6A1B8 
***
#### 9 - What is the name given to this malware? Use the name used by McAfee, Ikarus, and alejandro.sanchez.
Depois do envio, podemos ir na aba `COMMUNITY`.
![[Pasted image 20250326095606.png]]

**Answer** : DarkGate
***
#### 10 - What is the user-agent string of the infected machine?
Após um mapeamento dos Fluxos ``TCP``, entendo que o `Stream` de número ``5`` é do host já infectado.
``0 ->`` Download do ``VBS Script`` via ``SMB``
``1 ->`` Download do Script PowerShell
``2 ->`` Download de um arquivo de test
``3, 4`` -> Download dos arquivos baixados pelo Script PowerShell
``5 ->`` Host já infectado (fazendo Exfiltração acredito)

```
tcp.stream eq 5
```
![[Pasted image 20250326101521.png]]

**Answer** : Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/118.0.0.0 Safari/537.36
***
#### 11 - To what IP does the RAT from the previous question connect?
Agora só precisamos ver o host em que o host infectado se conecta.
```
tcp.stream eq 5
```
![[Pasted image 20250326101814.png]]

**Answer** : 103.124.105.78
***

![[Pasted image 20250326101549.png]]