#### Políticas de senha
O usuário deve alterar sua senha a cada duas semanas, com 3 dias de aviso e 1 dia de uso após a expiração.
```sh
$ vim /etc/login.defs
PASS_MAX_DAYS   14   # Expira em 14 dias
PASS_MIN_DAYS   1    # Pode trocar a qualquer momento
PASS_WARN_AGE   3    # Aviso de expiração 3 dias antes

# Número de dias de grace após expiração da senha
$ vim /etc/default/useradd
INACTIVE=1
```

Configurar a política para um comprimento mínimo de senha de 10 caracteres. Configurar o limite de 3 para o número máximo de caracteres consecutivos permitidos da mesma classe. Exigir pelo menos um caractere minúsculo, um maiúsculo e exigir pelo menos um dígito.
```sh
$ vim /etc/security/pwquality.conf
minlen = 10
maxclassrepeat = 3

lcredit = -1    # Pelo menos 1 letra minúscula
ucredit = -1    # Pelo menos 1 letra maiúscula
dcredit = -1    # Pelo menos 1 dígito numérico
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
Configurar uma política de bloqueio de conta para bloquear contas após 3 tentativas de login malsucedidas por uma duração de 25 minutos.
```sh
$ vim /etc/security/faillock.conf
deny = 3
unlock_time = 1500
fail_interval = 900
```
#### Histórico de senhas
Reusabilidade de Senha – Lembrar das 15 senhas anteriores. Primeiramente localize essa linha ou similar.
```sh
$ vim /etc/pam.d/system-auth
password  sufficient  pam_unix.so sha512 shadow nullok try_first_pass use_authtok
```

Logo abaixo dessa linha, adicione ou modifique  a seguinte:
```sh
$ vim /etc/pam.d/system-auth
password  required  pam_pwhistory.so remember=15 use_authtok
```
***
$$\#54 : Nycolas Ramos$$