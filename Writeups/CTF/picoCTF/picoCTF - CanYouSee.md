#Category : Forensics #Difficult : Easy
***
#### Metadados
Recebemos uma imagem para concluir nossas investigações.
![[Pasted image 20250521132741.png]]

De início podemos verificar os metadados da imagem para ver se há algo de incomum.
```sh
exiftool ukn_reality.jpg
```
![[Pasted image 20250521132948.png]]

Temos uma valor em `base64` na propriedade `Attribution URL`. Podemos então usar um decode para ver seu real valor.
![[Pasted image 20250521133632.png]]
***
![[Pasted image 20250521133729.png]]
