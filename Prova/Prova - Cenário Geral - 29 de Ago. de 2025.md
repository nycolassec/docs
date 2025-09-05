***
## Topologia
![[Pasted image 20250829160118.png]]
***
## Grupos e usuários

| **Node** | **Object** | **Name**   | **OU/Grupo** | **Password** |
| -------- | ---------- | ---------- | ------------ | ------------ |
| WINSRV1  | User       | LinUser-1  | Domain Users | P@ssw0rd     |
| WINSRV1  | User       | LinUser-2  | Domain Users | P@ssw0rd     |
| WINSRV1  | User       | LinAdmin-1 | LinAdmins    | P@ssw0rd     |
| WINSRV1  | User       | LinAdmin-2 | LinAdmins    | P@ssw0rd     |
| WINSRV1  | Computer   | LINSRV1    | LinServes    |              |
| WINSRV1  | User       | VPNUser    | VPNUsers     | P@ssw0rd     |
| LINSRV1  | User       | admin      | admin        | P@ssw0rd     |
| LINSRV1  | User       | root       | root         | P@ssw0rd     |

***
## NTP
- [x] **LINSRV1** - Será o servidor NTP principal da topologia.
- [x] **FW2** - Irá sincronizar com **LINSRV1**.
- [x] **FW1**,**OUT_CLI** - Irão sincronizar com **FW2**.
- [x] **WINSRV1** - Deverá sincronizar com o **FW1**.
***
## PKI
- [ ] **\*** - Ao acessar qualquer serviço que dependa de certificados não deve ser mostrados erros referente ao certificado.
- [ ] **WINSRV2** - Será a RootCA da topologia.
	- [x] Os pontos de distribuição devem estar devidamente configurados.
	- [ ] Assinará o certificado da SubCA Windows e da SubCA Linux.
	- [ ] Após ser devidamente configurada e ter assinado o certificado das CAs intermediárias, deverá ser desligada.
- [x] **WINSRV1** - Será a SubCA Windows.
	- [x] Os pontos de distribuição devem estar devidamente configurados.
- [ ] **LINSRV1** - Será a SubCA Linux da topologia.
	- [ ] Os pontos de distribuição devem estar devidamente configurados.
***
## IAM
- [x] **WINSRV1** - Servirá como servidor de autenticação geral da topologia.
- [ ] **LINSRV1** - Deverá ingressar no domínio **wsc.local**.
	- [x] Ao entrar no domínio deverá ingressar na OU **LinServers**.
	- [ ] Os usuários **LinUser-1**, **LinUser-2** e membros do grupo **LinAdmins** devem conseguir fazer logon.
	- [ ] Usuários do grupo **LinAdmins** devem ter autenticação baseada em chaves RSA.
- [ ] **FW\*** - **WINSRV1** deve servir como servidor de autenticação.
	- [ ] Deve ser possível logar em ambos os firewalls com usuários do **AD**.
***
## VPN
- [ ] **S2S** - Construa uma VPN Site-to-Site entre os firewalls **FW1** e **FW2** para permitir acesso entre os sites.
	- [ ] A VPN deve ter autenticação baseada em certificados.
	- [ ] Cada firewall utilizará o certificado emitido pela CA em seu site.
	- [ ] Todo tráfego dentro da VPN deve ser permitido.
	- [ ] Não deve ser possível acessar o **SITE-B** fora da VPN.
- [ ] **Remote Access** - Construa uma VPN Remote Access para permitir acesso ao **SITE-A**
	- [ ] Deve ser utilizado certificados emitidos pela SubCA Windows para autenticação.
	- [ ] **WINSRV1** deve ser utilizado como servidor de autenticação através do **LDAP**.
	- [ ] Somente o usuários da OU **VPNUsers** devem autenticar na **VPN**.
***
## Políticas
- [ ] **WINSRV1** - Todas as políticas a seguir devem ser configuradas via **GPO**.
	- [ ] Usuários do grupo **LinAdmins** devem ter o logon negado no **WINCLI**.
	- [ ] A animação de primeiro logon deve ser desativada.
	- [ ] Os certificado da **RootCA Windows**, **SubCA Windows** e **SubCA Linux** devem estar nos certificados confiáveis.
	- [ ] Computadores que ingressarem no domínio devem conseguir um certificado através do **Auto-Enrollment**.
	- [ ] Ao tentar fazer logon em uma máquina do domínio deve ser mostrado um banner com o título **Acesso** e com o texto **Apenas acesso autorizado**.
	- [ ] Deve-se lembrar das 15 senhas anteriores.
	- [ ] Complexidade de senha deve estar habilitada.
	- [ ] O usuário deve trocar sua senha a cada duas semanas.
	- [ ] O usuário deve ser notificado da expiração da senha 3 dias antes.
	- [ ] As credenciais não devem ser armazenadas localmente.
	- [ ] A senha deve ter no mínimo 8 caracteres.
	- [ ] O acesso ao **Control Panel** e ao **Resgistry editing tools** deve ser negado para usuários do grupo **VPNUsers**.
	- [ ] A conta do usuário **Guest** deve ser desabilitada.
	- [ ] Após 3 tentativas de logon com falha, o usuário será bloqueado por 25min e após 25min o número de tentativas falhas será zerada.
	- [ ] Após 20 minutos de inatividade, a tela deve ser bloqueada.
- [ ] **LINSRV1** - As políticas devem ser configuradas através da política de segurança local.
	- [ ] O usuário deve alterar sua senha a cada duas semanas, com 3 dias de aviso e 1 dia de uso após a expiração.
	- [ ] Exigir pelo menos um caractere minúsculo, um maiúsculo e exigir pelo menos um dígito.
	- [ ] Exigir um comprimento mínimo de senha de 10 caracteres.
	- [ ] Configurar o limite de 3 para o número máximo de caracteres consecutivos permitidos da mesma classe.
	- [ ] Bloquear contas após 3 tentativas de login malsucedidas numa duração de 15min por uma duração de 25 min.
	- [ ] Lembrar das 15 senhas anteriores.
	- [ ] Usuário **root** deve ser forçado a usar as políticas.
- [ ] **LINSRV1** - O serviço do SELinux deve estar operando no modo enforcing.
***
## Roteamento
- [ ] **LINSRV1** - O **LINCLI** deve ter acesso site sendo executado em **WINSRV1**.
	- [ ] O roteamento deve ser feito com NAT.
- [ ] **WINSRV1** - O **WINCLI** deve ter acesso ao site sendo executado em **LINSRV1**.
	- [ ] O roteamento deve ser feito com NAT.
***
## Files
- [ ] **WINSRV1** - A pasta `C:\home` será compartilhada como `home`.
	- [ ] Para cada usuário com acesso a share deve ser criado uma pasta com seu nome, essa pasta deve ser mapeada para a unidade **R:** automaticamente quando o usuário fizer logon.
	- [ ] Qualquer acesso bem sucedido a pasta `home` deve ser auditado.
	- [ ] Os usuários só terão acesso a sua pasta  e não a share por completo.
	- [ ] Somente os usuários **LinUser-1** e **LinUser-2** terão acesso a share.
	- [ ] Cada usuário com acesso a share terá 500MB de armazenamento na sua pasta.
	- [ ] Nas pastas dos usuários não deve ser permitido arquivos `.bat`, `.ps1`, `.exe` e `.sh`.
***
## WEB
- [ ] **LINSRV1** - Irá conter um site que responderá pelo FQDN : `www.wsc.local`.
	- [ ] Qualquer acesso via `http` deve ser redirecionado para `https`.
	- [ ] Tentativa de acesso a arquivos com a extensão `.bak` devem retornar erro `Denied`.
	- [ ] Erros `403` devem retornar uma página personalizada.
	- [ ] Rota `/admin` deve requisitar autenticação simples com credenciais `admin:P@ssw0rd`
	- [ ] Os logs de acesso devem ser mandados para **LINCLI**.
- [ ] **LINSRV1** - Irá conter um site que responderá pelo FQDN : `files.wsc.local`.
	- [ ] Irá listar os arquivos presentes na pasta `home` compartilhada por **WINSRV1**.
	- [ ] Apenas listagem, não deve indexar arquivos.
	- [ ] Os logs de acesso devem ser mandados para **LINCLI**.
- [ ] **LINSRV1** - Irá conter um site que responderá pelo FQDN : `web.wsc.local`.
	- [ ] Ele servirá de proxy reverso para o site `site.wsc.local`.
	- [ ] Deve estar acessível apenas para o host `OUT_CLI`.
- [ ] **WINSRV1** - Irá conter um site que responderá pelo FQDN : `site.wsc.local`.
	- [ ] Qualquer acesso via `http` deve ser redirecionado para `https`.
	- [ ] Tentativa de acesso a arquivos com a extensão `.bak` devem retornar erro `Denied`.
	- [ ] Erros `403` devem retornar uma página personalizada.
	- [ ] Rota `/admin` deve requisitar autenticação simples.
***
## Log
- [ ] **LINCLI** - Irá servir como servidor de logs.
	- [ ] Os logs recebidos devem ser organizados de acordo com o hostname e o aplicativo de quem enviou os logs.
	- [ ] Os logs de acesso e de erro referentes ao site `www.wsc.local` devem ser enviados para este servidor.
	- [ ] Os logs de acesso e de erro referentes ao site `files.wsc.local` devem ser enviados para este servidor.
	- [ ] Os logs de erros e de acesso dos recebidos devem ser colocados em arquivos separados.
- [ ] **WINSRV1** - Configure o Performance Monitor para analisar o desempenho deste servidor.
	- [ ] Tempo de processamento.
	- [ ] Memória disponível.
	- [ ] Disco.
	- [ ] ○ Colete as informações a cada 60 segundos por 5 minutos e salve em **C:\performance**.
***
$$\#54\ : Nycolas\ Ramos$$
