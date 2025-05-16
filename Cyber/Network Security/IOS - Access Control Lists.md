## Introduction to access control lists
ACLs number 1 to 99, or 1300 to 1999 are standard ACLs while ACLs number 100 to 199, or 2000 to 2699 are extended ACLs, as shown in the output.
```
R1(config)# access-list ?
  <1-99>       IP standard access list
  <100-199>    IP extended access list
  <1100-1199>  Extended 48-bit MAC address access list
  <1300-1999>  IP standard access list (expanded range)
  <200-299>    Protocol type-code access list
  <2000-2699>  IP extended access list (expanded range)
  <700-799>    48-bit MAC address access list
  rate-limit   Simple rate-limit specific access list
  template     Enable IP template acls
R1(config)# access-list
```

The **ip** **access-list** global configuration command is used to create a named ACL, as shown in the following example.
```
R1(config)# ip access-list extended FTP-FILTER
R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 any eq ftp
R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 any eq ftp-data
R1(config-ext-nacl)#
```

## Wildcard masking
In this example, assume you wanted an ACE in ACL 10 to permit network access for the 14 users in the subnet 192.168.3.32/28. Subtract the subnet (i.e., 255.255.255.240) from 255.255.255.255, as shown in the table.
This solution produces the wildcard mask 0.0.0.15. Therefore, the ACE would be **access-list 10 permit 192.168.3.32 0.0.0.15.**

| Starting Value             | 255.2555.255.255  |
| -------------------------- | ----------------- |
| Subtracted the subnet mask | - 255.255.255.240 |
| Resulting wildcard mask    | 0.0.0.15          |

In this example, assume you needed an ACE in ACL 10 to permit only networks 192.168.10.0 and 192.168.11.0. These two networks could be summarized as 192.168.10.0/23 which is a subnet mask of 255.255.254.0. Again, you subtract 255.255.254.0 subnet mask from 255.255.255.255, as shown in the table.
This solution produces the wildcard mask 0.0.1.255. Therefore, the ACE would be **access-list 10 permit 192.168.10.0 0.0.1.255.**

| Starting Value             | 255.2555.255.255 |
| -------------------------- | ---------------- |
| Subtracted the subnet mask | -255.255.254.0   |
| Resulting wildcard mask    | 0.0.1.255        |
#### Wildcard mask keywords
The two keywords are:
- **host** - This keyword substitutes for the 0.0.0.0 mask. This mask states that all IPv4 address bits must match to filter just one host address.
- **any** - This keyword substitutes for the 255.255.255.255 mask. This mask says to ignore the entire IPv4 address or to accept any addresses.

For example, in the command output, two ACLs are configured. The ACL 10 ACE permits only the 192.168.10.10 host and the ACL 11 ACE permits all hosts.
```
R1(config)# access-list 10 permit 192.168.10.10 0.0.0.0
R1(config)# access-list 11 permit 0.0.0.0 255.255.255.255
R1(config)#
```

Alternatively, the keywords **host** and **any** could have been used to replace the highlighted output.
The following commands accomplishes the same task as the previous commands.
```
R1(config)# access-list 10 permit host 192.168.10.10
R1(config)# access-list 11 permit any
R1(config)#
```
## Configure ACLs
#### Numbered Standard IPv4 CAL Syntax
To create a numbered standard ACL, use the following global configuration command:
```
Router(config)# access-list access-list-number {deny | permit | remark text} source [source-wildcard] [log]
```
Use the **no access-list** _access-list-number_ global configuration command to remove a numbered standard ACL.
#### Named Standard IPv4 ACL Syntax
Naming an ACL makes it easier to understand its function. To create a named standard ACL, use the following global configuration command:
```
Router(config)# ip access-list standard access-list-name
```
**Note:** Use the **no ip access-list** **standard** _access-list-name_ global configuration command to remove a named standard IPv4 ACL.

The three highlighted options are configured similar to the numbered standard ACL. Unlike the numbered ACL method, there is no need to repeat the initial **ip access-list** command for each ACE.
```
R1(config)# ip access-list standard NO-ACCESSR1(config-std-nacl)# ?Standard Access List configuration commands:  <1-2147483647>  Sequence Number  default         Set a command to its defaults  deny            Specify packets to reject  exit            Exit from access-list configuration mode  no              Negate a command or set its defaults  permit          Specify packets to forward  remark          Access list entry commentR1(config-std-nacl)#
```

The procedural steps for configuring extended ACLs are the same as for standard ACLs.
To create a numbered extended ACL, use the following global configuration command:
```
Router(config-if)# ip access-group {access-list-number | access-list-name} {in | out}
```
Use the **no access-list** _access-list-number_ global configuration command to remove an extended ACL.

The command to apply an extended IPv4 ACL to an interface is the same as the command used for standard IPv4 ACLs.
```
Router(config-if)# ip access-group {access-list-number | access-list-name} {in | out}
```

To remove an ACL from an interface, first enter the **no ip access-group** interface configuration command. To remove the ACL from the router, use the **no access-list** global configuration command.
#### Protocols and Port Numbers

**Protocol Options**
**Note:** If an internet protocol is not listed, then the IP protocol number could be specified. For instance, the ICMP protocol number 1, TCP is 6, and UDP is 17.
```
R1(config)# access-list 100 permit ? 
  <0-255>       An IP protocol number
  ahp           Authentication Header Protocol
  dvmrp         dvmrp
  eigrp         Cisco's EIGRP routing protocol
  esp           Encapsulation Security Payload
  gre           Cisco's GRE tunneling
  icmp          Internet Control Message Protocol
  igmp          Internet Gateway Message Protocol
  ip            Any Internet Protocol
  ipinip        IP in IP tunneling
  nos           KA9Q NOS compatible IP over IP tunneling
  object-group  Service object group
  ospf          OSPF routing protocol
  pcp           Payload Compression Protocol
  pim           Protocol Independent Multicast
  tcp           Transmission Control Protocol
  udp           User Datagram Protocol
R1(config)# access-list 100 permit 
```

**Port Keyword Options**
Port names or number can be specified. However, port names make it easier to understand the purpose of an ACE. Notice how some common ports names (e.g., SSH and HTTPS) are not listed. For these protocols, port numbers will have to be specified.
```
R1(config)# access-list 100 permit tcp any any eq ? 
  <0-65535>    Port number
  bgp          Border Gateway Protocol (179)
  chargen      Character generator (19)
  cmd          Remote commands (rcmd, 514)
  daytime      Daytime (13)
  discard      Discard (9)
  domain       Domain Name System (53) 
  echo         Echo (7)
  exec         Exec (rsh, 512)
  finger       Finger (79)
  ftp          File Transfer Protocol (21) 
  ftp-data     FTP data connections (20) 
  gopher       Gopher (70)
  hostname     NIC hostname server (101)
  ident        Ident Protocol (113)
  irc          Internet Relay Chat (194)
  klogin       Kerberos login (543)
  kshell       Kerberos shell (544)
  login        Login (rlogin, 513)
  lpd          Printer service (515)
  msrpc        MS Remote Procedure Call (135)
  nntp         Network News Transport Protocol (119)
  onep-plain   Onep Cleartext (15001)
  onep-tls     Onep TLS (15002)
  pim-auto-rp  PIM Auto-RP (496)
  pop2         Post Office Protocol v2 (109)
  pop3         Post Office Protocol v3 (110) 
  smtp         Simple Mail Transport Protocol (25) 
  sunrpc       Sun Remote Procedure Call (111)
  syslog       Syslog (514)
  tacacs       TAC Access Control System (49)
  talk         Talk (517)
  telnet       Telnet (23) 
  time         Time (37)
  uucp         Unix-to-Unix Copy Program (540)
  whois        Nicname (43)
  www          World Wide Web (HTTP, 80) 
```

#### Examples
Extended ACLs can filter on different port number and port name options. This example configures an extended ACL 100 to filter HTTP traffic. The first ACE uses the **www** port name. The second ACE uses the port number **80**. Both ACEs achieve exactly the same result.
```
R1(config)# access-list 100 permit tcp any any eq wwwR1(config)#  !or...R1(config)# access-list 100 permit tcp any any eq 80
```

Configuring the port number is required when there is not a specific protocol name listed such as SSH (port number 22) or an HTTPS (port number 443), as shown in the next example.
```
R1(config)# access-list 100 permit tcp any any eq 22R1(config)# access-list 100 permit tcp any any eq 443R1(config)#
```
#### TCP Established Extended ACL
```
R1(config)# access-list 120 permit tcp any 192.168.10.0 0.0.0.255 establishedR1(config)# interface g0/0/0 R1(config-if)# ip access-group 120 out R1(config-if)# endR1# show access-lists Extended IP access list 110     10 permit tcp 192.168.10.0 0.0.0.255 any eq www     20 permit tcp 192.168.10.0 0.0.0.255 any eq 443 (657 matches)Extended IP access list 120     10 permit tcp any 192.168.10.0 0.0.0.255 established (1166 matches)R1#
```
#### Named Extended IPv4 ACL Syntax
Naming an ACL makes it easier to understand its function. To create a named extended ACL, use the following global configuration command:
```
Router(config)# ip access-list extended access-list-name
```

In the example, a named extended ACL called NO-FTP-ACCESS is created and the prompt changed to named extended ACL configuration mode. ACE statements are entered in the named extended ACL sub configuration mode.
```
R1(config)# ip access-list extended NO-FTP-ACCESSR1(config-ext-nacl)#
```
#### Named Extended IPv4 ACL Example
```
R1(config)# ip access-list extended SURFINGR1(config-ext-nacl)# Remark Permits inside HTTP and HTTPS traffic R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 any eq 80R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 any eq 443R1(config-ext-nacl)# exitR1(config)# R1(config)# ip access-list extended BROWSINGR1(config-ext-nacl)# Remark Only permit returning HTTP and HTTPS traffic R1(config-ext-nacl)# permit tcp any 192.168.10.0 0.0.0.255 establishedR1(config-ext-nacl)# exitR1(config)# interface g0/0/0R1(config-if)# ip access-group SURFING inR1(config-if)# ip access-group BROWSING outR1(config-if)# endR1# show access-listsExtended IP access list SURFING    10 permit tcp 192.168.10.0 0.0.0.255 any eq www    20 permit tcp 192.168.10.0 0.0.0.255 any eq 443 (124 matches) Extended IP access list BROWSING    10 permit tcp any 192.168.10.0 0.0.0.255 established (369 matches) R1#
```
## Modify ACLs
#### Text Editor Method
For example, assume ACL 1 was entered incorrectly using **19** instead of **192** for the first octet, as shown in the running configuration.
```
R1# show run | section access-list access-list 1 deny 19.168.10.10access-list 1 permit 192.168.10.0 0.0.0.255R1#
```

Assume that ACL 1 has now been corrected. Therefore, the incorrect ACL must be deleted, and the corrected ACL 1 statements must be pasted in global configuration mode, as shown in the output.
```
R1(config)# no access-list 1
R1(config)#
R1(config)# access-list 1 deny 192.168.10.10
R1(config)# access-list 1 permit 192.168.10.0 0.0.0.255
R1(config)#
```
#### Sequence Number Method
