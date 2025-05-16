#Category : Endpoint Forensics #Difficult : Easy
***
#### 1 - What is the name of the process responsible for the suspicious activity?
Começo vendo os comandos emitidos no sistema.
```sh
vol -f memory.dmp windows.cmdline
```
![[Pasted image 20250515195837.png]]
Temos um processo que tenta se passar pelo instalador do `Chrome`. Pois o nome do processo não corresponde ao do instalador oficial.

**Answer :** *`chromesetup.exe`*
***
#### 2 - What is the exact path of the executable for the malicious process?
Podemos ver os comandos emitidos na `shell` e procurar pelo `pid` do processo.
```sh
vol -f memory.dmp windows.cmdline
```
![[Pasted image 20250515195837.png]]

**Answer :** *`C:\Users\alex\Downloads\ChromeSetup.exe`*
***
#### 3 - Identifying network connections is crucial for understanding the malware's communication strategy. What IP address did the malware attempt to connect to?
Então eu filtro as conexões de rede feitas pelo processo malicioso.
```sh
vol -f memory.dmp windows.netscan | grep -i chrome
```
![[Pasted image 20250515201337.png]]

**Answer :** *`58.64.204.181`*
***
#### 4 - To determine the specific geographical origin of the attack, Which city is associated with the IP address the malware communicated with?
Para essa questão podemos usar uma `API` de informações de `IP` diretamente do terminal.
```sh
curl http://ip-api.com/json/58.64.204.181 | jq
```
![[Pasted image 20250515202007.png]]

**Answer :** *`Hong Kong`*
***
#### 5 - Hashes serve as unique identifiers for files, assisting in the detection of similar threats across different machines. What is the SHA1 hash of the malware executable?
Primeiro eu listo os arquivos que contém `chrome` no nome, para ter o `offset` para o `dump`.
```
vol -f memory.dmp windows.filescan | grep -i 'chrome'
```
![[Pasted image 20250515204637.png]]

Agora podemos fazer a extração com o endereço que conseguimos co o `filescan`.
```sh
vol -f memory.dmp windows.dumpfiles --virtaddr 0xca82b85325a0
```
![[Pasted image 20250515204833.png]]

Agora podemos ver a sua `hash`.
```sh
sha1sum ./file.0xca82b85325a0.0xca82b7e06c80.ImageSectionObject.ChromeSetup.exe.img
```
![[Pasted image 20250515205124.png]]

**Answer :** *`280c9d36039f9432433893dee6126d72b9112ad2`*
***
#### 6 - Examining the malware's development timeline can provide insights into its deployment. What is the compilation timestamp for the malware?
Podemos utilizar o `exiftool` para ver informações sobre as datas. Podemos passar o formato `UTC` como variável.
```sh
TZ=UTC exiftool ./file.0xca82b85325a0.0xca82b7e06c80.ImageSectionObject.ChromeSetup.exe.img
```
![[Pasted image 20250516082317.png]]

**Answer :** *`2019-12-01 08:36`*
***
#### 7 - Identifying the domains associated with this malware is crucial for blocking future malicious communications and detecting any ongoing interactions with those domains within our network. Can you provide the domain connected to the malware?
Com a `hash` que tiramos do executável, podemos ver sua análise no `Virus Total` em *`Relationns > Contacted Domains`*.
![[Pasted image 20250516082737.png]]

**Answer :** *`dnsnb8.net`*
***
![[Pasted image 20250516083438.png]]

