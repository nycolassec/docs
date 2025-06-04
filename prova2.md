the File Services requirements were moved to WinSRV


![[Pasted image 20250604094149.png]]

## TASK
Client 1 and 2 and 3
- [x] DHCP Client  - OK
***
## Firewall
- [x] Set admin user password to be “P@ssw0rd”
- [x] Setup DHCP for LAN clients on PFSense – WinSRV1 should be the DNS Server - OK
- [x] Traffic rules
- [ ] Snort
***
## PKI 
- [ ] GPO - Auto enrollment computers certificates
- [ ] GPO -  Trusted Root CA
***
## WINSRV1
- [x] Create a password policy that requires all domain user’s passwords to be 8 characters in length, must be changed monthly
- [x] Create a fine-grained password policy that requires members of the executive group to have a 10 character long password
- [x] Create a login banner/title that says “WorldSkills Lyon”
- [x] Create a login banner/text that says “authorized access only”
- [x] Create a GPO called “control” that will restrict access to the control panel – which is only applicable to accounting users
- [x] Create a GPO called “registry” that will prevent access to registry editing tools – only applicable to users in Paris
- [x] The client would like you to recommend three other GPO’s which should be created to help better secure the domain. Fill in table 2 in the appendix describing what the effect is of the GPO’s you have chosen, and why you chose these GPO settings over other possible GPO settings. This document should be saved on the desktop of the competitor computer and be sure when finishing that experts are able to find/collect the file.
***
## LINSRV1
- [x] Join LinSRV1 to Lumiere.com domain.
- [x] All other domain machines should be able to resolve the name to IP address of LINSRV1.
- [x] access allowed for domain users C1 and C2 via ssh
- [x] Both C1 and C2 users should be able to use all root level administrative commands through the use of the sudo commands.
- [x] Other users which connect will not have elevated sudo privileges.
- [x] Firewall should be running
- [x] Services for required operation permanently added to the firewall to be operational after a reboot
- [x] Root not allowed to connect via ssh
- [x] Only C1 and C2 users can connect via ssh
- [x] 10 characters in length
- [x] Password changed every month
- [x] Minimum password length of 25 days
- [x] Warn a user their password will expire after 25 days







| Recommended GPO             | Reason                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Configure Automatic Updates | É de conhecimento comum que atacantes assim como nossos sistemas se atualizam a todo instante, por parte dos atacantes essa atualização se deve ao desenvolvimento de novos métodos ou ferramentas para burlar os sistemas de segurança, por parte do sistemas de segurança eles se atualizam para corrigir vulnerabilidades que podem ser exploradas. Tendo em vista de que um colaborador não poderia verificar as atualizações em cada máquina a todo instante, se torna viável criar uma política de grupo que verificasse e atualizasse o sistema assim que possível, mantendo assim um sistema atualizado e seguro. |
| Deny log on Locally         | Apesar de em muitas ocasiões os grupos de trabalho serem divididos de forma física em uma organização, tendo assim seus insumos divididos por localidade, ainda assim temos a possibilidade de tentativa de logon de um usuário não permitido. Destarte a melhor forma de proteger essa superfície de ataque seria negar o logon na máquina a qualquer usuário que seja indevido.                                                                                                                                                                                                                                         |
