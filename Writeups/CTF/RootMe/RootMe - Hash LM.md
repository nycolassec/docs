#Category : Cryptanalysis #Difficult : VeryEasy
***
## LM hash
Recebemos um arquivo com um dump de credenciais do windows.
![[Pasted image 20250526202652.png]]

Após ter uma ideia de qual é o algorítimo do hash. Faço uma pesquisa no `hashcat` para saber qual o modo correto.
![[Pasted image 20250526202914.png]]

Agora joguei a hash em um arquivo `hash`, quebrei a hash com o `hashcat` e mostrei seu valor com o argumento `--show`.
![[Pasted image 20250526203235.png]]

**Answer :** `admin!!`
***
![[Pasted image 20250526203310.png]]