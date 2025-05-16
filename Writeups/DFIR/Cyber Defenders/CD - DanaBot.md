#Category : Network Forensics #Difficult : Easy
***
#### Info
Recebemos um arquivo `PCAP` para prosseguir coma s investigações.
![[Pasted image 20250515171408.png]]
***
#### 1 - Which IP address was used by the attacker during the initial access?
Ao abrir o arquivo de tráfego de rede, identifiquei um acesso `http` ao arquivo `login.php` o que indicaria tentativa de login em alguma aplicação. Então segui o `HTTP Stream` do pacote.
![[Pasted image 20250515171802.png]]

Parece que um arquivo `JS` foi enviado para o servidor.
![[Pasted image 20250515172325.png]]

Podemos exportar esse arquivo em `File > Export Objects > HTTP`
![[Pasted image 20250515172619.png]]

Então tirei a `sha256 hash` do arquivo e pesquisei no `VirusTotal`. Então vejo que é um arquivo malicioso.
![[Pasted image 20250515173106.png]]

![[Pasted image 20250515172810.png]]

**Answer :** *`62.173.142.148`*
***
#### 2 - What is the name of the malicious file used for initial access?
O nome do arquivo aparece na requisição de `login.php`
![[Pasted image 20250515172325.png]]

**Answer :** *`allegato_708.js`*
***
#### 3 - What is the SHA-256 hash of the malicious file used for initial access?
Podemos usar o comando `sha256 sum` para essa tarefa.
![[Pasted image 20250515173106.png]]

**Answer :** *`847b4ad90b1daba2d9117a8e05776f3f902dda593fb1252289538acf476c4268`*
***
#### 4 - Which process was used to execute the malicious file?
Aqui olhei o conteúdo de `login.php`, e tentei identificar qual executável inicia o `script`.
![[Pasted image 20250515175611.png]]

Então pesquisei sobre esse `WScript` para saber seu objetivo.
![[Pasted image 20250515175704.png]]

**Answer :** *`wscript.exe`*
***
#### 5 - What is the file extension of the second malicious file utilized by the attacker?
Comecei a olhar as `Conversations TCP`, as próximas conversas são em `HTTPS` ou seja ilegíveis. Então olhei para a próxima `Conversation`, ela se origina do servidor atacado, além de possuir um número muito alto de pacotes e o maior número de bytes. Então selecionei `Follow Stream` para acessar o fluxo.
![[Pasted image 20250515174114.png]]

Ele buscou um arquivo `resources.dll` em um servidor externo. Esse arquivo se refere a uma biblioteca do windows, ou um processo.
![[Pasted image 20250515174420.png]]

**Answer :** *`.dll`*
***
#### 6 - What is the MD5 hash of the second malicious file?
Primeiro exportei o arquivo da questão anterior, então usei o `md5sum` para saber seu `hash`.
![[Pasted image 20250515175920.png]]

![[Pasted image 20250515180331.png]]

**Answer :** *`e758e07113016aca55d9eda2b0ffeebe`*
***

![[Pasted image 20250515180231.png]]