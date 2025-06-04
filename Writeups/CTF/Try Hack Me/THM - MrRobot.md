#Difficult : Medium #OS : Linux
***
## Port Scan
Começo sempre fazendo uma enumeração das portas para saber qual é a nossa superfície de ataque.
![[Pasted image 20250526083513.png]]
Aparentemente temos algum serviço web rodando.
## WEB Fuzzing
```sh
ffuf -u http://10.10.2.14/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
```
![[Pasted image 20250526084535.png]]

Como encontrei alguns diretórios interessantes, eu mesmo cancelei. Vou começar pelo `robots.txt` por ser uma pasta padrão.
![[Pasted image 20250526084903.png]]

Temos a nossa primeira chave e um dicionário de palavras `fsocity.dic`. Então baixei e dei uma olhada no dicionário.
![[Pasted image 20250526085015.png]]

Como a lista original era muito grande, retirei as linhas duplicadas.
![[Pasted image 20250526090016.png]]
## Broken Authentication
Temos outro recurso interessante na máquina, o `wp-admin`, que é uma página de autenticação do `Wordpress`.
![[Pasted image 20250526092110.png]]

Quando fazemos uma requisição com usuário o senha incorretos, ele indica na página o que erramos, o que nos possibilita fazer um brute force.
![[Pasted image 20250526092244.png]]

Podemos então pegar os parâmetros da requisição e montar nosso script com o `hydra`.
![[Pasted image 20250526092352.png]]

```sh
hydra -L ./fsocity.list -p '123' 10.10.209.141 http-form-post '/wp-login.php:log=^USER^&pwd=^PASS^&testcookie=1:Invalid username'
```
![[Pasted image 20250526093250.png]]

Já temos nosso usuário `elliot`, agora precisamos da senha. Primeiro precisamos ver qual a mensagem que aparece quando erramos a senha.
![[Pasted image 20250526093633.png]]
```sh
hydra -l 'elliot' -P 'fsocity.list' 10.10.209.141 http-form-post '/wp-login.php:log=^USER^&pwd=^PASS^&testcookie=1:is incorrect'
```
![[Pasted image 20250526093502.png]]

Agora que temos as credenciais, podemos acessar o `WordPress`.
## Initial access
Por ser algo mais comum de se fazer, vou instalar algum plugin de gerenciamento de arquivos e subir uma shell em php.
![[Pasted image 20250526093927.png]]

Podemos ir em **Wordperss Plugin Directory**.
![[Pasted image 20250526094303.png]]

E procurarmos por **File Manager**. Serve qualquer um que possamos utilizar para subir arquivos.
![[Pasted image 20250526094349.png]]

Depois de baixarmos, podemos subir em **Upload Plugin**.
![[Pasted image 20250526094514.png]]

Então instalar o `plugin`.
![[Pasted image 20250526094553.png]]

Peguei uma `rever shell` no site https://www.revshells.com, podemos então subir nossa shell na pasta `wp-content` 
![[Pasted image 20250526095230.png]]

Podemos então subir nosso `listener`, acessar a página `wp-content/rev-shell.php` e esperar a `shell`.
![[Pasted image 20250526095412.png]]

Podemos melhorar a nossa `shell` com os comandos.
```sh
export TERM=xterm
python -c "import pty; pty.spawn('/bin/bash')"
```

Dentro de `/home/robot` temos uma senha em `MD5` e a segunda `key`.
![[Pasted image 20250526095938.png]]

Podemos quebrar essa `hash` no `crackstation`.
![[Pasted image 20250526100049.png]]

Agora podemos logar como `robot`.
![[Pasted image 20250526100137.png]]
## PRIV ESC
Após logar como `robot` fiz uma enumeração de `SUID` e verifiquei se existia algum binário que não fosse comum.
![[Pasted image 20250526100433.png]]

Um que não é muito recorrente é o `nmap`, podemos entrar em seu modo interativo e `spawnar` uma shell.
![[Pasted image 20250526100625.png]]

Ma `home` do `root` temos nossa terceira `key`.
![[Pasted image 20250526100715.png]]
***
![[Pasted image 20250526100758.png]]
