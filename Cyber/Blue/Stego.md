## Imagens
#### Tamanho modificado
Para esconder mensagens, alguém pode criar uma imagem e modificar os bytes referente as dimensões da imagem. Para conseguirmos fazer essa mudança temos que achar o seu [JPEG makers](https://en.wikipedia.org/wiki/JPEG#Syntax_and_structure).
![[Pasted image 20250627192846.png]]
 
 No nosso caso são os bytes `FF C0`, após esses bytes será indicado as dimensões. Devemos então achar esses bytes e fazer a devida modificação.
![[hex.png]]

Podemos fazer esse processo no cybercheff. Primeiro submetemos a imagem e transformamos seu valor em hexadecimal.
![[Pasted image 20250627193415.png]]

Após isso usamos a saída em hexadecimal como input e renderizamos a imagem.
![[Pasted image 20250627194615.png]]

Como podemos ver a imagem é muito pequena, modificar seus bytes de dimensão e observar cada nova renderização. Eu costumo usar saltos de valores. Começo com FF pois a menos que esse seja o valor real, irá sobrar algum espaço sem a imagem, a partir daí posso ir diminuindo.
![[Pasted image 20250627195008.png]]

Mais um salto de valor. Parece que estamos perto.
![[Pasted image 20250627195129.png]]

Por fim, achamos.
![[Pasted image 20250627195416.png]]

****