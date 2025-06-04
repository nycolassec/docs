Difficult : #Easy Category : #Forensics
***
## Packets
Recebemos um arquivo de tráfego de rede. Primeiro organizei os pacotes por tempo. Então identifiquei que cada pacote tinha um tamanho padrão de 8 bytes, até chegar no pacote `2`. Então a partir do pacote `2` comecei pegando o valor do `TCP Payload`.
![[Pasted image 20250527145530.png]]

O valor de cada pacote representa um valor em hexadecimal. Então peguei de todos e coloquei no `CyberCheff` e usei um decode `base64`.
![[Pasted image 20250527145703.png]]
***
![[Pasted image 20250527145805.png]]