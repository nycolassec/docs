## **1️⃣ Pré-requisitos**

- Uma **CA** instalada no domínio (Root CA ou SubCA emitindo certificados).
    
- O **template de certificado** para "Web Server" já publicado na CA.
    
- Permissões adequadas no template para o grupo **Domain Computers** (para que o autoenrollment funcione).
    

---

## **2️⃣ Criar e configurar a GPO “certenroll”**

1. No **Servidor AD DS**, abra o **Group Policy Management** (`gpmc.msc`).
    
2. Clique com o botão direito no domínio ou na OU onde quer aplicar a política e escolha **Create a GPO in this domain, and Link it here**.
    
3. Nomeie como **certenroll**.
    
4. Clique com o botão direito na GPO **certenroll** e selecione **Edit**.
    

---
## **3️⃣ Configurar Autoenrollment de certificados**

No Editor de GPO:

### **Para computadores**

```
Computer Configuration  
   → Policies  
      → Windows Settings  
         → Security Settings  
            → Public Key Policies  
               → Certificate Services Client – Auto-Enrollment

```
**Configuração:**
- **Configuration Model:** Enabled
    
- **Renew expired certificates, update pending certificates, and remove revoked certificates** → Marcar ✅
    
- **Update certificates that use certificate templates** → Marcar ✅

***
## **4️⃣ Distribuir o certificado da Root CA**

Ainda na GPO **certenroll**:
```
Computer Configuration  
   → Policies  
      → Windows Settings  
         → Security Settings  
            → Public Key Policies  
               → Trusted Root Certification Authorities

```
- Clique com o botão direito → **Import** → selecione o **certificado da sua Root CA (.cer)**.
    
- Isso garante que **todos os computadores no escopo da GPO** vão confiar na sua CA e não terão erro ao acessar sites internos.

***
## **5️⃣ Configurar o template de certificado para o IIS**

No servidor da CA:

1. Abra o **Certification Authority**.
    
2. Vá em **Certificate Templates** → **Manage**.
    
3. Localize o template **Web Server** (ou crie um duplicado para ajustes).
    
4. Clique com o direito → **Properties** → guia **Security**.
    
5. Conceda:
    
    - **Read** + **Enroll** + **Autoenroll** para o **computador WinSRV3** ou para o grupo que ele pertence.
        
6. Feche e volte à console da CA.
    
7. Clique com o direito em **Certificate Templates** → **New** → **Certificate Template to Issue** → selecione o template.

***
## **6️⃣ Forçar atualização da política e emissão do certificado**

No **WinSRV3**:
```
gpupdate /force
certutil -pulse
```

- O `gpupdate` aplica a GPO.
    
- O `certutil -pulse` força o cliente a buscar certificados via autoenrollment.
    
- Verifique em `certlm.msc` → **Personal → Certificates** se o **certificado do IIS** foi emitido.
