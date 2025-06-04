### Info
Recebemos um arquivo de tráfego de rede para fazermos nossa análise.
***
### 1 - Qual é o IP responsável por conduzir a atividade de varredura de portas?
Em *Statistics > Conversation > TCP* ordenei pelo número das portas e pelo `IP`. O `IP` **10.251.96.4** tentou fazer conexões da porta `1` até a porta `1024`.
![[Pasted image 20250528194651.png]]

**Answer :** `10.251.96.4`
***
### 2 - Qual é o intervalo de portas verificado pelo host suspeito? (Formato: porta-Início-porta-Fim)
Como visto anteriormente as conexões foram feitas da porta `1` até a `1024`.

**Answer :** `1-1024`
***
### 3 - Qual é o tipo de varredura de porta realizada? (formato: xxx xxx)
Após filtrar pelo `IP` do atacante, vi uma longa lista de tentativas de conexão baseada na flag `SYN` o que identifica como `TCP SYN`.
![[Pasted image 20250528195301.png]]

**Answer :** `TCP SYN`
***
### 4 - Após a varredura de portas, o “atacante” avançou na fase de reconhecimento realizando uma enumeração. Qual a porta TCP do serviço enumerado?
Após a enumeração das portas há um grande número de conexões com a porta `80`.
![[Pasted image 20250528195633.png]]

**Answer :** `80`
***
### 5 - Qual ferramenta ele utilizou para isso? (Formato: nome_ferramenta versão)
Primeiro filtrei os pacotes pelo `IP` e protocolo `HTTP`.Então selecionei um pacote e identifiquei a ferramenta no `User-Agent`.
![[Pasted image 20250528200242.png]]

**Answer :** `gobuster 3.0.1`
***
### 6 - Uma outra ferramenta também foi utilizada para tentar realizar um ataque web. Qual? (Formato: nome_ferramenta versão)
Como havia muito tráfego com o método `GET` pela enumeração com o `gobuster`, decidi buscar pelo método `POST`.
![[Pasted image 20250528201221.png]]

**Answer :** `sqlmap 1.4.7`
***
### 7 - Através de qual URI path o atacante conseguiu realizar o upload de um web shell? (Formato: /path)
Tanto no final das requisição do método `POST` há o acesso ao recurso `upload.php`. Podemos seguir o fluxo desse pacote e ver quais ações foram seguidas.
![[Pasted image 20250528201840.png]]

**Answer :** `/upload.php`
***
### 8 - Qual é o nome do web shell que o invasor carregou?
Ainda no `Stream` da questão anterior, podemos ver o nome do arquivo submetido.
![[Pasted image 20250528201918.png]]

**Answer :** `dbfunctions.php`
***
### 9 - Qual é o parâmetro usado no web shell para executar comandos?
Ainda no `Stream` da questão anterior podemos ver qual é o parâmetro verificado.
![[Pasted image 20250528202031.png]]

**Answer :** `cmd`
***
### 10 - Qual é o primeiro comando executado pelo invasor?
Aqui avancei os `Stream` até achar o fluxo onde o invasor usou a `web shell`. No caso 2 `Streams` a frente o invasor usou a `web shell`.
![[Pasted image 20250528202342.png]]

**Answer :** `id`
***
### 11 - Qual linguagem o atacante usa para pegar a revershell
Para ver os comandos executados filtrei pelo método `GET` e pelos pacotes que continham `cmd=` na `url`. Podemos então a linguagem que foi usada para a `rev shell`
![[Pasted image 20250528203333.png]]

**Answer :** `python`
***
### 12 - Qual é a porta TCP que ele utiliza para a conexão shell?
Nesse mesmo pacote podemos apenas ver qual porta foi utilizada pelo para a conexão
![[Pasted image 20250528203536.png]]

**Answer :** `4422`
***
![[Pasted image 20250528192932.png]]