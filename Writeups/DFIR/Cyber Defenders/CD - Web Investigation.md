#Difficult : Easy
#Category : Network Forensics
***
#### 1 - By knowing the attacker's IP, we can analyze all logs and actions related to that IP and determine the extent of the attack, the duration of the attack, and the techniques used. Can you provide the attacker's IP?
Indo em `Statistics -> Conversations -> IPv4` temos uma anomalia, sendo que o  um host envia muitos pacotes acima do perfil da rede.
![[Pasted image 20250416141706.png]]

**Answer : `111.224.250.131`**
***
#### 2 - If the geographical origin of an IP address is known to be from a region that has no business or expected traffic with our network, this can be an indicator of a targeted attack. Can you determine the origin city of the attacker?
Usando o `curl` podemos usar uma `API` que nos fornece algumas informações sobre um `IP`.
```bash
curl https://ipinfo.io/111.224.250.131/json
```
![[Pasted image 20250416142825.png]]

**Answer : `Shijiazhuang`**
***
#### 3 - If the geographical origin of an IP address is known to be from a region that has no business or expected traffic with our network, this can be an indicator of a targeted attack. Can you determine the origin city of the attacker?
Aqui fiz uma pesquisa com o `IP` do atacante e a extensão `.php`, e então observei se havia algo que fugisse do padrão.
```
ip.addr == 111.224.250.131 && http.request.full_uri contains ".php"
```
![[Pasted image 20250416143729.png]]

Encontrei tentativas de `SQL Injection` e `XSS Injection` sobre o recurso `search.php`.

**Answer : `search.php`**
***
#### 4 - Establishing the timeline of an attack, starting from the initial exploitation attempt, What's the complete request URI of the first SQLi attempt by the attacker?
Podemos ver a `URI` da primeira requisição com `SQL Injection`.
![[Pasted image 20250416144302.png]]

**Answer : `/search.php?search=book%20and%201=1;%20--%20-`**
***
#### 5 - Can you provide the complete request URI that was used to read the web server's available databases?
A tabela `information_schema` contém informações sobre a estrutura do banco de dados, podemos ver se houve alguma tentativa de acesso a ela.
```
ip.addr == 111.224.250.131 && http.request.full_uri contains "INFORMATION"
```
![[Pasted image 20250416150316.png]]

**Answer : `/search.php?search=book%27%20UNION%20ALL%20SELECT%20NULL%2CCONCAT%280x7178766271%2CJSON_ARRAYAGG%28CONCAT_WS%280x7a76676a636b%2Cschema_name%29%29%2C0x7176706a71%29%20FROM%20INFORMATION_SCHEMA.SCHEMATA--%20-`**
***
#### 6 - Assessing the impact of the breach and data access is crucial, including the potential harm to the organization's reputation. What's the table name containing the website users data?
Podemos fazer a mesma pesquisa, e então ver os detalhes dos pacotes de resposta.
```
ip.addr == 111.224.250.131 && http.request.full_uri contains "INFOR
```
![[Pasted image 20250416150848.png]]

Nos detalhes do pacote `1553` temos uma lista de tabelas extraídas pelo atacante.
![[Pasted image 20250416151104.png]]

**Answer : `customers`**
***
#### 7 - The website directories hidden from the public could serve as an unauthorized access point or contain sensitive functionalities not intended for public access. Can you provide the name of the directory discovered by the attacker?
Tentei pesquisar mais sobre o `SQLInjection` mas sem sucesso, então filtrei por requisições `POST`, pois é o método usado para o envio de dados. Após o filtro, descobri o diretório sensível.
```
ip.addr == 111.224.250.131 && http.request.method == POST
```
![[Pasted image 20250416151629.png]]

**Answer : `/admin/`**
***
#### 8 - Knowing which credentials were used allows us to determine the extent of account compromise. What are the credentials used by the attacker for logging in?
Apenas adicionei o filtro de `login.php` para ver se tinha alguma outra página de login. Então olhei as informações do último pacote, pois acredito que o último indica que ele obteve êxito.
```
ip.addr == 111.224.250.131 && http.request.method == POST
```
![[Pasted image 20250416152200.png]]

**Answer : `admin:admin123!`**
***
#### 9 - We need to determine if the attacker gained further access or control of our web server. What's the name of the malicious script uploaded by the attacker?
Como a `payload` foi feita em `PHP` o `MIME` seria `x-php` (**caso o atacante não mude**), então pesquisei pela coluna `info` do `Wireshark` já que ela apresenta o `MIME Type`.
```
ip.addr == 111.224.250.131 && http.request.method == POST && _ws.col.info contains "x-php"
```
![[Pasted image 20250416153735.png]]

Conseguimos o nome do script, mas ainda podemos seguir o `Stream HTTP` para ver o conteúdo do `script`. O seu conteúdo é uma `reverse shell`, agora temos certeza que é uma `payload` maliciosa.
![[Pasted image 20250416154033.png]]

**Answer : `NVri2vhp.php`**
***
![[Pasted image 20250416154250.png]]