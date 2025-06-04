#Difficult : Easy #Category : Forensics
***
#### openssl
recebemos vários arquivos para prosseguir com a nossa investigação.
![[Pasted image 20250522155114.png]]

Temos vários arquivos `tar`, que indicam arquivos compactados. Para facilitar o trabalho podemos extrair e mover todos para uma pasta `compacted`.
![[Pasted image 20250522155411.png]]

Temos vários arquivos agora, alguns deles s destacam como `pass.txt` e `flag.enc`.
```sh
ls -lh
```
![[Pasted image 20250522155812.png]]

O `flag.enc` é um arquivo criptografado com o `openssl`, enquanto o 
![[Pasted image 20250522155933.png]]