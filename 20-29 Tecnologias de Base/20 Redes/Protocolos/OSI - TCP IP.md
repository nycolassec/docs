#osi #tcp/ip

# OSI
The functionality of each layer and the relationship between layers will become more evident throughout this course as the protocols are discussed in more detail.

| **OSI Model Layer**  | **Description**                                                                                                                                                                                                     |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **7 - Application**  | The application layer contains protocols used for process-to-process communications.                                                                                                                                |
| **6 - Presentation** | The presentation layer provides for common representation of the data transferred between application layer services.                                                                                               |
| **5 - Session**      | The session layer provides services to the presentation layer to organize its dialogue and to manage data exchange.                                                                                                 |
| **4 - Transport**    | The transport layer defines services to segment, transfer, and reassemble the data for individual communications between the end devices.                                                                           |
| **3 - Network**      | The network layer provides services to exchange the individual pieces of data over the network between identified end devices.                                                                                      |
| **2 - Data Link**    | The data link layer protocols describe methods for exchanging data frames between devices over a common media.                                                                                                      |
| **1 - Physical**     | The physical layer protocols describe the mechanical, electrical, functional, and procedural means to activate, maintain, and de-activate physical connections for a bit transmission to and from a network device. |

- Data - The general term for the PDU used at the application layer
- Segment - Transport layer PDU
- Packet - Network layer PDU
- Frame - Data Link layer PDU
- Bits - Physical layer PDU used when physically transmitting data over the medium

**Note:** If the Transport header is TCP, then it is a segment. If the Transport header is UDP then it is a datagram.


---

# TCP/IP
The TCP/IP model is a protocol model because it describes the functions that occur at each layer of protocols within the TCP/IP suite. TCP/IP is also used as a reference model. The table shows details about each layer of the OSI model.

|**TCP/IP Model Layer**|**Description**|
|---|---|
|**4 - Application**|Represents data to the user, plus encoding and dialog control.|
|**3 - Transport**|Supports communication between various devices across diverse networks.|
|**2 - Internet**|Determines the best path through the network.|
|**1 - Network Access**|Controls the hardware devices and media that make up the network.|

The table lists the various types of protocols that are needed to enable communications across one or more networks.

|**Protocol Type**|**Description**|
|---|---|
|**Network Communications Protocols**|Protocols enable two or more devices to communicate over one or more networks. The Ethernet family of technologies involves a variety of protocols such as IP, Transmission Control Protocol (TCP), HyperText Transfer Protocol (HTTP), and many more.|
|**Network Security Protocols**|Protocols secure data to provide authentication, data integrity, and data encryption. Examples of secure protocols include Secure Shell (SSH), Secure Sockets Layer (SSL), and Transport Layer Security (TLS).|
|**Routing Protocols**|Protocols enable routers to exchange route information, compare path information, and then to select the best path to the destination network. Examples of routing protocols include Open Shortest Path First (OSPF) and Border Gateway Protocol (BGP).|
|**Service Discovery Protocols**|Protocols are used for the automatic detection of devices or services. Examples of service discovery protocols include Dynamic Host Configuration Protocol (DHCP) which discovers services for IP address allocation, and Domain Name System (DNS) which is used to perform name-to-IP address translation.|
