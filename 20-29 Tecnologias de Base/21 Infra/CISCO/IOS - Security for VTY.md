#### Banner
```ios
RT(config)# banner {  motd | exec | login } #Proibido acesso não autorizado#
```

#### Defend against DOS
```ios
R1(config)# login block-for 15 attempts 5 within 60
R1(config)# ip access-list standard PERMIT-ADMIN

R1(config-std-nacl)# remark Permit only Administrative hosts    
R1(config-std-nacl)# permit 192.168.10.10
R1(config-std-nacl)# permit 192.168.11.10
R1(config-std-nacl)# exit

R1(config)# login quiet-mode access-class PERMIT-ADMIN   
R1(config)# login delay 10
R1(config)# login on-success log
R1(config)# login on-failure log 
```

#### Login enhancements
Specifically, the **login block-for** command monitors login device activity and operates in two modes:

- **Normal mode** - This is also known as watch mode. The router keeps count of the number of failed login attempts within an identified amount of time.
- **Quiet mode** - This is also known as the quiet period. If the number of failed logins exceeds the configured threshold, all login attempts using Telnet, SSH, and HTTP are denied for the time specified in the **login block-for** command.
- 
```ios
R1(config)# ip access-list standard PERMIT-ADMIN  

R1(config-std-nacl)# remark Permit only Administrative hosts 
R1(config-std-nacl)# permit 192.168.10.10                    
R1(config-std-nacl)# permit 192.168.11.10
R1(config-std-nacl)# exit

R1(config)# login quiet-mode access-class PERMIT-ADMIN 
```

```ios
R1(config)# login delay 3 
```
This command introduces a uniform delay between successive login attempts.

#### Log failed attempts
The number of login attempts before a logging message is generated can be specified using the \[**every** _login_] syntax, where the default _login_ value is 1 attempt. The valid range is from 1 to 65,535.
```ios
Router(config)# login on-success log [every login]
Router(config)# login on-failure log [every login]
```

the **security authentication failure rate** command can be configured to generate a log message when the login failure rate is exceeded.
```
Router(config)# security authentication failure rate threshold-rate log
```

Use the **show login** command to verify the **login block-for** command settings and current mode.
```ios
R1# show login
    A login delay for 10 sec is applied.
    Quiet-Mode access list PERMIT-ADMIN is applied.
 
    Router enabled to watch for login Attacks.
    If more than 5 login failures occur in 60 sec or less,
    login will be disabled for 120 secs.
 
    Router presently in Normal-Mode.
    Current Watch Window
        Time remaining: 5 seconds.
        Login failures for current window: 4.
    Total login failures:4.
```

The **show login failures** command displays additional information regarding the failed attempts, such as the IP address from which the failed login attempts originated.
```
R1# show login failures
Total failed logins: 22
Detailed information about last 50 failures
 
Username    SourceIPAddr    lPort Count TimeStamp
admin       1.1.2.1         23    5     15:38:54 UTC Wed Dec 10 2008
Admin       10.10.10.10     23    13    15:58:43 UTC Wed Dec 10 2008
admin       10.10.10.10     23    3     15:57:14 UTC Wed Dec 10 2008
cisco       10.10.10.10     23    1     15:57:21 UTC Wed Dec 10 2008
 
R1#
```



























