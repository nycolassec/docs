### 4648(S): A logon was attempted using explicit credentials.

### 4624(S): An account was successfully logged on.

### 4769(S, F): A Kerberos service ticket was requested



***
- ID 2003: The firewall was activated for a profile.
- ID 2004: A new rule was created.
- ID 2005: A rule was modified.
- ID 2006: A rule was deleted.

### Event ID 1116 - A malware was successfully detected

### Event ID 1117 - The antimalware platform performed an action to protect your system from malware.


***

### 4719(S): System audit policy was changed.

---


A ID do evento 4688 é para a criação do processo e inclui a Linha de Comando do Processo. 
A ID do evento 4689 é para o encerramento do processo.

---
O evento "4103" inclui o nome e a saída do cmdlet.
Os eventos "4104" contêm o conteúdo do script. Os scripts que excedem o tamanho máximo da mensagem de um evento são registrados como vários eventos "4104".


# 4698(S): A scheduled task was created

# 1102(S): The audit log was cleared

## O evento com ID 104 da fonte Microsoft-Windows-Eventlog indica que o arquivo de log foi limpo.


---
`Event ID 4625 - Failed Logon`

Other event logs that may aid in password spraying detection include:
- `4768 and ErrorCode 0x6 - Kerberos Invalid Users`
- `4768 and ErrorCode 0x12 - Kerberos Disabled Users`
- `4776 and ErrorCode 0xC000006A - NTLM Invalid Users`
- `4776 and ErrorCode 0xC0000064 - NTLM Wrong Password`
- `4648 - Authenticate Using Explicit Credentials`
- `4771 - Kerberos Pre-Authentication Failed`


- `Event ID 4768 (Kerberos TGT Request)`: Occurs when the client workstation requests a TGT from the KDC, generating this event in the Security log on the domain controller.
- `Event ID 4769 (Kerberos Service Ticket Request)`: Generated after the client receives the TGT and requests a TGS for the MSSQL server's SPN.
- `Event ID 4624 (Logon)`: Logged in the Security log on the MSSQL server, indicating a successful logon once the client initiates a connection to the MSSQL server and logs in using the service account with the SPN to establish the connection.
