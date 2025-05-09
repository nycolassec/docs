## Secure Cisco IOS Image and Configuration Files

#### Enable the IOS Image Resilience Feature
To secure the IOS image and enable Cisco IOS image resilience, use the **secure boot-image** global configuration mode command.
To take a snapshot of the router running configuration and securely archive it in persistent storage, use the **secure boot-config** global configuration mode command.
The running image and running configuration archives are not visible in the **dir** command output. Use the **show secure bootset** command to verify the existence of the archive.
```
R1(config)# secure boot-image

R1(config)# secure boot-config

R1# show secure bootset
IOS resilience router id FTX1449AJBJ
 
IOS image resilience version 15.4 activated at 12:47:09 UTC Tue Sep 22 2020
Secure archive flash0:c2900-universalk9-mz.SPA.154-3.M.bin type is image (elf) []
  file size is 103727964 bytes, run size is 103907016 bytes
  Runnable image, entry point 0x81000000, run from ram
 
IOS configuration resilience version 15.4 activated at 12:47:18 UTC Tue Sep 22 2020
Secure archive flash0:.runcfg-20200922-124717.ar type is config
configuration archive size 1683 bytes
```
#### The primary Bootset Image
Restore a primary bootset from a secure archive after the router has been tampered with, as shown in the following steps and example:
```
Router# reload
<Issue Break sequence, if necessary>
rommon 1 > dir flash0:
program load complete, entry point: 0x80803000, size: 0x1b340
Directory of flash0:
 
4    103727964  -rw-     c2900-universalk9-mz.SPA.154-3.M.bin
  
 
rommon 2 > boot flash0:c2900-universalk9-mz.SPA.154-3.M.bin <Router reboots with specified image>
Router> enable
Router# conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)# secure boot-config restore flash0:rescue-cfg
ios resilience:configuration successfully restored as flash0:rescue-cfg
 
Router(config)# end
Router# copy flash0:rescue-cfg running-config
Destination filename [running-config]?  
%IOS image resilience is already active
%IOS configuration resilience is already active
 
2182 bytes copied in 0.248 secs (8798 bytes/sec)
 
R1#
```
#### Configure Secure Copy
SCP relies on:
- SSH to secure communication
- AAA to provide authentication and authorization

**Configuring**
```
R1(config)# ip domain-name span.com
R1(config)# crypto key generate rsa general-keys modulus 2048
R1(config)# username Bob privilege 15 algorithm-type scrypt secret cisco12345
R1(config)# aaa new-model                          
R1(config)# aaa authentication login default local
R1(config)# aaa authorization exec default local  
R1(config)# ip scp server enable
```

**Copying**
```
R2# copy flash0:R2backup.cfg scp:
Address or name of remote host []? 10.1.1.1
Destination username [R2]? Bob
Destination filename [R2backup.cfg]?  
Writing R2backup.cfg  
Password: <cisco12345>
!
1381 bytes copied in 8.596 secs (161 bytes/sec)
 
R2#
```
#### Recover a router password
```
R1(config)# no service password-recovery
WARNING:
Executing this command will disable password recovery    
mechanism.
Do not execute this command without another plan for  
password recovery.
Are you sure you want to continue? [yes/no]: yes
R1(config)#
```
***
## Lock Down a Router Using AutoSecure
The Cisco Discovery Protocol (CDP) is an example of a service that is enabled by default on Cisco routers. The Link Layer Discovery Protocol (LLDP) is an open standard that can be enabled on Cisco devices, as well as other vendor devices that support LLDP.

```
R1(config)# lldp run
R1(config)# end
R1# show cdp neighbors detail
-------------------------
Device ID: S1
Entry address(es):
  IP address: 192.168.1.254
Platform: cisco WS-C2960-24TT-L, Capabilities: Switch IGMP
Interface: GigabitEthernet0/1, Port ID (outgoing port): FastEthernet0/5
Holdtime : 164 sec
 
Version :
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE7,
RELEASE SOFTWARE (fc1)          
<output omitted>
 
R1# show lldp neighbors detail  
------------------------------------------------
Local Intf: Gi0/1
Chassis id: 0022.9121.0380
Port id: Fa0/5
Port Description: FastEthernet0/5
System Name: S1
 
System Description:  
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE7,
RELEASE SOFTWARE (fc1)
<output omitted>
```

#### Cisco autosecurity
Released in IOS version 12.3, Cisco AutoSecure is a feature that is initiated from the CLI and executes a script. AutoSecure can lock down the management plane functions and the forwarding plane services and functions of a router. There are several management plane services and functions:
- Secure BOOTP, CDP, FTP, TFTP, PAD, UDP, and TCP small servers, MOP, ICMP (redirects, mask-replies), IP source routing, Finger, password encryption, TCP keepalives, gratuitous ARP, proxy ARP, and directed broadcast
- Legal notification using a banner
- Secure password and login functions
- Secure NTP
- Secure SSH access
- TCP intercept services

There are three forwarding plane services and functions that AutoSecure enables:

- Cisco Express Forwarding (CEF)
- Traffic filtering with ACLs
- Cisco IOS firewall inspection for common protocols
```
R1# auto secure 
```

```
Router# auto secure {no-interact | full} [forwarding | management] [ntp | login | ssh | firewall | top-intercept]
```

#### Routing Protocol Authentication
Enable OSPF MD5 authentication globally:
- **ip ospf message-digest-key** _key_ **md5** _password_ interface configuration command.
- **area** _area-id_ **authentication message-digest** router configuration command.
- This method forces authentication on all OSPF enabled interfaces. If an interface is not configured with th**e ip ospf message-digest-key** command, it will not be able to form adjacencies with other OSPF neighbors.

Enable MD5 authentication on a per interface basis:
- **ip ospf message-digest-key** _key_ **md5** _password_ interface configuration command.
- **ip ospf authentication message-digest** interface configuration command.

##### OSPF Configured Without Authentication
```
R1# show run | begin router ospf
router ospf 1
  passive-interface GigabitEthernet0/1
  network 10.1.1.0 0.0.0.3 area 0
  network 192.168.1.0 0.0.0.255 area 0
!
<output omitted>
!--------------------------------
R2# show run | begin router ospf
router ospf 1
  passive-interface GigabitEthernet0/1
  network 10.1.1.0 0.0.0.3 area 0
  network 192.168.2.0 0.0.0.255 area 0
!
<output omitted>
```

##### OSPF SHA Routing Protocol Authentication
**Step 1.** Specify an authentication key chain in global configuration mode:
- Configure a key chain name with the **key chain** command.
- Assign the key chain a number and a password with the **key** and **key-string** commands.
- Specify SHA authentication with the **cryptographic-algorithm** command.
- (Optional) Specify when this key will expire with the **send-lifetime** command.
The syntax for these commands are as follows:
```
Router(config)# key chain name
Router(config-keychain)# key key-id
Router(config-keychain-key)# key-string string
Router(config-keychain-key)# cryptographic-algorithm {hmac-sha-1 | hmac-sha-256 | hmac-sha-384 | hmac-sha-512 | md5} 
Router(config-keychain-key)# send-lifetime start-time {infinite | end-time | duration seconds}
```

**Step 2.** Use the following syntax to assign the authentication key to the desired interfaces with the **ip ospf authentication key-chain** command.
```
Router(config)# interface type number 
Router(config-if)# ip ospf authentication key-chain name
```

##### OSPF Configured with SHA Authentication
```
R1(config)# key chain SHA256
R1(config-keychain)# key 1
R1(config-keychain-key)# key-string ospfSHA256
R1(config-keychain-key)# cryptographic-algorithm hmac-sha-256
R1(config-keychain-key)# exit
R1(config-keychain)# exit
R1(config)# interface s0/0/0
R1(config-if)# ip ospf authentication key-chain SHA256
R1(config-if)#
000218: Feb 20 15:06:07.607 UTC: %OSPF-5-ADJCHG: Process 1, Nbr 10.1.1.2 on Serial0/0/0  
from FULL to DOWN, Neighbor Down: Dead timer expired
R1(config-if)#
--------------------------------------
R2(config)# key chain SHA256
R2(config-keychain)# key 1
R2(config-keychain-key)# key-string ospfSHA256
R2(config-keychain-key)# cryptographic-algorithm hmac-sha-256
R2(config-keychain-key)# exit
R2(config-keychain)# exit
R2(config)# interface s0/0/0
R2(config-if)# ip ospf authentication key-chain SHA256
R2(config-if)#
000142: Feb 20 15:07:22.631: %OSPF-5-ADJCHG: Process 1, Nbr 192.168.1.1 on Serial0/0/0  
from LOADING to FULL, Loading Done
R2(config-if)#
```
***
## Syslog
ach syslog level has its own meaning:
- **Emergency Level 0 - Warning Level 4:** These messages are error messages about software or hardware malfunctions; these types of messages mean that the functionality of the device is affected. The severity of the issue determines the actual syslog level applied.
- **Notification Level 5:** This notifications level is for normal, but significant events. For example, interface up or down transitions, and system restart messages are displayed at the notifications level.
- **Informational Level 6:** This is a normal information message that does not affect device functionality. For example, when a Cisco device is booting, you might see the following informational message: %LICENSE-6-EULA_ACCEPT_ALL: The Right to Use End User License Agreement is accepted.
- **Debugging Level 7:** This level indicates that the messages are output generated from issuing various **debug** commands.
#### Facilities
Syslog facilities are service identifiers that identify and categorize system state data for error and event message reporting. The logging facility options that are available are specific to the networking device.

Some common syslog message facility codes reported on Cisco IOS routers include:
- **IF** - Identifies that the syslog message was generated by an interface.
- **IP** - Identifies that the syslog message was generated by IP.
- **OSPF** - Identifies that the syslog message was generated by the OSPF routing protocol.
- **SYS** - Identifies that the syslog message was generated by the device operating system.
- **IPSEC** - Identifies that the syslog message was generated by the IP Security encryption protocol.

By default, the format of syslog messages on the Cisco IOS Software is as follows:

```
! %facility-severity-MNEMONIC: description

%LINK-3-UPDOWN: Interface Port-channel1, changed state to up
```

#### Configure Syslog Timestamps
```
R1# configure terminal
R1(config)# interface g0/0/0
R1(config-if)# shutdown
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to administratively down
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0, changed state to down
R1(config-if)# exit
R1(config)# service timestamps log datetime
R1(config)# interface g0/0/0
R1(config-if)# no shutdown
*Mar  1 11:52:42: %LINK-3-UPDOWN: Interface GigabitEthernet0/0/0, changed state to down
*Mar  1 11:52:45: %LINK-3-UPDOWN: Interface GigabitEthernet0/0/0, changed state to up
*Mar  1 11:52:46: %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0,
changed state to up
R1(config-if)#
```

#### Syslog Configuration
Configure system logging:

**Step 1**. Set the destination logging host using the **logging** [**host**] command.  
```
Router(config)# logging host [hostname | ip-address]
```

**Step 2**. (Optional) Set the log severity (trap) level using the **logging trap** command.  
```
Router(config)# logging trap level
```

**Step 3**. (Optional) Set the source interface using the **logging source-interface** command.  
```
Router(config)# logging source-interface interface-type interface-number
```

**Step 4**. (Optional) Enable logging to all enabled destinations with the **logging on** command.
```
Router(config)# logging on
```

```
R1(config)# logging 10.2.2.6
R1(config)#
*Sep 25 12:57:14.120: %SYS-6-LOGGINGHOST_STARTSTOP: Logging to host 10.2.2.6 port 514 started - CLI initiated
R1(config)#
R1(config)# logging trap informational
R1(config)# logging source-interface lo0
R1(config)# logging on
R1(config)# exit
```

***
## NTP
The date and time settings on a router or switch can be manually configured, as shown in the example.
```
R1# clock set 16:01:00 sept 25 2020 
R1# show clock detail 
```

```
S1(config)# ntp server 192.168.1.1
S1(config)# end
R1# show clock detail 
S1# show ntp associations
R1# show ntp status 
```



