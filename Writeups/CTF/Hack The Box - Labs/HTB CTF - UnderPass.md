#Difficult : Easy
#OS : Linux
***
## Port Scanning
Como sempre começamos fazendo um escaneamento de portas para termos uma noção de quais serviços estão rodando na máquina.

Enumeramos então as portas dos serviços que rodam em cima do `TCP`.
```bash
nmap 10.10.11.48 -sC -Pn -sV -A -sU --top-ports 100 -oN nmap_output_udp
```
![[Pasted image 20250401174929.png]]
Temos então um serviço `HTTP` e `SSH`.

Agora enumeramos as portas dos serviços que rodam em cima do `UDP`.
```bash
nmap 10.10.11.48 -sC -Pn -sV -A -sU --top-ports 100 -oN nmap_output_udp
```
![[Pasted image 20250401174956.png]]
Descobrimos então um serviço `SNMP`, que remete a um servidor de email. Podemos então adicionar o host `underpass.htb` em no arquivo de hosts.

```bash
echo "10.10.11.48    underpass.htb" >> /etc/hosts
```

>[!tip] Comando `nmap`
>Costumo usar sempre o mesmo comando `nmap`, a menos que tenha alguma proteção contra `network scan`.

***
## Broken Authentication
Então faço uma pesquisa sobre o `Daloradius` que foi o serviço citado no `udp scan` . Encontrei então a [WIKI](https://github.com/lirantal/daloradius/wiki/Installing-daloRADIUS) de instalação do `Daloradius`.
Nessa WIKI encontrei as credenciais padrão.
![[Pasted image 20250401180611.png]]

Nessa WIKI também diz que o `Daloradius` por padrão tem duas interfaces, `users` para usuários regulares e `operators` para administradores. Também mostra as rotas para essas interfaces.
![[Pasted image 20250401181227.png]]

Essa interfaces podem ter sido trocadas, na instalação atual, mas podemos conferir. Ao acessarmos `http://underpass.htb/daloradius/app/operators` somos redirecionados para `/login.php`. Podemos então tentar usar as credenciais que achamos.
![[Pasted image 20250401181622.png]]
***
## RCE
Conseguimos acesso a administração do `Daloradius`. Tentei procurar por alguma vulnerabilidade, mas não encontrei muita coisa a respeito, então decidir ir em `Management` e `List Users`, vi que tinha um usuário com uma senha em `MD5`.
![[Pasted image 20250401182040.png]]

Então verifiquei se o hash já foi quebrado em algum site que armazena essas hashes como o `crackstation`.
![[Pasted image 20250401181839.png]]

Podemos agora tentar nos conectar via `SSH` com as credenciais encontradas.
```bash
ssh svcMosh@underpass.htb
```
![[Pasted image 20250401182455.png]]

**Entramos!**
***
## PRIV ESC
Listo então quais comandos posso executar com permissões de `root`.
```bash
sudo -l
```
![[Pasted image 20250401182924.png]]

Há um [artigo](https://medium.com/@momo334678/mosh-server-sudo-privilege-escalation-82ef833bb246) que mostra como podemos fazer `PRIV ESC` se tivermos a possibilidade de executa-lo com `sudo`.
![[Pasted image 20250401183854.png]]

>[!tip] Artigo
>Separe um tempo para ler o [artigo](https://medium.com/@momo334678/mosh-server-sudo-privilege-escalation-82ef833bb246) e entende-lo de verdade.

Primeiro iniciamos o servidor com `sudo` para obtermos qual a `port` e a `key` para a conexão.
```bash
sudo /usr/bin/mosh-server
```
![[Pasted image 20250401184853.png]]

Agora usamos o `mosh-client` para nos conectarmos, usando a `port`, `key` e `IP` do servidor.
```bash
MOSH_KEY=Uz3GqEBX52wimlLeKrc7WA mosh-client 127.0.0.1 60001
```
![[Pasted image 20250401184822.png]]

**Owned!**
***
![[Pasted image 20250401184940.png]]