#Difficult : Medium #Category : Forensics
***
#### Search
Recebemos um arquivo `APK` para concluir nossas investigações.
![[Pasted image 20250521155055.png]]

Pela saída do `file` esse arquivo é na verdade um arquivo `zip`. Podemos então extrair e ver os arquivos que foram zipados.
![[Pasted image 20250521155242.png]]

Conseguimos vários arquivos, por uma questão de conveniência, podemos pesquisar por algo como `pico`, `ctf`, `flag` ou algo do gênero. Caso haja algum arquivo com esse nome, podemos ver seu conteúdo.
![[Pasted image 20250521162123.png]]

O conteúdo arquivo estava em `HEX`, podemos ver seu real conteúdo pelo `xxd`.
![[Pasted image 20250521162221.png]]
***
![[Pasted image 20250521162328.png]]
