#Difficult : Easy #Category : Forensics
***
#### Info
Recebemos um arquivo de tráfego de rede para prosseguirmos com as nossas investigações.
![[Pasted image 20250519115813.png]]
***
Ao abrir o arquivo de tráfego de rede, filtro primeiramente pelo tráfego `HTTP` e as suas requisições, para ter uma visão geral.
![[Pasted image 20250519132639.png]]
Primeiramente o que parece ser uma enumeração de arquivos e depois o que parece ser um `php stream`, o que não é comum e deve ser considerado uma payload maliciosa.
`php://input` : Retorna dados brutos de um POST ou PUT request.

Podemos então seguir seu stream; Vemos então que há vários códigos em `base64`. Como são poucos posso aplicar o decode um a um.
![[Pasted image 20250519133649.png]]
Parece que foi usado compressão com o `DeflateStream`. Podemos descompactar com `CyberCheff`, `python` ou `zli-flate`. 

Após tentar obter o conteúdo da resposta `http` obtive um valor não conhecido, imaginei que fosse o valor `compressed`. Aqui irei optar pelo `CyberCheff`.
![[Pasted image 20250519134254.png]]

No `cyberCheff` usaremos o `From Base64` e `Raw Inflate`.
![[Pasted image 20250519135154.png]]
***
![[Pasted image 20250519135304.png]]