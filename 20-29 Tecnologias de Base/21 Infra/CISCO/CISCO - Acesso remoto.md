```ios
# Verificar suporte SSH
SW# show ip ssh

# Configurar o domínio IP
SW(config)# ip domain-name cisco.com

# Gerar pares de chave SSH
SW(config)# crypto key generate rsa

# Autenticação do usuário
SW(config)# username admin secret ccna

# configurar as linhas vty
SW(config)# line vty 0 15
SW(config-line)# transport input ssh
SW(config-line)# login local
SW(config-line)# exit

# Ativar o ssh versão 2
SW(config)# ip ssh version 2

```