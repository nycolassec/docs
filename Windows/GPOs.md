###### Negar Logon de um grupo a um computador
**Computer Configuration** > **Policies** > **Windows Settings** > **Security Settings** > **Local Policies** > **User Rights Assignment** > **Deny log on Locally**

---
###### Animação de primeiro Logon
**Computer Configuration** > **Policies** > **Administrative Templates** > **System** > **Logon** > **Show first sign-in animation**

---
###### Certificados root permitidos
**Computer Configuration** > **Policies** > **Windows Settings** > **Security Settings** > **Public Key Policies** > **Trusted Root Certification**

###### Auto-Enrollment Certificate
**Computer Configuration** > **Policies** > **Windows Settings** > **Security Settings** > **Public Key Policies** > **Certificates Services Client - Auto-Enrollment**

***
###### Banner no logon
**``Mensagem:``** **Computer Configuration** > **Policies** > **Windows Settings** > **Security Settings** > **Local Policies** > **Security Options** > **Interactive Logon: Message text for users attempting to log on**
![[Pasted image 20250428092155.png]]

**``Título:``** **Computer Configuration** > **Policies** > **Windows Settings** > **Security Settings** > **Local Policies** > **Security Options** > **Interactive Logon: Message title for users attempting to log on**
 
***
###### Políticas de senha
**Computer Configuration** > **Policies** > **Windows Settings** > **Security Settings** > **Account Policies** > **Password Policy**
![[Pasted image 20250428093447.png]]
***
###### Auto Windows Update
**Computer Configuration** > **Policies** > **Administrative Templates** > **Windows Components** > **Windows Update** > **Configure Automatic Updates**
***
###### NTP Service
**Computer Configuration** > **Policies** > **Administrative Templates** > **System** > **Windows Time Service** > **Time Providers** : 

**Configure Windows NTP Client** : Qual será o servidor de ``NTP`` que será usado.
**Enable Windows NTP Client** : Possibilita que o Windows seja um cliente NTP.

```powershell
# Verifica o status
w32tm /query /status

# Força a sincronização
w32tm /resync
```
***
###### Control Panel
**User Configuration** > **Policies** > **Administrative Templates** > **Control Panel** :
***
###### Registry
User Configuration > Administrative Templates > System > Prevent Access to registry editing tools