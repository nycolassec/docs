#### Verificar status
```sh
# Mostra o modo um que o SELinux está operando
$ getenforce

# Mostra informações gerais do SELinux
$ sestatus
```
#### Habilitando
```sh
# Habilita de forma temporária
$ setenforce 1

# Habilita de forma permanente, requer restart
$ vim /etc/selinux/config
SELINUX=enforcing # Pode ser permissive, enforcing ou disabled
```
#### Relabel
Caso o SELinux vier desabilitado, ele precisará rotular(relabel) os arquivos que foram criados durante seu estado de `desabled`, podemos forçar esse rotulamento.
```sh
$ fixfiles -F onboot
```
#### Portas
Portas também precisam ser liberadas pelo SELinux, para que serviços que utilizam portas funcionem corretamente precisamos precisamos definir o rótulo corretamente.
```sh
# Lista todos os tipos possíveis
$ seinfo -t

# Lista os tipos e as portas associadas
$ semanage port -l

# Rotulamos uma porta
$ semanage port -a -t ftp_port_t -p tcp 21

# Exibe o contexto de uma porta em específico
$ seinfo --portcon=21
```
#### Arquivos
```sh
# Lista todos os tipos possíveis
$ seinfo -t

# Lista os tipos e os arquivos associados
$ semanage fcontext -l

# Marcamos /srv/ftp/files e seu conteúdo como public_content_t
$ semanage fcontext -a -t public_content_t '/srv/ftp/files(/.*)?'

# Aplicamos o contexto
$ restorecon -Rv /srv/ftp/files

# Lista o contexto atual
$ ls -lZ /srv/ftp/files
```
##### Permitindo escrita em diretório
```sh
sudo semanage fcontext -a -t public_content_rw_t '/srv/ftp/upload(/.*)?'
sudo restorecon -R -v /srv/ftp/upload
```
#### Logs
Se estiver havendo algum bloqueio podemos ver as mensagens de erro do SELinux.
##### ausearch
ausearch é um comando que filtra logs de auditoria dos `daemons`, `-m avc` indica auditoria do SELinux, `-ts` indica o período, `-c` o daemon e `-i` indica legível.
```sh
# Ele lista todos os bloqueios SELinux do dia.
$ ausearch -m avc -ts today

# Interpreta mensagens AVC para explicar o motivo do bloqueio.
$ ausearch -m avc -ts today | audit2why

# Lista todas as ações bloqueadas hoje em formato legível
$ ausearch -m avc -ts today -i

# Agora filtramos pelo processo vsftpd
$ ausearch -m avc -c vsftpd -i
```
##### Arquivos
```sh
$ grep avc /var/log/audit/audit.log
# ou
$ grep "SELinux is preventing" /var/log/messages
```
***
#### Permissões
###### Reverse Proxy

```sh
$ setsebool -P httpd_can_network_connect 1
```

***
$$\#54 : Nycolas Ramos$$