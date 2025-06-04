#Category : Cryptanalysis #Difficult : VeryEasy
***
Aqui recebemos uma hash para descriptografarmos, ele aparenta ser um sha-256, um hash sha-256 tem extensão de 64 caracteres que vão de `0` a `9` e de `A` a `F`. Quando olhei a string percebi que havia um `k` que obviamente não faz parte da hash.
![[Pasted image 20250527071517.png]]

Após remover a letra `k` submeti a hash no `crackstation`.
![[Pasted image 20250527072102.png]]

Então tirei a hash `SHA-1` desse valor.
![[Pasted image 20250527072758.png]]

**Answer :** `a7c9d5a37201c08c5b7b156173bea5ec2063edf9`
***
![[Pasted image 20250527072850.png]]
