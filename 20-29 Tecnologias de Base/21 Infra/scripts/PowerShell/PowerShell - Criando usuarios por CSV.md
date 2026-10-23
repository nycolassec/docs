```powershell
New-ADUser -name 'Atylas Ramos' -SamAccountName atylasRamos -UserPrincipalName atylasramos@empresa.local -Path "OU=Users,OU=Vendas,DC=empresa,DC=local" -AccountPassword (ConvertTo-SecureString -AsPlainText 'Senha123' -force) -Enable $true
```

### Meu
```powershell
-Organization 'Ti'

-Name 'Nycolas'
-SurName 'Ramos'
-SamAccountName 'nycolasramos'
-UserPrincipalName 'nycolas.ramos@corp.com'
-EmailAddress 'nycolas.ramos@corp.com'


-AccountPassword (ConvertTo-SecureString -AsPlainText 'P@ssw0rd' -force)
-Path "OU=Ti,DC=corp,DC=com"
-Enable 1
```

> [!important] O nome que será usado para logar será o SamAccountName

###### Exemplo
```powershell
New-ADUser
-Organization 'Ti'
-Name 'Nycolas'
-SurName 'Ramos'
-SamAccountName 'nycolasRamos'
-UserPrincipalName 'nycolas.ramos@corp.com'
-EmailAddress 'nycolas.ramos@corp.com'
-AccountPassword (ConvertTo-String -AsPlainText 'P@ssw0rd' -force) -Path "OU=Ti,DC=corp,DC=com"
-Enable 1
```
![[Windows-Images-Image-01.png]]

---
### Usando um CSV

|   |   |   |
|---|---|---|
|**FirstName**|**LastName**|**SamAccountName**|
|Fernando|Machado|fmachado|
|Jeferson|Cardouso|jcardouso|
|Luis|Marconi|lmardoni|
|Daniel|Veronesi|dveronesi|
```powershell
 $secpass = "Password" -AsSecureString

Import-Csv nomes.csv |
      foreach {
               $name = "$($_.LastName) $($_.FirstName)"
               
               New-ADUser 
               -GivenName $($_.FirstName) \
               -Surname $($_.LastName) \
               -Name $name \
               -SamAccountName $($_.SamAccountName) \
               -UserPrincipalName "$($_.SamAccountName)@devmedia.com" \
               -AccountPassword $secpass \
               -Path "cn=Users,dc=Devmedia,dc=com" \
               -Enabled:$true
      }
 ```

---
### Outros valores possíveis
```powershell
      New-ADUser
      -Name "MACHADO Fernando" 
      -SamAccountName fmachado `
      -GivenName "Fernando" 
      -Surname "Machado" `
      -DisplayName "Fernando Machado" 
      -UserPrincipalName "fmachado@devmedia.com" `
      -AccountPassword $secpass 
      -ChangePasswordAtLogon:$true `
      -Path "cn=Users,dc=Devmedia,dc=com" 
      -Enabled:$true `
      -Description "Teste de usuário com diversas opções" `
      -Manager fmachado 
      -Organization "DEVMEDIA" `
      -Company "DEVMEDIA" 
      -Department "IT" 
      -Division "AD Admin" `
      -Title "AD administrator" `
      -Office "DEVMEDIA" 
      -StreetAddress "Rua Fernando Pessoa" `
      -City "São Paulo" 
      -State "São Paulo" `
      -Country "GB" 
      -PostalCode "92110100" 
      -POBox "1234" `
      -OfficePhone "011 34345768" `
      -HomePhone "011 40981232" 
      -MobilePhone "011 34096098" `
      -HomeDrive "H:" 
      -HomeDirectory "\\server1\Home\fmachado" `
      -ProfilePath "\\server2\profiles\wsc" 
      -ScriptPath "logon.vbs"
```

***
$$\#54 : Nycolas Ramos$$