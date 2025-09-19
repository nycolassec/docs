#### 1 - Qual o endereço IP do atacante?
Após fazer um filtro para identificar acessos web, foi identificado um número muito grande de requisições vindo de um mesmo endereço IP `192.168.10.118` com o **User-Agent** da ferramenta de fuzzing **Fuzz Faster U Fool v1.1.0**.

**Filtro:** `agent.name: Nessus AND decoder.name: web-accesslog`
**Answer :** `192.168.10.118`
***
#### 2 - Qual protocolo/porta o hacker utilizou para iniciar a exploração? Ex: SNMP/161
Aqui podemos filtrar por tentativas de logon tanto bem (`4624`) quanto mal (`4625`) sucedidas. Vemos que o IP do atacante 

**Answer :** `smb/445`
***
#### 3 - Qual o nome de usuário/conta o hacker utilizou para o acesso inicial?


**Answer :** `Guest`
***
#### 4 - Qual horário o hacker realizou o primeiro acesso? Ex: 2025-01-01T15:15:15Z


**Answer :** `2025-06-09T12:35:39Z`
***
#### 5 - Como o hacker obteve acesso na aplicação web em execução? Ex: crosssitescripting (Somente o nome do ataque tudo junto)


**Answer :** `bruteforce`
***
#### 6 - Qual horário o hacker conseguiu fazer login na aplicação web? Ex: 2025-01-01T15:15:15Z


**Answer** : `2025-06-09T12:41:59Z`
***
#### 7 - Qual o usuário utilizado pelo hacker na aplicação web?


**Answer** : `jose`
***
#### 8 - Quanto tempo de ataque foi necessário para que o hacker obtesse credenciais válidas? Ex: 7 segundos


**Answer** : `39 segundos`
***
#### 9 - Qual a porta e protocolo utilizada pelo atacante na configuração de proxy do nessus? Ex: 666/tcp

**Answer** : `8080/tcp`
***
#### 10 - Qual horário o hacker fez o primeiro acesso interativo a máquina, possibilitando a execução de comandos? Ex: 2025-01-01T15:15:15Z


**Answer** : `2025-06-09T12:53:33Z`
***
#### 11 - Quais foram os 3 primeiros comandos executados diretamente pelo hacker? Ex: cmd1,cmd2 arg1,cmd3 arg3


**Answer** : `whoami,whoami /all,cmdkey /list`
***
#### 12  -Qual o primeiro executável que foi dado como malicioso pelo Windows Defender e qual o horário que foi detectado? Ex: joao.exe_2025-01-01T15:15:15Z

**Answer** : `win.exe_2025-06-09T12:56:32Z`
***
#### 14 - Qual o primeiro comando malicioso executado para persistência/escalação de privilégios?


**Answer** : `net user pwn pwn /add`
***
#### 15 - Qual nome do processo vítima? vitor.exe


**Answer :** `nessusd.exe`
***
#### 16 - Qual o nome da técnica de persistência/escalação de privilegios o hacker utilizou? Ex: nome_da_tecnica

**Answer**
***
#### 18 - Qual horário o hacker estabelece a primeira sessão privilegiada com o servidor vítima? Ex: 2025-01-01T15:15:15Z


**Answer** : `2025-06-09T13:35:42Z`
***
#### 19 - Qual arquivo o hacker utiliza para obter informações sensíveis do sistema? Ex: arquivo.extensao


**Answer :** `mimi.exe`
***


powershell.exe_
svchost.exe_2025-06-09T12:53:36Z
2025-06-09T12:53:33Z




***


agent.name: Nessus AND 192.168.10.118 AND data.win.system.eventID: (4624 OR 4625)