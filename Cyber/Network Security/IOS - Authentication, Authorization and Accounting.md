## Configure local AAA Authentication
#### Authenticate Administrative Access
```
R1(config)# username JR-ADMIN algorithm-type scrypt secret Str0ng5rPa55w0rd
R1(config)# username ADMIN algorithm-type scrypt secret Str0ng5rPa55w0rd  
R1(config)# aaa new-model
R1(config)# aaa authentication login default local-case
R1(config)#
```
#### Authentication methods
Use the aaa authentication login command, shown below, to enable authentication of the console, aux, and vty lines. The default keyword applies authentication to all lines. Alternatively, a custom authentication method can be configured using a list-name.
```
Router(config)# aaa authentication login {default | list-name} method1…[ method4 ]
```
#### Default and Named Methods
```
R1(config)# username JR-ADMIN algorithm-type scrypt secret Str0ng5rPa55w0rd
R1(config)# username ADMIN algorithm-type scrypt secret Str0ng5rPa55w0rd
R1(config)# aaa new-model
R1(config)# aaa authentication login default local-case enable
R1(config)# aaa authentication login SSH-LOGIN local-case
R1(config)# line vty 0 4
R1(config-line)# login authentication SSH-LOGIN
```
#### Fine-Tuning the Authentication Configuration
This command secures AAA user accounts by locking out accounts that have excessive failed attempts.
```
Router(config)# aaa local authentication attempts max-fail [number-of-unsuccessful-attempts]
```

To display a list of all locked-out users, use the **show aaa local user lockout** command in privileged EXEC mode, as shown in the example.
```
R1# show aaa local user lockout
```

The **show aaa sessions** command can be used to show the unique ID of a session, as shown in the example.
```
R1# show aaa sessionsTotal sessions since last reload: 4 Session Id: 1     Unique Id: 175     User Name: ADMIN     IP Address: 192.168.1.10     Idle Time: 0     CT Call Handle: 0
```
## Configure server-based authentication
#### Configure TACACS+ Servers
```
R1(config)# aaa new-model
R1(config)#
R1(config)# tacacs server Server-T
R1(config-server-tacacs)# address ipv4 192.168.1.101
R1(config-server-tacacs)# single-connection
R1(config-server-tacacs)# key TACACS-Pa55w0rd
R1(config-server-tacacs)# exit
R1(config)#
```
#### Configure Radius Servers
```
R1(config)# aaa new-model
R1(config)#
R1(config)# radius server SERVER-R
R1(config-radius-server)# address ipv4 192.168.1.100 auth-port 1812 acct-port 1813
R1(config-radius-server)# key RADIUS-Pa55w0rd
R1(config-radius-server)# exit
R1(config)#
```
#### Authenticate to the AAA Server Configuration Commands
Refer to the example to see command syntax options available with the **aaa authentication login** command.
```
R1(config)# aaa authentication login default ?
R1(config)# aaa authentication login default group ?
```

To configure a method list for the default login to authenticate first using a TACACS+ server, second with a RADIUS server, and finally with a local username database, specify the order with the **aaa authentication login defaul**t command, as highlighted in the example.
```
R1(config)# aaa new-model
R1(config)#
R1(config)# tacacs server Server-T
R1(config-server-tacacs)# address ipv4 192.168.1.100
R1(config-server-tacacs)# single-connection
R1(config-server-tacacs)# key TACACS-Pa55w0rd
R1(config-server-tacacs)# exit
R1(config)#
R1(config)# radius server SERVER-R
R1(config-radius-server)# address ipv4 192.168.1.101 auth-port 1812 acct-port 1813
R1(config-radius-server)# key RADIUS-Pa55w0rd
R1(config-radius-server)# exit
R1(config)#
R1(config)# aaa authentication login default group tacacs+ group radius local-case
```

#### AAA Authorization Configuration
To configure authorization, use the **aaa authorization** command, as shown in the examples below . The authorization type can specify the types of commands or services:

- **network** - for network services such as PPP and SLIP
- **exec** - for User EXEC terminal sessions
- **commands** _level_ - command authorization attempts authorization for all EXEC mode commands, including global configuration commands, associated with a specific privilege level

```
Router(config)# aaa authorization (network | exec | commands level) {default | list-name} method1… [method4]
```

```
R1(config)# aaa authorization exec ?
  WORD Named authorization list.
  default The default authorization list.

R1(config)# aaa authorization exec default ?
  cache             Use Cached-group
  group             Use server-group.
  if-authenticated  Succeed if user has authenticated.
  krb5-instance     Use Kerberos instance privilege maps.
  local             Use local database.
  none              No authorization (always succeeds).
 
R1(config)# aaa authorization exec default group ?
  WORD     Server-group name
  ldap     Use list of all LDAP hosts.
  radius   Use list of all Radius hosts.
  tacacs+  Use list of all Tacacs+ hosts.
```

```
R1(config)# username JR-ADMIN algorithm-type scrypt secret Str0ng5rPa55w0rd
R1(config)# username ADMIN algorithm-type scrypt secret Str0ng5rPa55w0rd
R1(config)# aaa new-model
R1(config)# aaa authorization exec default group tacacs+
R1(config)# aaa authorization network default group tacacs+
```

#### AAA Accounting Configuration
To configure AAA accounting, use the **aaa accounting** command that is shown in the example.

The following three parameters are commonly used **aaa accounting** keywords:
- **network** - Runs accounting for all network-related service requests, including PPP.
- **exec** - Runs accounting for the EXEC shell session.
- **connection** - Runs accounting on all outbound connections such as SSH and Telnet.

```
Router(config)# aaa accounting {network | exec | connection} {default | list-name} {start-stop | stop-only | none } [broadcast] method1...[method4]

R1(config)# aaa accounting exec ?
  WORD Named Accounting list.
  default The default accounting list
```

As with AAA authentication, either the keyword **default** or a _list-name_ can be used.
Next, the record type, or trigger, is configured. The trigger specifies what actions cause accounting records to be updated. Possible triggers include:
- **start-stop** - Sends a "start" accounting notice at the beginning of a process and a "stop" accounting notice at the end of a process.
- **stop-only** - Sends a "stop" accounting record for all cases including authentication failures.
- **none** - Disables accounting services on a line or interface.
```
R1(config)# aaa accounting exec default start-stop ?
  broadcast Use Broadcast for Accounting
  group     Use Server-group
 
R1(config)# aaa accounting exec default start-stop group ?
  WORD    Server-group name
  radius  Use list of all Radius hosts.
  tacacs+ Use list of all Tacacs+ hosts.
```

The example shows an accounting configuration that logs the use of EXEC commands and network connections.
```
R1(config)# username JR-ADMIN algorithm-type scrypt secret Str0ng5rPa5w0rd
R1(config)# username ADMIN algorithm-type scrypt secret Str0ng5rPa55w0rd
R1(config)# aaa new-model
R1(config)# aaa authentication login default group tacacs+
R1(config)# aaa authorization exec default group tacacs+
R1(config)# aaa authorization network default group tacacs+
R1(config)# aaa accounting exec default start-stop group tacacs+
R1(config)# aaa accounting network default start-stop group tacacs+
```









