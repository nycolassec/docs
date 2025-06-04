#Difficult : Easy #Category Forensics
***
#### root Home
Recebemos um arquivo `tar` para concluir nossas investigações.
![[Pasted image 20250522115744.png]]

Após a extração, temos um arquivo de disco `vmdk` e um arquivo de configuração de máquina virtual `ovf`.
![[Pasted image 20250522115931.png]]

Pelo `vmdk` ser um arquivo de disco virtual, podemos abri-lo no `Autopsy` para analisarmos melhor. Nele temos uma partição que contém aos arquivos da raiz do sistema. Começo analisando pelo `/root`, que por ser o usuário administrador, pode conter informações importantes.
![[Pasted image 20250522132443.png]]

Temos então nossa flag no arquivo `.passwd`.
![[Pasted image 20250522132800.png]]
***
![[Pasted image 20250522133031.png]]