#### Info
#Category : Network Forensics
#Difficult : Medium
***
#### 1 - By identifying the C2 IP, we can block traffic to and from this IP, helping to contain the breach and prevent further data exfiltration or command execution. Can you provide the IP of the C2 server that communicated with our server?
Podemos começar vendo as estatísticas das conversas, para termos uma noção geral do tráfego.

Aqui temos alguns `IOCs` como o alto número de pacotes e a porta `8000` que é incomum.
![[Pasted image 20250414180850.png]]

Podemos então começar a seguir os fluxos.
**`Stream 0`**
![[Pasted image 20250414181149.png]]
Aqui temos o início da comunicação com um servidor com o `Apache ActiveMQ`. E uma requisição para um `IP` na porta `8000`, que é a porta padrão de quando iniciamos um servidor web `Python`.

**`Stream 1`**
![[Pasted image 20250414181405.png]]
No `Stream 1` o servidor faz uma requisição para um `IP` que está rodando um servidor web Python com um arquivo `xml` que tem uma payload.

**Answer : `146.190.21.92`**
***
#### 2 - Initial entry points are critical to trace back the attack vector. What is the port number of the service the adversary exploited?
Nas estatísticas das conversas temos a resposta. O ataque iniciou na porta `61616`.
![[Pasted image 20250414180850.png]]

**Answer : `61616`**
***
#### 3 - Following up on the previous question, what is the name of the service found to be vulnerable?
O nome do servidor está descrito no `Stream 0`, mas também podemos pesquisar pelo número da porta `61616` e ver o serviço associado.

**`Stream 0`**
![[Pasted image 20250414181149.png]]

**Answer : `Apache ActiveMQ`**
***
#### 4 - The attacker's infrastructure often involves multiple components. What is the IP of the second C2 server?
Podemos encontrar o segundo `C2` no `Stream 1`.

**`Stream 1`**
![[Pasted image 20250414181405.png]]
A payload enviada pelo atacante aponta para outro ``IP``.

**Answer : `128.199.52.72`**
***
#### 5 - Attackers usually leave traces on the disk. What is the name of the reverse shell executable dropped on the server?
Ainda no `Stream 1` podemos ver qual foi o arquivo de saída executado pela `pauload`.

**`Stream 1`**
![[Pasted image 20250414181405.png]]

**Answer : `docker`**
***
#### 6 - What Java class was invoked by the XML file to run the exploit?
Ainda no `Stream 1` podemos achar a classe utilizada no arquivo `xml` que continha a payload.

**`Stream 1`**
![[Pasted image 20250414181405.png]]

**Answer : `java.lang.ProcessBuilder`**
***
### 7 - To better understand the specific security flaw exploited, can you identify the CVE identifier associated with this vulnerability?
Após uma breve pesquisa, podemos achar a `CVE` relacionada a essa vulnerabilidade.
![[Pasted image 20250414183255.png]]

Como só apareceu essa `CVE` não analisei ela, mas caso tivesse encontrado mais de uma, poderíamos analisar uma por uma.

**Answer : `CVE-2023-46604`**
***
#### 8 - The vendor addressed the vulnerability by adding a validation step to ensure that only valid `Throwable` classes can be instantiated, preventing exploitation. In which **Java class** and **method** was this validation step added?
Aqui primeiro pesquisamos a `CVE-2023-46604` e vamos no site da `NVD`. Que descreve a vulnerabilidade junto com mais algumas informações.
![[Pasted image 20250414185832.png]]

Podemos então ir em `Vendor Advisory`.
![[Pasted image 20250414185929.png]]

No final temos um ticket do `Jira` sobre essa vulnerabilidade. Podemos seguir esse ticket para ver como ele se desenrolou.
![[Pasted image 20250414190050.png]]

Temo um link do `GitHub` que aparentemente aponta para o patch de correção da vulnerabilidade.
![[Pasted image 20250414190153.png]]

Podemos então seguir o commit que segue esse `pull request`.
![[Pasted image 20250414190320.png]]

Temo então onde foi implementado a verificação.
![[Pasted image 20250414190552.png]]

**Answer : `BaseDataStreamMarshaller.createThrowable`**
***

![[Pasted image 20250414190759.png]]