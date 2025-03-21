#Difficult : Easy
#Tools : sqlite3
***
#### Info
Aqui recebemos dois arquivos, um arquivo de log e um banco de dados do SQLite.
![[Pasted image 20250321152008.png]]
***
#### 1 - What was the username of the external contractor?

Vamos dar uma olhada primeiro no banco de dados, e listar suas tabelas.
```
sqlite3 phpbb.sqlite3
.tables
```
![[Pasted image 20250321152356.png]]

Muitas tabelas foram listadas, mas uma que me interessa é a de users. Podemos usar o comando `.schema` para ver o campos disponíveis da tabela, e selecionar somente os que nos interessa.

```
.schema phpbb_users
```
![[Pasted image 20250321152653.png]]

Essa tabela tem muitos campos, selecionei os que pareciam fazer mais sentido.
```
SELECT user_id,user_type,username,user_email,user_ip,user_lastvisit,user_login_attempts FROM phpbb_users ;
```
![[Pasted image 20250321153605.png]]
No final da lista temos dois usuários com `contractor` no email, mas somente um tem registro no campo `user_lastvisit`.

**Answer** : apoole1
***
#### 2 - What IP address did the contractor use to create their account?

Podemos obter o endereço IP com a consulta.
```
SELECT user_id,username,user_email,user_ip FROM phpbb_users WHERE username = 'apoole1';
```
![[Pasted image 20250321154046.png]]

**Answer** : 10.10.0.78
***
#### 3 - What is the post_id of the malicious post that the contractor made?

Primeiro precisamos ter uma noção da tabela que guarda os posts.
```
.schema phpbb_posts
```
![[Pasted image 20250321154729.png]]

Ela possui vários campos, então selecionei os campos mais interessantes.
```
ELECT post_id,poster_ip,post_edit_user FROM phpbb_posts;
```
![[Pasted image 20250321155000.png]]

Um deles apresenta o IP do contractor, podemos ver seu conteúdo para validação
```
SELECT post_text FROM phpbb_posts WHERE post_id = 9;
```
![[Pasted image 20250321155802.png]]
O conteúdo do post é um código em HTML, o que pode ser uma tentativa de ``Stored XSS``.

**Answer** : 9
***
#### 4 - What is the full URI that the credential stealer sends its data to?

Para ver para onde ele tentar enviar as credenciais, devemos copiar esse código para um arquivo ``.html`` e abri-lo em um navegador.

Essa página tem alguns links, mas o que me chamou a atenção foi o formulário.
![[Pasted image 20250321162456.png]]

Podemos inspecionar a página para ver seu funcionamento.
![[Pasted image 20250321162635.png]]
O formulário está enviando as credenciais para uma página remota em `http://10.10.0.78/update.php`.

**Answer** : http:\//10.10.0.78/update.php
***
#### 5 - When did the contractor log into the forum as the administrator? (UTC)

Vasculhei o arquivo de log `access.log`, mas não encontrei nada relevante. Até notar que existe uma tabela ``phpbb_log``.

Primeiro olhamos a sua estrutura.
```
.schema phpbb_log
```
![[Pasted image 20250321170215.png]]
 
 Escolhi campos que podemos ser interessantes, e filtrei pelo IPdo atacante.
```
SELECT log_ip,log_data,log_operation,log_time FROM phpbb_log WHERE log_ip = '10.10.0.78';
```
![[Pasted image 20250321170656.png]]

A data e hora não vieram formatadas, isso porque precisamos formata-la. 
 ```
 SELECT log_ip,log_data,log_operation,datetime(log_time,"unixepoch") FROM phpbb_log WHERE log_ip = '10.10.0.78';
```
![[Pasted image 20250321170933.png]]


**Answer** : 26/04/2023 10:53:12
***
#### 6 - In the forum there are plaintext credentials for the LDAP connection, what is the password?

Aqui podemos olhar a tabela `phpbb_config`, pois costumam armazenar credenciais em arquivos de configurações.
```
.schema phpbb_config
```
![[Pasted image 20250321171331.png]]

Então bisquei todos os campos, mas apliquei um filtro em cima do campo `config_name`.
```
SELECT * FROM  phpbb_config WHERE config_name LIKE '%ldap%' ;
```
![[Pasted image 20250321171623.png]]

**Answer** : Passw0rd1
***
#### 7 - What is the user agent of the Administrator user?

Primeiro revisitei a tabela ``phpbb_users`` para descobrir o IP do usuário admin.
```
SELECT username, user_ip FROM  phpbb_users WHERE username LIKE '%admin%';
```
![[Pasted image 20250321172423.png]]

Após isso usei o IP como filtro no arquivo de logs.
```
cat access.log | grep -E '^10.255.254.2'
```
![[Pasted image 20250321172630.png]]

**Answer** : Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.0.0 Safari/537.36
***
#### 8 - What time did the contractor add themselves to the Administrator group? (UTC)

Podemos usar a tabela `phpbb_log` novamente para filtrar essa tabela.
```
SELECT log_ip,log_data,log_operation,datetime(log_time,"unixepoch") FROM phpbb_log WHERE log_ip = '10.10.0.78';
```
![[Pasted image 20250321172905.png]]

**Answer** : 26/04/2023 10:53:51
***
#### 9 - What time did the contractor download the database backup? (UTC)

Podemos buscar no arquivo de logs, filtrei algumas vezes, mas o que me deu a resposta foi filtrar por `backup` e `sql`.
```
cat access.log | grep -E 'backup' | grep -E 'sql'
```
![[Pasted image 20250321173817.png]]

Embora seja ``26/04/2023 12:01:38``, devemos subtrair ``1`` das horas, por causa do fuso horário.

**Answer** : 26/04/2023 11:01:38
***
#### 10 - What was the size in bytes of the database backup as stated by access.log?

Aqui podemos olhar olha novamente a requisição do backup.
```
cat access.log | grep -E 'backup' | grep -E 'sql'
```
![[Pasted image 20250321173817.png]]

**Answer** : 34707

***

![[Pasted image 20250321174332.png]]