There are 16 privilege levels in total, as listed below.
- **Level 0:** Predefined for user-level access privileges. Seldom used, but includes five commands: **disable**, **enable**, **exit**, **help**, and **logout**.
- **Level 1:** The default level for login with the router prompt Router >.
- **Levels 2 -14:** May be customized for user-level privileges.
- **Level 15:** Reserved for the enable mode privileges (**enable** command).

```
Router(config)# privilege mode {level level|reset} command
```
**level** : Optional | **reset** : Optional | **command** : Optional

```ios
R1# conf t
R1(config)# !Level 5 and SUPPORT user configuration
R1(config)# privilege exec level 5 ping
R1(config)# enable algorithm-type scrypt secret level 5 cisco5
R1(config)# username SUPPORT privilege 5 algorithm-type scrypt secret cisco5
R1(config)# !Level 10 and JR-ADMIN user configuration
R1(config)# privilege exec level 10 reload  
R1(config)# enable algorithm-type scrypt secret level 10 cisco10  
R1(config)# username JR-ADMIN privilege 10 algorithm-type scrypt secret cisco10  
R1(config)# !Level 15 and ADMIN user configuration
R1(config)# enable algorithm-type scrypt secret level 15 cisco123
R1(config)# username ADMIN privilege 15 algorithm-type scrypt secret cisco123
```

There are two methods for assigning passwords to the different privilege levels:
- To a user that is granted a specific privilege level, use the **username** _name_ **privilege** _level_ **secret** _password_ global configuration mode command
- To the privilege level, use the **enable secret level** _level password_ global configuration mode command

Use the **username** command to assign a privilege level to a specific user. Use the **enable secret** command to assign a privilege level to a specific EXEC mode password.