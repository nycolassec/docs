```ios
RT(config)# service password-encryption
```
Criptografa todas as senhas no dispositivo.

```
RT(config)# security passwords min-length 8
```
Define um mínimo de caracteres que devem ser usados nas senhas.

```
RT(config)# login block-for 120 attempts 3 within 60
```
Bloqueia o login de um usuário por `120` segundos se houver 3 tentativas de login falhas dentro de 60 segundos.

#### vty
```ios
RT(config)# line vty 0 4
RT(config-line)# password cisco123
RT(config-line)# exec-timeout 5 30
RT(config-line)# transport input ssh 
```
- `exec-timeout 5 30` - Irá desconectar automaticamente se houver inatividade por 5 minutos e 30 segundos.
- `transport input ssh` - Irá permitir apenas conexões via `ssh`
***
#### Password algorithms
Temos 3 algoritmos de criptografia para usarmos:
- `5` - Specifies a MD5 HASHED secret will follow
- `8` - Specifies a PBKDF2 HASHED secret will follow
- `9` - Specifies a SCRYPT HASHED secret will follow 

```ios
RT(config)# enable algorithm-type {  md5 | scrypt | sha256 |  secret  } unencrypted password 
RT(config)# enable algorithm-type scrypt secret cisco123

RT(config)# username name algorithm-type {  md5 | scrypt | sha256 |     secret  } unencrypted password
RT(config)# username cisco algorithm-type scrypt secret cisco123
```






























