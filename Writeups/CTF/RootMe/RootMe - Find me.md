Difficult : #Medium Category : #Forensic 
***
>[!warning] Error
>Meu volatility2 está emitindo alguns erros então acrescentei o `grep -v '\*\*\*'`
## Memory dump
Recebemos um arquivo de dump de memória, primeiro vemos qual profile iremos utilizar.
```
vol2 -f dump imageinfo
```
![[Pasted image 20250527201844.png]]

Comecei vendo a árvore de processos para ter uma visualização geral dos processos.
```
vol2 -f dump --profile=Win7SP1x86_23418 pstree
```
![[Pasted image 20250527202635.png]]

Temos um processo interessante, o `TrueCrypt` é um programa que criptografa arquivos e monta como volumes. Agora vejo quais comandos foram emitidos no terminal. Dois arquivos me chamaram a atenção pelo seu nome
```
vol2 -f dump --profile=Win7SP1x86_23418 cmdline
```
![[Pasted image 20250527203700.png]]
## Truecrypt
Podemos saber se esses dois arquivos foram utilizados com o `Truecrypt` fazendo um `dump` desse processo e procurando pelo nome desses arquivos.
```
vol2 -f dump --profile=Win7SP1x86_23418 memdump --pid 3224 -D ./
strings 3224.dmp | grep -E "(findme|flag\.png)"
```
![[Pasted image 20250527204504.png]]

Agora que sabemos que ele foi utilizado com aqueles arquivos, podemos recuperar a senha do volume com um plugin do volatility.
```
vol3 -f dump windows.truecrypt
```
![[Pasted image 20250527204858.png]]

Agora recuperamos o arquivo `findme`, primeiro precisamos do seu endereço.
![[Pasted image 20250527205122.png]]

Agora fazemos o `dump`.
![[Pasted image 20250527205240.png]]

Agora precisamos utilizar o `truecrypt` para descriptografar esse arquivo. Não consegui fazer funcionar no `kali` então tive que baixa-lo no `windows`. Devemos selecionar o arquivo, selecionar a unidade onde ele vai ser montado, selecionar **Mount** w inserir a sneha.
![[Pasted image 20250528070947.png]]

Após isso, podemos verificar os arquivos no volume.
![[Pasted image 20250528071216.png]]
## Keepass
Tanto a imagem quanto o arquivo de texto mostram que não há `flag` aqui.
![[Pasted image 20250528071408.png]]

Já o arquivo `odt` é um documento tirando dúvidas técnicas sobre o `keepass`.
![[Pasted image 20250528072012.png]]

Então utilizei o `binwalk` para ver se tinha algo a mais nesses arquivos. Na `flag.png` e no `readme.txt` não continha nada, mas no `readme.odt` tínhamos alguns arquivo interessantes. É normal que contenham muitos arquivos, pois são arquivos zipados, mas um arquivo me chamou a atenção.
![[Pasted image 20250528072532.png]]

Então descomprimi o arquivo `odt` e verifiquei qual era o tipo do arquivo `my_safety_box`, ele é um `keepass password database`, então tentei abrir como `keepass2`, mas ele exige uma senha.
![[Pasted image 20250528072926.png]]

Podemos tentar procurar alguma senha no sistema, mas algo que me veio na mente foi fazer o `hashdump` do sistema, para tentar obter alguma senha de usuário.
![[Pasted image 20250528073200.png]]

Consegui as hashes, como tem apenas um usuário não comum, vou tentar quebrar sua senha.
![[Pasted image 20250528073340.png]]

Agora vou tentar utilizar essa senha no arquivo do `keepass`. Funcionou, temos algumas senhas em `Internet`, como são muitas vou exporta-las como `csv`.
![[Pasted image 20250528073717.png]]

Abri com o `neovim` mesmo, tem muitas senhas, talvez sejam valores em `Hex` ou `base64`, então procurei por `=` para ver se achava alguma string `base64`. Então encontrei uma enorme string em `base64`.
![[Pasted image 20250528074041.png]]














***
![[Pasted image 20250527200932.png]]