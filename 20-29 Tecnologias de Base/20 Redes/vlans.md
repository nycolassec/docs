**Normal Range VLANs**

The following are characteristics of normal range VLANs:

- They are used in all small- and medium-sized business and enterprise networks.
- They are identified by a VLAN ID between 1 and 1005.
- IDs 1002 through 1005 are reserved for legacy network technologies (i.e., Token Ring and Fiber Distributed Data Interface).
- IDs 1 and 1002 to 1005 are automatically created and cannot be removed.
- Configurations are stored in the switch flash memory in a VLAN database file called vlan.dat.
- When configured, VLAN trunking protocol (VTP), helps synchronize the VLAN database between switches.

**Extended Range VLANs**

The following are characteristics of extended range VLANs:

- They are used by service providers to service multiple customers and by global enterprises large enough to need extended range VLAN IDs.
- They are identified by a VLAN ID between 1006 and 4094.
- Configurations are saved, by default, in the running configuration.
- They support fewer VLAN features than normal range VLANs.
- Requires VTP transparent mode configuration to support extended range VLANs.

**Note:** 4096 is the upper boundary for the number of VLANs available on Catalyst switches, because there are 12 bits in the VLAN ID field of the IEEE 802.1Q header.
***
3.3.4 VLAN Port Assignment Commands

After creating a VLAN, the next step is to assign ports to the VLAN.

The table displays the syntax for defining a port to be an access port and assigning it to a VLAN. The **switchport mode access** command is optional, but strongly recommended as a security best practice. With this command, the interface changes to strictly access mode. Access mode indicates that the port belongs to a single VLAN and will not negotiate to become a trunk link.
The example shows the configuration for S1 to assign F0/6 to VLAN 20.

```
S1# configure terminal
S1(config)# interface fa0/6
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 20
S1(config-if)# end


S3(config)# vlan 20
S3(config-vlan)# name student
S3(config-vlan)# vlan 150
S3(config-vlan)# name VOICE
S3(config-vlan)# exit
S3(config)# interface fa0/18
S3(config-if)# switchport mode access
S3(config-if)# switchport access vlan 20
S3(config-if)# mls qos trust cos
S3(config-if)# switchport voice vlan 150
S3(config-if)# end
S3#
```

**Note:** Use the **interface range** command to simultaneously configure multiple interfaces.

***
The example shows the configuration of port F0/1 on switch S1 as a trunk port. The native VLAN is changed to VLAN 99 and the allowed VLAN list is restricted to 10, 20, 30, and 99.

```
S1(config)# interface fastEthernet 0/1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk native vlan 99
S1(config-if)# switchport trunk allowed vlan 10,20,30,99
S1(config-if)# end

# Retirar o trunk
S1(config)# interface fa0/1
S1(config-if)# no switchport trunk allowed vlan
S1(config-if)# no switchport trunk native vlan
S1(config-if)# end

# Mostrar informações
S1# show interfaces fa0/1 switchport
```
***
## DTP
![[Pasted image 20260724003159.png]]

![[Pasted image 20260724003239.png]] 