#Difficult : Easy
#Tools :
***
#### Info
Recebemos 6 artefatos para realizarmos nossas investigações dentre eles 4 imagens, um arquivo de ``keylog`` e o ``Malware``.
![[Pasted image 20250328104154.png]]
***
#### 1 - What is the SHA-256 hash of this malware binary?
Podemos obter essa informação diretamente do terminal.
```bash
sha256sum Loggy.exe
# 6acd8a362def62034cbd011e6632ba5120196e2011c83dc6045fcb28b590457c  Loggy.exe
```

**Answer** : 6acd8a362def62034cbd011e6632ba5120196e2011c83dc6045fcb28b590457c
***
#### 2 - What programming language (and version) is this malware written in?
Podemos obter através do `VirusTotal` em ``Details`` e `Basic Properties`.
![[Pasted image 20250328111041.png]]

Já sabemos a linguagem mas não a versão exata do compilador. Podemos então filtrar as strings do binário para achar a versão.
```bash
strings ./Loggy.exe | grep "go1."
```
![[Pasted image 20250328111408.png]]

Caso não estivesse escrito, poderíamos usar a ferramenta `DetectItEasy`.

**Answer** : Golang 1.22.3
***
#### 3 - There are multiple GitHub repos referenced in the static strings. Which GitHub repo would be most likely suggest the ability of this malware to exfiltrate data?
Quando usei o comando `string` retornou muitos repositórios, então filtrei pelo diretório pai de cada repositório para ver minhas opções e analisar cada uma.
```bash
strings ./Loggy.exe | grep -E "^github\.com/" | cut -d '/' -f1,2 | sort -u
```
![[Pasted image 20250328133733.png]]
**`grep -E "^github\.com/"`** : Somente strings que começam com ``github.com/``
**`cut -d '/' -f1,2`** : Vai delimitar as colunas pelo caractere `/`
**`sort -u`** : Irá excluir todas as strings repetidas

Após uma breve pesquisa, descobri um repositório de um cliente ``FTP`` para go
```bash
strings ./Loggy.exe | grep -E "^github\.com/jlaffaye" | cut -d '/' -f1,2,3 | sort -u
```
![[Pasted image 20250328134250.png]]

**Answer** : github\.com/jlaffaye/ftp
***
#### 4 - What dependency, expressed as a GitHub repo, supports Janice’s assertion that she thought she downloaded something that can just take screenshots?
Agora busquei os repositórios com alguma palavra chave como `shot`, `screen` e `print`.
```bash
strings ./Loggy.exe | grep -E "^github\.com/" | sort -u | grep "screen"
```
![[Pasted image 20250328134833.png]]

**Answer** : github\.com/kbinani/screenshot
***
#### 5 - Which function call suggests that the malware produces a file after execution?

**Answer** : 
***
#### 6 - You observe that the malware is exfiltrating data over FTP. What is the domain it is exfiltrating data to?

**Answer** : 
***
#### 7 - What are the threat actor’s credentials?

**Answer** : 
***
#### 8 - What file keeps getting written to disk?

**Answer** : 
***
#### 9 - When Janice changed her password, this was captured in a file. What is Janice's username and password?

**Answer** : 
***
#### 10 - What app did Janice have open the last time she ran the "screenshot app"?

**Answer** : 
***


