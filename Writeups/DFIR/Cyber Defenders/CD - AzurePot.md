#Category : Endpoint Forensics #Difficult : Medium
***
#### Info
Aqui recebemos os artefatos de uma máquina que foi explorada utilizando a `CVE-2021-41773`. Para realizarmos nossas investigações recebemos 3 artefatos.
![[Pasted image 20250512175903.png]]
***
#### 1 - There is a script that runs every minute to do cleanup. What is the name of the file?
==sdb.vhd==

Aqui podemos olhar em `/var/spool/crontabs` para ver quais scripts foram automatizados pelo `crontab`.

No arquivo `root` temos um script que é chamado todo minuto.
![[Pasted image 20250512180412.png]]

**Answer :** `.remove.ssh`
***
#### 2 - The script in Q1 terminates processes associated with two Bitcoin miner malware files. What is the name of 1st malware file?
==sdb.vhd==

Podemos olhar o conteúdo script `/root/.remove.sh` e ver os arquivos que ele altera.
![[Pasted image 20250512182441.png]]

**Answer :** `kinsing`
***
#### 3 - The script changes the permissions for some files. What is their new permission?
==sdb.vhd==

Novamente podemos olhar para o script em `/root/.remove.sh`.
![[Pasted image 20250512182441.png]]

**Answer :** `444`
***
#### 4 - What is the SHA256 of the botnet agent file?
==sdb.vhd==

Esse ataque se resume em um `path transversal` pelo servidor web `Apache`, podemos estão olhar seus logs para tentar obter alguma pista. Os logs estão em `/var/log/apache2`.

Em `access_log` requisições tem o caractere `%2e` que indica um `.`, assim que o atacante consegue passar da verificação.
![[Pasted image 20250512185741.png]]

Após algum atacante conseguir invadir uma máquina, normalmente se baixa binários para serem executados. Nessa lógica podemos filtrar os logs por `chmod +x` ou alguma variante.
![[Pasted image 20250512191836.png]]

Temos então um arquivo que foi mudado para ser executado em `/tmp/dk86`, podemos então tirar seu `hash`. Não o encontrei em `/tmp` mas em `/var/tmp` o que indica que o atacante moveu de lugar.
![[Pasted image 20250512192401.png]]

![[Pasted image 20250512192605.png]]

**Answer :** `0e574fd30e806fe4298b3cbccb8d1089454f42f52892f87554325cb352646049`
***
#### 5 - What is the name of the botnet in **Q4**?
==sdb.vhd==

Podemos colocar a `hash` que obtemos do malware no `VirusToatl`.
![[Pasted image 20250512192740.png]]

**Answer :** `Tsunami`
***
#### 6 - What IP address matches the creation timestamp of the botnet agent file in **Q4**?
==sdb.vhd==

Podemos filtrar por comandos que poderiam baixar esse binário, como `curl` ou `wget`.
```sh
grep -E '(wget|curl).*dk86' error_log
```
![[Pasted image 20250512193640.png]]

**Answer :** `141.135.85.36`
***
#### 7 - What URL did the attacker use to download the botnet agent?
==sdb.vhd==

Como se refere ao download do binário podemos usar o mesmo comando da pergunta anterior.
```sh
grep -E '(wget|curl).*dk86' error_log
```
![[Pasted image 20250512193640.png]]

**Answer :** `http://138.197.206.223:80/wp-content/themes/twentysixteen/dk86`
***
#### 8 - What is the name of the file that the attacker downloaded to execute the malicious script and subsequently remove itself?
==sdb.vhd==

Como também se refere a um script baixado decidi buscar novamente por `wget` e `curl` além de filtrar também pelos `IPs` anteriormente adquiridos.
![[Pasted image 20250512202129.png]]

Mas todos os logs fazem referência ao binário `dk86`. Então decidi excluir esses `IPs` e o binário `dk86`. Mas ainda obtive muito tráfego.
![[Pasted image 20250512202538.png]]

Então agora também exclui logs que continham `curl/7.74` e `/bin/sh`pois poluem o tráfego.
![[Pasted image 20250512203202.png]]

Consegui o nome de outros executáveis como `init.sh`, `ap.sh` mas nenhum com as condições pedidas. Mas um log me chamou a atenção, pois continha código encodado em `base64`.
![[Pasted image 20250512203930.png]]

Os dois são obtidos do arquivo `b64.php`, então usei o `nvim` para fazer uma navegação no conteúdo e fazer a busca. Vi que tinha 4 linhas seguidas de código em `base64`.
![[Pasted image 20250513072908.png]]

Então juntei todos e usei o `CyberCheff` como decode.
![[Pasted image 20250513074820.png]]

Temos então a criação de um script chamado `.install`, que carrega novamente um conteúdo em `base64`, podemos usar um decode para ele também.
![[Pasted image 20250513074740.png]]
Temos então a remoção do próprio `script` com parte do seu fluxo.

**Answer :** `.install`
****
#### 9 - The attacker downloaded SH scripts. What are the names of these files?
==sdb.vhd==

Aqui podemos fazer um filtro por `.sh` já que é a extensão desses scripts.
```sh
grep -E "(wget|curl).*\.sh" error_log
```
![[Pasted image 20250513075324.png]]
***
#### 10 - Two suspicious processes were running from a deleted directory. What are their PIDs?
==UAC==

O comando que se refere a arquivos abertos por um processo é o `lsof`, então primeiro pesquisei para ver se tinha algo relacionado na pasta.
![[Pasted image 20250513075940.png]]

Então filtrei por `deleted` e procurei algum processo que fosse suspeito. `aggetyd` é o nome de um processo legítimo, então nos sobraram duas opções.
```sh
grep deleted ./live_response/process/lsof_-nPl.txt
```
![[Pasted image 20250513080120.png]]

**Answer :** `6388, 20645`
***
#### 11 - What is the suspicious command line associated with the PID that ends with **45** in **Q10**?
==UAC==

O comando `ps -ef` mostra os processos com informações completas, inclusive a linha de comando que os originou.
```sh
find ./ -name *ps_-*
grep -E " 20645 " ./live_response/process/ps_-ef.txt
```
![[Pasted image 20250513081933.png]]

**Answer :** `sh .src.sh`
***
#### 12 - UAC gathered some data from the second process in **Q10**. What is the remote IP address and remote port that was used in the attack?
==UAC==

Não achei nos comando utilizados, então olhei dentro da pasta do processo em vi que tinha o arquivo de variáveis de ambiente `./live_response_proccess/proc/20645/environ.txt`.
```sh
cat ./live_response_proccess/proc/20645/environ.txt
```
![[Pasted image 20250513082622.png]]

**Answer :** `116.202.187.77:56590`
***
#### 13 - Which user was responsible for executing the command in **Q11**?
==UAC==

Novamente podemos olhar o arquivo do `ps -ef` e filtrar pelo `pid`.
```sh
grep -E " 20645 " ./live_response/process/ps_-ef.txt
```
![[Pasted image 20250513081933.png]]

**Answer :** `daemon`
***
#### 14 -  Two suspicious shell processes were running from the **tmp** folder. What are their PIDs?
==UAC==

Aqui podemos utilizar novamente o arquivo do comando `lsof`.
```sh
grep -E "/tmp$" ./live_response/process/lsof_-nPl.txt
```
![[Pasted image 20250513083530.png]]

**Answer :** `15853, 21785`
***
#### 15 - What is the MAC address of the captured memory?
==UAC==

Não necessariamente precisamos usar o dump de memória, podemos usar a saída do comando `ifconfig`.
```sh
find ./ -name *ifconfig*
cat ./live_response/network/ifconfig_-a.txt
```
![[Pasted image 20250513083908.png]]

**Answer :** `00:22:48:26:3b:16`
***
#### 16 - Based on Bash history. The attacker downloaded the SH script. What is the name of the file?
==UAC==

Aqui podemos olhar o histórico de comandos do usuário `azureuser` e filtrar por `wget`.
![[Pasted image 20250513084320.png]]

**Answer :** `unk.sh`
***

![[Pasted image 20250513084520.png]]


























