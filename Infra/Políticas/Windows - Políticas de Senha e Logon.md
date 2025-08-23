

#### Políticas de senha
O usuário deve alterar sua senha a cada duas semanas, com 3 dias de aviso e 1 dia de uso após a expiração.
**Computer Configuration** > **Policies** > **Windows Settings** > **Security Settings** > **Account Policies** > **Password Policy**
>Enforce password history : 15
>Maximum password age : 14
>Minimum password age : 1
>Minimum Length : 10
>Password must meet complexity requirements : Enabled
#### Banner / Notificar expiração / Prevenção de cache de credencial
3 dias antes da expiração da senha o usuário deve ser notificado, as credenciais não deverão ser armazenadas localmente e deverá ser mostrado um banner de logon com o título "WorldSkills." e o texto "Somente usuários autorizados.".
**Computer Configuration** > **Policies** > **Windows Settings** > **Security Settings** > **Local Policies** > **Security Options** > 
>Interactive Logon: Message text for users attempting to log on : WorldSkills.
>Interactive Logon: Message title for users attempting to log on : Somente usuários 
>autorizados.
>Interactive Logon: Prompt user to change password before expiration : 3 days
>Interactive logon: Number of previous logons to cache (in case domain controller is not available) : 0
#### Faillock
Após 3 tentativas de logon com falha, o usuário será bloqueado por 25min, e após 25min o número de falhas será zerada.
**Computer Configuration** > **Policies** > **Windows Settings** > **Security Settings** > **Account Policies** > **Account Lockout Policy** >
>Account lockout duration : 25 minutes
>Account lockout threshold: 3 invalid attempts
>Reset account lockout counter after : 25 minutes
#### Bloquear tela após 20min de inatividade
Após 20 minutos de inatividade, a tela será bloqueada
**User Configuration** > **Policies** > **Administrative Templates** > **Control Panel** > **Personalization** >
>Enable screen saver : Enable
>Screen saver timeout : 1200
>Force specific screen saver : Enable
>**>** Options : scrnsave.scr
>Password protect the screen saver : Enable
***
$$\#54 : Nycolas Ramos$$