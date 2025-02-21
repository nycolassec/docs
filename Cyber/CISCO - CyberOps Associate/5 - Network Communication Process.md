Message timing is also very important in network communications. Message timing includes the following:
- **Flow Control** - This is the process of managing the rate of data transmission. Flow control defines how much information can be sent and the speed at which it can be delivered. For example, if one person speaks too quickly, it may be difficult for the receiver to hear and understand the message. In network communication, there are network protocols used by the source and destination devices to negotiate and manage the flow of information.
- **Response Timeout** - If a person asks a question and does not hear a response within an acceptable amount of time, the person assumes that no answer is coming and reacts accordingly. The person may repeat the question or instead, may go on with the conversation. Hosts on the network use network protocols that specify how long to wait for responses and what action to take if a response timeout occurs.
- **Access method** - This determines when someone can send a message. Click Play in the figure to see an animation of two people talking at the same time, then a "collision of information" occurs, and it is necessary for the two to back off and start again. Likewise, when a device wants to transmit on a wireless LAN, it is necessary for the WLAN network interface card (NIC) to determine whether the wireless medium is available.

|**OSI Model Layer**|**Description**|
|---|---|
|**7 - Application**|The application layer contains protocols used for process-to-process communications.|
|**6 - Presentation**|The presentation layer provides for common representation of the data transferred between application layer services.|
|**5 - Session**|The session layer provides services to the presentation layer to organize its dialogue and to manage data exchange.|
|**4 - Transport**|The transport layer defines services to segment, transfer, and reassemble the data for individual communications between the end devices.|
|**3 - Network**|The network layer provides services to exchange the individual pieces of data over the network between identified end devices.|
|**2 - Data Link**|The data link layer protocols describe methods for exchanging data frames between devices over a common media.|
|**1 - Physical**|The physical layer protocols describe the mechanical, electrical, functional, and procedural means to activate, maintain, and de-activate physical connections for a bit transmission to and from a network device.|

![[Pasted image 20250214080758.png]]

| **TCP/IP Model Layer** | **Description**                                                         |
| ---------------------- | ----------------------------------------------------------------------- |
| **4 - Application**    | Represents data to the user, plus encoding and dialog control.          |
| **3 - Transport**      | Supports communication between various devices across diverse networks. |
| **2 - Internet**       | Determines the best path through the network.                           |
| **1 - Network Access** | Controls the hardware devices and media that make up the network.       |

![[Pasted image 20250214082404.png]]

- Data - The general term for the PDU used at the application layer
- Segment - Transport layer PDU
- Packet - Network layer PDU
- Frame - Data Link layer PDU
- Bits - Physical layer PDU used when physically transmitting data over the medium




















