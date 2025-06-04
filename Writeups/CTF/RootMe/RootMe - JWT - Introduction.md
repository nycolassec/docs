Difficult : #Medium Category : #WEB-SERVER
***
## JWT modify
Ao entrar na challenge somos apresentados a uma tela de login, na qual podemos logar como `guest`.
![[Pasted image 20250527135215.png]]

Ao logar listei os `cookies` e vi que temos um token `jwt` para o nosso usuário.![[Pasted image 20250527135514.png]]
Então peguei o token e o submeti no `jwt.io`, o primeiro teste é testar se a aplicação valida o algorítimo. Podemos modifica-lo para `none` e trocar o username para `admin`.
![[Pasted image 20250527140043.png]]

Podemos então trocar o nosso token na aplicação pelo que modificamos e recarregar a página.
![[Pasted image 20250527140407.png]]

**Answer :** `S1gn4tuR3_v3r1f1c4t10N_1S_1MP0Rt4n7`
***
![[Pasted image 20250527140546.png]]