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