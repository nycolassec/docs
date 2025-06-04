Difficult : #Easy Category : #Network 
***
## Crack hash
Recebemos um tráfego de rede para a análise. Podemos filtrar pelo protocolo `POP`, seguir o `tcp stream` e procurar pela senha ou hash utilizado.
![[Pasted image 20250527084542.png]]

A primeira diz referência a `salt` utilizada e a segunda diz respeito a `hash` da senha. O POP utiliza o MD5 da seguinte forma: `MD5($salt.$hash)`, podemos quebrar com o modo `20` do `hashcat`. A hash tem de estar assim : `$hash:$salt`.
![[Pasted image 20250527084926.png]]

Agora podemos quebrar a hash com o `hashcat`.
![[Pasted image 20250527085034.png]]

**Answer : ** `100%popprincess`

