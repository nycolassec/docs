### Info
Recebemos 2 arquivos de log para começarmos nossas investigações.
***
### 1 - Há alguma evidência que indique uma tentativa de ataque ? Em caso afirmativo, informe o endereço IP do invasor.
No arquivo `access.log` temos um grande número de requisições vindo somente de um `IP` para vários caminhos da aplicação o que indica um `Web Fuzzing`.
![[Pasted image 20250529190057.png]]

Já no arquivo `auth.log` temos um grande número de tentativas de autenticação vindo de um mesmo `IP` o que indicaria um `Brute Force`.
![[Pasted image 20250529190400.png]]

**Answer :** `102.36.116.22`
***
### 2 - Qual é o país de origem do possível invasor ?
Aqui eu costumo usar uma `API` que retorna algumas informações sobre um `IP` informado.
![[Pasted image 20250529190806.png]]

**Answer :** `South Africa`
***
### 3 - Qual protocolo ou serviço o invasor utilizou na tentativa de autenticação ?
No `auth.log` vimos várias tentativas de autenticação pelo `SSH`.
![[Pasted image 20250529190400.png]]

**Answer :** `ssh`
***
### 4 - Quantas tentativas de autenticação falharam no trecho de log recebido ?
Sabendo o padrão de log de falha na autenticação podemos criara uma regex e contar as linhas.
![[Pasted image 20250529191319.png]]

**Answer :** `249`
***
### 5 - Quantas tentativas de autenticação foram bem-sucedidas ?
Sabendo o padrão de autenticação feita com sucesso, filtramos e contamos as linhas
![[Pasted image 20250529191433.png]]

**Answer :** `0`
***
### 6 - Com quais nomes de usuário o invasor tentou fazer login? Se houver mais de um, liste-os em ordem alfabética, separados por vírgulas (sem espaços).
Nem todas as mensagens seguem o mesmo padrão, então tive que filtrar com o `grep` buscar mais de uma coluna com o `cut`.
![[Pasted image 20250529192444.png]]

**Answer :** `admin,administrator,operator,root,simoc`
***
### 7 - Quantas vezes o atacante tentou logar com o usuário admin ?
Aqui filtrei pela mensagem de falha para o usuário `admin`.
![[Pasted image 20250529191126.png]]

**Answer :** `50`
***
### 8 - Quantas vezes o atacante tentou logar com o usuário administrator ?
Aqui filtrei pela mensagem de falha para o usuário `administrator`.
![[Pasted image 20250529191045.png]]

**Answer :** `49`
***
### 9 - Parece que este endereço IP também tentou de alguma maneira atacar outro serviço que rodava no servidor. Qual protocolo ?
No arquivo `access.log` o invasor tentou enumerar a aplicação `WEB`.
![[Pasted image 20250529190057.png]]

**Answer :** `http`
***
### 10 - Qual ferramenta o atacante usou para tentar enumerar a aplicação web ? Cite o nome completo e versão conforme encontrado no log.
Podemos apenas olhar para o final dos primeiros logs.
![[Pasted image 20250529192927.png]]

**Answer :** `Fuzz Faster U Fool v2.0.0-dev`
***
### 11 - Na enumeração, quantos paths foram localizados pelo atacante ? Responda somente referente a status codes `2XX`.
Aqui filtrei pelos logs que fossem requisições `GET`, que continham alguma string de 3 caracteres e que começasse com `2`.
![[Pasted image 20250529193240.png]]

**Answer :** `639`
***
![[Pasted image 20250528192932.png]]


