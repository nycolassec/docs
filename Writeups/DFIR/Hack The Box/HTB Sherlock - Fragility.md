#Difficult : Easy
#Tools : Wireshark
***
#### Info
![[Pasted image 20250325115454.png]]
Recebemos para a análise um arquivo de tráfego de rede, e uma pasta que pelo o que parece, contém a raiz de um sistema ``linux``.
***
#### 1 - What CVE did the attacker use to exploit the vulnerability?
Podemos começar olhando a captura do tráfego de rede. Sempre começo pelas estatísticas das conversas, para ter uma visão mais abrangente.
![[Pasted image 20250325153757.png]]

Uma conversa teve muito mas pacotes do que as outras, o que é anormal.
![[Pasted image 20250325155001.png]]

Como a primeira conversa se refere ao SSH, não conseguir entender a conversa, mas podemos nos atentar ao IP. Que logo abaixo inicia outra conversa. Podemos seguir seu fluxo e tentar entender o que aconteceu.
![[Pasted image 20250325155141.png]]

Essa primeira requisição ao servidor do Splunk mais parece uma payload do que uma requisição normal, então pesquisei para ver se achava algo.
![[Pasted image 20250325155415.png]]

Há uma ``CVE`` relacionada ao envio de uma payload antes da linguagem do servidor ``Splunk``.

**Answer** : CVE-2023-46214
***
#### 2 - What MITRE technique does the attacker use to maintain persistence?
Podemos dar continuidade na leitura do tráfego até acharmos algo relacionado a persistência.
![[Pasted image 20250325161830.png]]

O método usado para persistência foi o de criação de uma conta de usuário
![[Pasted image 20250325162018.png]]

**Answer** : T1136
***
#### 3 - John has adjusted the timezone but hasn't rebooted the computer yet, which has led to some things either being updated or not updated with the new timezone. Identifying the timezone can assist you further in your investigation. What was the default timezone and the timezone after John's adjustment on this machine?
Nos arquivos que nos foram dados, podemos pesquisar dentro de ``var/logs/``, que é onde fica os logs do sistema.

Podemos pesquisar por GMT(Greenwich Mean Time), que costuma ser um indicador comum, junto com UTC.
```sh
grep -R  "GMT" ./var/log/
```
![[Pasted image 20250325152800.png]]
No log do syslog, o ``Gnome Shell`` iniciou com o horário ``PDT``, que seria o mesmo que ``UTC−07``. 

Também foi pedido o ``Time Zone`` ajustado. Podemos pesquisar pelo comando ``timedatectl``, que é responsável por operações como o ``Time Zone``.
![[Pasted image 20250325153036.png]]
Aqui vemos o ``Asia/Ho_Chi_Minh``, que equivale a ``UTC+07``.

**Answer** : utc-07/utc+07
***
#### 4 - When did the attacker SSH in? (UTC)
Já sabemos o IP do atacante, podemos então filtrar pelo protocolo SSH, e observar o tráfego até achar o primeiro pacote criptografado.
```
ip.addr == 192.168.222.130 && ssh
```
![[Pasted image 20250325163148.png]]


**Answer** : 04-14 15:00:21
***
#### 5 - How much time has passed from when the user was first created to when the attacker stopped using SSH?
Sabemos que o usuário foi criado via ``HTTP``, então podemos seguir o tráfego até achar onde ele foi criado.
```
ip.addr == 192.168.222.130 && http
```
![[Pasted image 20250325165318.png]]
Este pacote indica quando o invasor rodou o script para a criação do usuário. E o pacote seguinte, confirma a criação em ``15:00:13``.

```
ip.addr == 192.168.222.130 && ssh
```
Após isso filtramos pelo tráfego ``SSH``, seguimos o ``TCP Stream`` que nos levará no `tcp.stream eq 4` e vamos até o final e ver quando ele finaliza a conexão. 
![[Pasted image 20250325170009.png]]
O primeiro pacote de finalização é enviado em ``15:03:08``.

Agora convertemos os dois tempos para segundos, podemos retirar as horas.
(03:08 = 188s) - (00:13 = 13s) = 175s
175s = ``02:55``

**Answer** : 00:02:55
***
#### 6 - What is the password for the account that the attacker used to backdoor?
Podemos filtrar pelo tráfego HTTP e seguir o `TCP Stream`, e analisarmos o tráfego até vermos o script de criação do usuário.
```
ip.addr == 192.168.222.130 && http
```
![[Pasted image 20250325171817.png]]

Agora extraímos a criação da senha e executamos no nosso terminal.
```
echo MzlhNmJiZTY0NTYzLTY3MDktOTNhNC1hOWYzLTJjZTc4Mjhm | base64 -d | rev
```
![[Pasted image 20250325172023.png]]

**Answer** : f8287ec2-3f9a-4a39-9076-36546ebb6a93
***
#### 7 - There is a secret in the exfiltrated file, what is its content?
No pacote anterior, vimos também que a home do usuário foi definida para ``/var/www``, podemos analisar o histórico do bash nesse local.
```sh
cat ./var/www/.bash_history
```
![[Pasted image 20250325203117.png]]

O invasor criptografou o arquivo e o enviou pela por ``8080`` no formato `ebcdic`, podemos filtrar pela porta `8080` e seguir o `TCP Stream` para tentar recuperar o arquivo.
```
ip.addr == 192.168.222.130 and tcp.port == 8080
```
![[Pasted image 20250325203558.png]]

Devemos coloca-lo em `EBCDIC` e exporta-lo, para descriptografar.
![[Pasted image 20250325203905.png]]

Após alguma pesquisa, consegui achar o comando de criptografia completo em `./var/log/auth.log`.
```sh
cat ./var/log/auth.log | grep "openssl"
```
![[Pasted image 20250325204151.png]]

Depois que sabemos qual foi o comando usado para criptografar, podemos fazer o processo inverso para descriptografar.
```sh
cat export.enc | base64 -di | openssl enc -d -aes-256-cbc -iv 4fa17640b7dfe8799f072c65b15f581d -K 3cabc6db78a034f69f16aa8986cf2e2cea05713b1e95ff9b2d80f6a71ae76b7d > export.zip
```
![[Pasted image 20250326073605.png]]

Dentro do zip tem um arquivo ``PDF``, vamos abri-lo.
![[Pasted image 20250326073703.png]]
E aqui está o arquivo roubado.

**Answer** : Th3_uNs33n_P4\$$w0rd_is_th3_m05t_s3cur3
***
#### 8 - What are the username and password that the attacker uses to access Splunk?
```
ip.addr == 192.168.222.130 && http
```
Podemos filtrar pelo tráfego HTTP e seguir o `TCP Stream`, e analisarmos o tráfego até vermos a ação de login.
![[Pasted image 20250325173224.png]]

**Answer** : johnnyC:h3Re15j0hnNy
***

![[Pasted image 20250325202438.png]]
























