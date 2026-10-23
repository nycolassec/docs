**AAA**
```IOS
(config)# aaa new-model
```
O comando aaa new-model faz com que o nome de usuário e a senha locais no roteador sejam usados ​​na ausência de outras instruções AAA.

### SSH

**Criação de um usuário com privilégio 15**
```IOS
(config)# username cisco privilege 15 secret 0 cisco
```

**Criação da chave RSA e ativação da versão 2 do SSH**
```IOS
(config)# crypto generate rsa
```

**Define a versão do SSH para a versão 2**
```IOS
(config)# ip ssh version 2
```

**Console virtua**
```IOS
(config)# line vty 0 4
```
Indica que iremos configurar os terminais 0 ao 4

**Bloqueio de outras conexões**
```IOS
(config-line)# login local
(config-line)# transport input ssh
```
o `transport input ssh` indica que só serão aceitas as conexões via SSH.