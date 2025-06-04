#Category : WebClient #Difficult : Easy
***
## Mapping
Após iniciarmos a challenge, somos apresentados a uma aplicação que utiliza o `webpack`. Não achei nada na tela principal, então decidi procurar pelo pelos arquivos carregados.
![[Pasted image 20250526193141.png]]

Como eu sei que primeiramente ele carrega o script `app` vou começar com ele.
![[Pasted image 20250526193109.png]]

Aqui temos o mapeamento do `webpack` que normalmente é utilizado para `debug`, mas por algum motivo veio para produção.
![[Pasted image 20250526193327.png]]

vou acessa-lo e procurar pela nossa flag.
```http
http://challenge01.root-me.org/web-client/ch27/static/js/app.a92c5074dafac0cb6365.js.map
```
![[Pasted image 20250526193627.png]]

**Answer :** `BecauseSourceMapsAreGreatForDebuggingButNotForProduction`
***
![[Pasted image 20250526193747.png]]