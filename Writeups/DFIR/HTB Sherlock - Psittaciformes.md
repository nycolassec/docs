#Difficult  : Easy
#Tools : VirusTotal, Shell
***
#### Info
Recebemos uma pasta `cascale_out` com alguns arquivos como logs e arquivos de usuários para nossa investigação.
![[Pasted image 20250327090253.png]]
***
#### 1 - What is the name of the repository utilized by the Pen Tester within Forela that resulted in the compromise of his host?
Algo interessante a se fazer é sempre ver o histórico de comandos de cada usuário, assim temos uma noção maior do que aconteceu. Em `./catscale_out/User_Files` temos um arquivo `hidden-user-home-dir.tar.gz`. Vamos extrair seus arquivos.
```sh
ls
tar -gz -x -f ./hidden-user-home-dir.tar.gz
ls
```
![[Pasted image 20250327092121.png]]

Podemos começar pelo ``./home/johnspire`` e ver o histórico.
```sh
cat /home/johnspire/.bash_history
```
![[Pasted image 20250327092536.png]]
E temos o nome do repositório usado para enumeração do host.

**Answer** : autoenum
***
#### 2 - What is the name of the malicious function within the script ran by the Pen Tester?
Agora que temos o repositório da ferramenta usada, podemos clona-la na máquina e analisarmos.
```sh
git clone https://github.com/pttemplates/autoenum && cd autoenum
```
![[Pasted image 20250327093838.png]]

Temos algumas funções nesse arquivo.
```
cat cat enum.sh
```
![[Pasted image 20250327094325.png]]

A função `do_wget_and_run` baixa algum arquivo da internet e o extrai com uma senha em `base64`, além de agendar a execução do script com o `crontab` para toda reinicialização.
![[Pasted image 20250327094606.png]]

**Answer** : do_wget_and_run
***
#### 3 - What is the password of the zip file downloaded within the malicious function?
Após ver o comando utilizado para decodificar senha, podemos apenas refaze-lo.
![[Pasted image 20250327094907.png]]

```sh
part1="c3VwZXI="
part2="aGFja2Vy"
echo "$part1$part2" | base64 -d
```
![[Pasted image 20250327095011.png]]

**Answer** : superhacker
***
#### 4 - What is the full URL of the file downloaded by the attacker?

Ainda no script, podemos remontar a ``URL`` usada.
![[Pasted image 20250327095243.png]]

```sh
f1="https://www.dropbox.com/scl/fi/uw8oxug0jydibnorjvyl2"
f2="/blob.zip?rlkey=zmbys0idnbab9qnl45xhqn257&st=v22geon6&dl=1"
echo "${f1}${f2}"
```
![[Pasted image 20250327095353.png]]

**Answer** : https:\//www.dropbox.com/scl/fi/uw8oxug0jydibnorjvyl2/blob.zip?rlkey=zmbys0idnbab9qnl45xhqn257&st=v22geon6&dl=1
***
#### 5 - When did the attacker finally take out the real comments for the malicious function?
Podemos ver o histórico de alterações do arquivo.
```
git log --follow -p enum.sh
```
![[Pasted image 20250327102947.png]]

No ``commit 7d203152c5a3a56af3d57eb1faca67a3ec54135f`` observamos que os comentários foram retirados.

**Answer** : 2024-12-23 22:27:58
***
#### 6 - The attacker changed the URL to download the file, what was it before the change?
Podemos seguir no fluxo de mudanças do arquivo até encontrar a última mudança da `URL`.
![[Pasted image 20250327103555.png]]

No `commit 5d88bee8918d514a206fec91be72899544cdd37b` houve a última mudança da `URL`.

**Answer** : https:\//www.dropbox.com/scl/fi/wu0lhwixtk2ap4nnbvv4a/blob.zip?rlkey=gmt8m9e7bd02obueh9q3voi5q&st=em7ud3pb&dl=1
***
#### 7 - What is the MITRE technique ID utilized by the attacker to persist?
Como o script adicionou um ``cronjob`` para persistência, podemos pesquisar no [Mitre ATT&CK](https://attack.mitre.org/).
![[Pasted image 20250327111823.png]]

**Answer** : T1053.003
***
#### 8 - What is the Mitre Att&ck ID for the technique relevant to the binary the attacker runs?
Aqui, podemos fazer o download do ``malware`` pela ``URL`` do ``script``, pegar seu ``SHA256`` e pesquisarmos no ``VirusTotal``. Sendo a senha do arquivo a mesma da pergunta ``3``.
```sh
wget "https://www.dropbox.com/scl/fi/uw8oxug0jydibnorjvyl2/blob.zip?rlkey=zmbys0idnbab9qnl45xhqn257&st=v22geon6&dl=1"

unzip blob.zip\?rlkey=zmbys0idnbab9qnl45xhqn257\&st=v22geon6\&dl=1

sha256sum blob
```
![[Pasted image 20250327110628.png]]

Após o envio do hash no `VirusToatl`, na aba `BEHAVIOR` podemos ir em `MITRE ATT&CK Tactics and Techniques` e `Impact`.
![[Pasted image 20250327110937.png]]

**Answer** : T1496
***

![[Pasted image 20250327111311.png]]































