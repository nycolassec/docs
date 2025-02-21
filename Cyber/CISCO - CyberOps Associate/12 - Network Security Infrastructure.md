### Packet Filtering (Stateless) Firewall 
Packet filtering firewalls are usually part of a router firewall, which permits or denies traffic based on Layer 3 and Layer 4 information. They are stateless firewalls that use a simple policy table look-up that filters traffic based on specific criteria.
- Source IP address
- Destination IP address
- Protocol
- Source port number
- Destination port number
- Synchronize/Start (SYN) packet receipt
### Stateful Firewall
Stateful firewalls provide stateful packet filtering by using connection information maintained in a state table. Stateful filtering is a firewall architecture that is classified at the network(3 OSI) layer. It also analyzes traffic at OSI Layer 4 and Layer 5.
### Application Gateway Firewall
An application gateway firewall (proxy firewall), as shown in the figure, filters information at Layers 3, 4, 5, and 7 of the OSI reference model. Most of the firewall control and filtering is done in software.
### Next Generation Firewall
Next-generation firewalls (NGFW) go beyond stateful firewalls by providing:
- Integrated intrusion prevention
- Application awareness and control to see and block risky apps
- Upgrade paths to include future information feeds
- Techniques to address evolving security threats

Other methods of implementing firewalls include:
- **Host-based (server and personal) firewall** - A PC or server with firewall software running on it.
- **Transparent firewall** - Filters IP traffic between a pair of bridged interfaces.
- **Hybrid firewall** - A combination of the various firewall types. For example, an application inspection firewall combines a stateful firewall with an application gateway firewall.

| **Solution** | **Advantages**                                                                                                                                  | **Disadvantages**                                                                                                                                           |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **IDS**      | - No Impact on network (latency, jitter)<br>- No Network impact if there is a sensor failure<br>- No network impact if there is sensor overload | - Response action cannot stop trigger packets<br>- Correct tuning required for response actions<br>- More vulnerable to network security evasion techniques |
| **IPS**      | - Stops trigger packets<br>- Can use stream normalization techniques                                                                            | - Sensor issues might affect network traffic<br>- Sensor overloading impacts the network<br>- Some impact on network (latency, jitter)                      |

There are a variety of specialized security appliances available. Here are a few examples.

A Cisco Email Security Appliance (ESA)/ Cisco Cloud Email Security helps to mitigate email-based threats. The Cisco ESA defends mission-critical email systems.

A Cisco Web Security Appliance (WSA) is a secure web gateway that combines leading protections to help organizations address the growing challenges of securing and controlling web traffic. WSA protects the network by automatically blocking risky sites and testing unknown sites before allowing users to access them. WSA provides malware protection, application visibility and control, acceptable use policy controls, insightful reporting and secure mobility.

Cisco Advanced Malware Protection (AMP) is an enterprise-class advanced malware analysis and protection solution. It provides comprehensive malware protection for organizations before, during, and after an attack.

#### SNMP
SNMP is an application layer protocol that provides a message format for communication between managers and agents. The Management Information Base (MIB) is a database on the agents that stores data and operational statistics about the device.

#### NetFlow
NetFlow is a Cisco IOS technology that provides statistics on packets flowing through a Cisco router or multilayer switch. While SNMP attempts to provide a very wide range of network management features and options, NetFlow is focused on providing statistics on IP packets flowing through network devices.

Should one of these fields vary in value from another packet, the packets could be safely determined to be from different flows:

- Source IP address
- Destination IP address
- Source port number
- Destination port number
- Layer 3 protocol type
- Type of Service (ToS) marking
- Input logical interface


#### Port Mirroring
Port mirroring is a feature that allows a switch to make duplicate copies of traffic passing through a switch, and then send it out a port with a network monitor attached.


#### Syslog
The syslog logging service provides three primary functions:
- The ability to gather logging information for monitoring and troubleshooting
- The ability to select the type of logging information that is captured
- The ability to specify the destination of captured syslog messages

#### NTP
Each level in this hierarchical system is called a stratum. The stratum level is defined as the number of hop counts from the authoritative source. The synchronized time is distributed across the network using NTP.
The max hop count is 15. Stratum 16, the lowest stratum level, indicates that a device is unsynchronized.

#### AAA Servers
all TACACS+ protocol exchanges are encrypted, while RADIUS only encrypts the user’s password. RADIUS does not encrypt usernames, accounting information, or any other information carried in the RADIUS message.

The table lists the differences between the two protocols.

|**TACACS+**|**RADIUS**|
|---|---|---|
|**Functionality**|Separates AAA according to the AAA architecture, allowing modularity of the security server implementation|Combines authentication and authorization but separates accounting, allowing less flexibility in implementation than TACACS+|
|**Standard**|Mostly Cisco supported|Open/RFC standard|
|**Transport**|TCP|UDP|
|**Protocol CHAP**|Bidirectional challenge and response as used in Challenge Handshake Authentication Protocol (CHAP)|Unidirectional challenge and response from the RADIUS security server to the RADIUS client|
|**Confidentiality**|Entire packet encrypted|Password encrypted|
|**Customization**|Provides authorization of router commands on a per-user or per-group basis|Has no option to authorize router commands on a per-user or per-group basis|
|**Accounting**|Limited|Extensive|

#### VPN
In the simplest sense, a VPN connects two endpoints, such as a remote office to a central office, over a public network, to form a logical connection. The logical connections can be made at either Layer 2 or Layer 3.

IPsec is a suite of protocols developed with the backing of the IETF to achieve secure services over IP packet-switched networks.
















































































































