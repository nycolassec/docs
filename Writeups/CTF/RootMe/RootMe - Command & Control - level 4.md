Difficult : #Medium Category : #Forensics
***
#### Info
Recebemos um arquivo de dump de memória de windows para prosseguir nossas investigações.
![[Pasted image 20250522203409.png]]

Agora vejo qual profile é o melhor para utilizar com o dump.
```sh
python2 /opt/vol2.6/vol.py -f ./ch2.dmp imageinfo
```
![[Pasted image 20250522204756.png]]
Vamos utilizar o `Win7SP1x86_23418`.
#### Process
Podemos listar os processos que estavam rodando na máquina. Algo que eu tento sempre notar é qual processo está rodando pode ter iniciado um `cmd` ou `powershell`.
```sh
python2 /opt/vol2.6/vol.py -f ./ch2.dmp --profile=Win7SP1x86_23418 pslist
```
![[Pasted image 20250523072957.png]]

Aqui o processo iexplore.exe iniciou o cmd.exe o que não é comum. Então listei os comandos utilizados.
![[Pasted image 20250523074623.png]]
![[Pasted image 20250523074606.png]]
Parece que o `2772` e o `1616` tiveram um inicio normal.

Então parti para a saída dos comandos.
```sh
python2 /opt/vol2.6/vol.py -f ./ch2.dmp --profile=Win7SP1x86_23418 consoles
```
![[Pasted image 20250523075730.png]]

O processo `2128` iniciou um `tcprelay` (talvez para estabelecer uma conexão remota). Além de iniciar um `cmd`. Vou fazer o dump desse processo para ver qual o `IP` remoto que ele utilizou para contato. Como não tem no histórico de comando a chamada de `tcprelay.exe` ele foi chamado pelo executável.
```sh
python2 /opt/vol2.6/vol.py -f ./ch2.dmp --profile=Win7SP1x86_23418 memdump -p 2168 -D ./
strings 2168.dmp | grep tcprelay
```
![[Pasted image 20250523081445.png]]
Então encontramos a nossa flag.
***
![[Pasted image 20250523082358.png]]
