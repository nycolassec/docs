#Difficult : Easy
#Tools : Wireshark, VirusTotal
***
#### Info
Recebemos um arquivo de captura de tráfego de rede para fazermos nossas investigações.
![[Pasted image 20250326130257.png]]
***
#### 1 - What is the IP address used for initial access?
De início, podemos ver as estatísticas das conversas TCP, para ver se há alguma anomalia.
![[Pasted image 20250326132829.png]]

o Stream como `ID 1` tem uma quantidade de pacotes bem maior do que os outros, o que pode indicar uma anomalia na rede. Embora tenha `ID 1` vou começar pelo `ID 0` para ver se há algo interessante, no Stream anterior.
```
tcp.stream eq 0
```
![[Pasted image 20250326133243.png]]
O fluxo tem como destino a porta `443` o que indica ``HTTPS``, por isso após o handshake ``TLS`` toda conversa é criptografada. O que não nos server.

Podemos então avançar para o Stream de `ID 1`.
```
tcp.stream eq 1
```
![[Pasted image 20250326133508.png]]

Aqui temos um fator muito estranho. Apesar da página dizer que o arquivo contém uma `image/gif`, ele envia um arquivo executável.
![[Pasted image 20250326133843.png]]

Vamos exportar esse arquivo e verifica-lo no ``VirusTotal`` para sabermos se é um malware.
![[Pasted image 20250326134102.png]]

Após a análise do `VirusTotal`, é confirmado que o arquivo é um malware
![[Pasted image 20250326134350.png]]

Podemos então dizer que o IP relacionado foi usado para acesso inicial.

**Answer** : 162.252.172.54
***
#### 2 - What is the SHA256 hash of the malware?
No `VirusToatl` na aba `DETAILS`, podemos ver o hash.
![[Pasted image 20250326134524.png]]

**Answer** : 9b8ffdc8ba2b2caa485cca56a82b2dcbd251f65fb30bc88f0ac3da6704e4d3c6
***
#### 3 - What is the Family label of the malware?
No `VirusTotal` na aba `DETECTION` podemos ver qual a família do malware.
![[Pasted image 20250326134901.png]]

**Answer** : Pikabot
***
#### 4 - When was the malware first seen in the wild (UTC)?
No `VirusTotal` na aba `DETAILS` na seção `History`, podemos encontrar.
![[Pasted image 20250326135338.png]]

**Answer** : 2023-05-19 14:01:21
***
#### 5 - The malware used HTTPS traffic with a self-signed certificate. What are the ports, from smallest to largest?
De volta ao `Wireshark` podemos ir em `Statistics` -> `IPv4 Statistics` -> `Destinations and Ports` ,ordena-los pelo `Count` e filtra-los por `tls`.
![[Pasted image 20250326141058.png]]

Podemos ignorar as portas do IP `172.16.1.191`, pois ele que iniciou as conexões. Então nos sobraram 4 portas : ``2078``, ``2222``, ``32999`` e `443`. Mas como a porta `443` é uma porta comum e tem menos conexões, também irei descarta-la.

**Answer** : 2078, 2222, 32999
***
#### 6 - What is the id-at-localityName of the self-signed certificate associated with the first malicious IP?
Aqui, podemos fazer uma correlação dos fluxos TCP e as Portas que foram usadas pelo malware. O fluxo de `ID 2` tem como integrante o IP `45.85.235.39` que está utilizando a porta `2078`. Esse fluxo acontece logo após o download do malware. Então podemos decidir que é ele.
![[Pasted image 20250326143907.png]]

Então podemos seguir esse fluxo, e pesquisar por `id-at-localityName`.
```
tcp.stream eq 2
```
![[Pasted image 20250326144043.png]]

**Answer** : Pyopneumopericardium
***
#### 7 - What is the notBefore time(UTC) for this self-signed certificate?
Como já sabemos em que fluxo está, podemos só pesquisar novamente.
```
tcp.stream eq 2
```
![[Pasted image 20250326144251.png]]

**Answer** : 2023-05-14 08:36:52
***
#### 8 - What was the domain used for tunneling?
Para saber qual domínio foi utilizado, podemos filtrar pelo tráfego `DNS`. Então identificamos o ``IP`` do servidor infectado fazendo múltiplas requisições para o mesmo domínio, alternando apenas o subdomínio, o que se classifica como uma exfiltração via ``DNS``.
![[Pasted image 20250326145246.png]]

**Answer** : steasteel.net
***

![[Pasted image 20250326115444.png]]
