#Difficult : Easy #Category : Endpoint Forensics
***
#### Info
Recebemos um arquivo de dump de memória para iniciar nossas investigações.
![[Pasted image 20250515181236.png]]
***
#### 1 - Identifying the name of the malicious process helps in understanding the nature of the attack. What is the name of the malicious process?
```sh
vol3 -f 192-Reveal.dmp windows.pstree --pid 3692
```
Começo vendo quais comandos fora emitidos na `shell`.
![[Pasted image 20250515191341.png]]

Tanto `powershell.exe` quanto `net.exe` executam comandos suspeitos, mas como o `powershell.exe` que chama o outro processo, ele é a resposta.

**Answer :** *`powershell.exe`*
***
#### 2 - Knowing the parent process ID (PPID) of the malicious process aids in tracing the process hierarchy and understanding the attack flow. What is the parent PID of the malicious process?
Podemos ver a árvore de processos e filtrar pelo `PID` do processo da questão anterior.
```sh
vol -f 192-Reveal.dmp windows.pstree --pid 3692
```
![[Pasted image 20250515191839.png]]

**Answer :** *`4120`*
***
#### 3 - Determining the file name used by the malware for executing the second-stage payload is crucial for identifying subsequent malicious activities. What is the file name that the malware uses to execute the second-stage payload?
Olhei novamente os comandos emitidos e filtrei pelo `PID` dos processos suspeitos.
```sh
vol -f 192-Reveal.dmp windows.cmdline | grep -Ei "(3692|2416)"
```
![[Pasted image 20250515192216.png]]

**Answer :** *`3435.dll`*
***
#### 4 - Identifying the shared directory on the remote server helps trace the resources targeted by the attacker. What is the name of the shared directory being accessed on the remote server?
Na saída do comando anterior podemos ver a `share` usada.
```sh
vol -f 192-Reveal.dmp windows.cmdline | grep -Ei "(3692|2416)"
```
![[Pasted image 20250515192216.png]]

**Answer :** *`davwwwroot`*
***
#### 5 - What is the MITRE ATT&CK sub-technique ID that describes the execution of a second-stage payload using a Windows utility to run the malicious file?
No comando do `powershell` temos o binário `builtin` do windows carregando uma `dll` remotamente. Podemos fazer uma pesquisa básicom com essas informações.
![[Pasted image 20250515192525.png]]

![[Pasted image 20250515192654.png]]

**Answer :** *`T1218.011 `*
***
#### 6 - Identifying the username under which the malicious process runs helps in assessing the compromised account and its potential impact. What is the username that the malicious process runs under?
Podemos ver a relação entre o `pid` dos processos e o `sid`.
![[Pasted image 20250515192943.png]]

**Answer :** *`elon`*
***
#### 7 - Knowing the name of the malware family is essential for correlating the attack with known threats and developing appropriate defenses. What is the name of the malware family?
No comando do `powershell` temos um `IP` da onde o malware foi baixado. Submeteremos o `IP` ao `VirusTotal` e ver a *`Relations > Files Referring`*
![[Pasted image 20250515193428.png]]

O primeiro arquivo chama a atenção pelo seu nome, podemos ter mais informações sobre ele, clicando nele. Na aba *`Detection > Security venor's analysis`* temos sua família de malwares.
![[Pasted image 20250515193620.png]]

**Answer :** *`StrelaStealer`*
***
![[Pasted image 20250515193646.png]]