#### Políticas de senha
O usuário deve alterar sua senha a cada duas semanas, com 3 dias de aviso e 1 dia de uso após a expiração.
```sh
$ vim /etc/login.defs
PASS_MAX_DAYS   14   # Expira em 14 dias
PASS_MIN_DAYS   1    # Tem que usar a senha pelo menos um dia
PASS_WARN_AGE   3    # Aviso de expiração 3 dias antes

# Número de dias de grace após expiração da senha para novos usuários
$ vim /etc/default/useradd
INACTIVE=1
# ou
$ sudo useradd -D -f 1

# Número de dias de grace após expiração da senha para usuário específico
$ sudo chage -I 1 old_user
```

Configurar a política para um comprimento mínimo de senha de 10 caracteres. Configurar o limite de 3 para o número máximo de caracteres consecutivos permitidos da mesma classe. Exigir pelo menos um caractere minúsculo, um maiúsculo e exigir pelo menos um dígito.
```sh
$ vim /etc/security/pwquality.conf
minlen = 10
maxclassrepeat = 3

lcredit = -1     # Pelo menos 1 letra minúscula
ucredit = -1     # Pelo menos 1 letra maiúscula
dcredit = -1     # Pelo menos 1 dígito numérico
enforce_for_root # Forçamos as políticas para o root
```
#### Banner de Logon
###### Pré-login
```sh
$ vim /etc/issue.net
WorldSkills.
Somente usuários autorizados.

$ vim /etc/ssh/sshd_config
Banner /etc/issue.net
```
###### Pós-login
```sh
$ vim /etc/motd
Nunca desligue o servidor.
Agradecido :| :| :| :| :| :|
```
###### Login via console local
```sh
$ vim /etc/issue
WorldSkills.
Somente usuários autorizados.
```
#### Faillock
Configurar uma política de bloqueio de conta para bloquear contas após 3 tentativas de login malsucedidas numa duração de 15min por uma duração de 25 min.
```sh
$ vim /etc/security/faillock.conf
deny = 3
unlock_time = 1500
fail_interval = 900
```
#### Histórico de senhas
Reusabilidade de Senha – Lembrar das 15 senhas anteriores.
```sh
$ vim /etc/security/pwhistory.conf
remember=15
enforce_for_root
```
***
$$\#54 : Nycolas Ramos$$