#Difficult : Easy #Category : Forensics
***
#### Info
Recebemos um arquivo de tráfego de rede para prosseguir coma s nossas investigações.
![[Pasted image 20250519135929.png]]
***
Ao abrir o arquivo de tráfego de rede vi que não tinha nenhum tráfego referente a `HTTP` mas somente `MySQL`. Ao seguir o fluxo do tráfego (Só tem um fluxo kkk) parece foi enviado códigos em `Base64`, para serem armazenados para reconstruir um binário.
![[Pasted image 20250519143715.png]]

Podemos exportar esse fluxo e tentar reconstruir a mensagem em `Base64`.
![[Pasted image 20250519143940.png]]

Aqui vou usar o `nvim` para retirar as partes que não fazem parte do código em `Base64`. Primeiro as linhas sem padrões, nesse caso as últimas e as primeiras.

Primeiro retiro as 14 primeiras linhas.
```nvim
d14j
```
![[Pasted image 20250519144153.png]]

Agora retiro as 9 últimas linhas.
```nvim
d9k
```
![[Pasted image 20250519144414.png]]

Agora podemos normalizar os códigos em `base64` para que fiquem na mesma linha.
```nvim
:%s/\v.\.{4,}(.*\('|)//g
:%s/\v(\n|'\))//g
```
![[Pasted image 20250519150323.png]]

Podemos então jogar seu conteúdo em algum arquivo.
![[Pasted image 20250519150524.png]]

Podemos ver as strings que estão presentes no binário. Temos uma `url` com o final em `base64`, podemos obter seu conteúdo real também.
![[Pasted image 20250519150603.png]]

```sh
echo "SFRCe2NodW5rNV80bmRfdWRmX2Ywcl9icjM0a2Y0NTd9" | base64 -d
```
![[Pasted image 20250519150713.png]]
***
![[Pasted image 20250519150814.png]]