#### Opção 1: `pam_limits` (para sessões interativas)
Fazer com que todos os processos executados pelo usuário Doe sejam executados com uma prioridade baixa e os executados pelo usuário John sejam executados com uma prioridade mais alta (+/- 10).
```sh
$ vim /etc/security/limits.d/90-nice.conf
# Usuário Doe roda com prioridade baixa (nice 10)
Doe       soft    nice     10
Doe       hard    nice     10

# Usuário John roda com prioridade mais alta (nice -10)
John      soft    nice     -10
John      hard    nice     -10

```
- `soft` define o valor padrão do `nice` ao iniciar a sessão.
- `hard` impede que o usuário aumente seu valor de nice além disso
**Limitação**: só vale para shells, cron, SSH interativo e processos iniciados via login PAM.









