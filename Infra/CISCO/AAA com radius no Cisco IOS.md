```ios
Router> enable
Router# conf t
Router(config)# username cisco secret cisco
Router(config)# aaa new-model

Router(config)# radius server WIN-SERVER
Router(config-radius-server)# address ipv4 10.0.10.5 auth-port 1812 acct-port 1813
Router(config-radius-server)# key cisco
Router(config-radius-server)# exit

Router(config)# aaa group server radius RADIUS-GROUP
Router(config-sg-radius)# server name WIN-SERVER
Router(config-sg-radius)# exit

Router(config)# aaa authentication login default group RADIUS-GROUP local
Router(config)# aaa authorization exec default group RADIUS-GROUP local
Router(config)# aaa accounting exec default start-stop group RADIUS-GROUP

Router(config)# radius-server attribute 6 on-for-login-auth  ! Service-Type
Router(config)# radius-server attribute 8 include-in-access-req  ! Framed-IP-Address
Router(config)# radius-server vsa send authentication

Router(config)#console e vty
Router(config)#
Router(config)#
```