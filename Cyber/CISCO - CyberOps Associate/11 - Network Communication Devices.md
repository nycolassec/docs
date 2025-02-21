To encapsulate the packet in the Ethernet frame, the router needs to determine the destination MAC address associated with the destination IP address of the packet. The process varies based on whether the packet is an IPv4 or IPv6 packet:

- **IPv4 packet** - The router checks its ARP table for the destination IPv4 address and an associated Ethernet MAC address. If there is no match, the router sends an ARP Request. The destination device will return an ARP Reply with its MAC address. The router can now forward the IPv4 packet in an Ethernet frame with the proper destination MAC address.
- **IPv6 packet** - The router checks its neighbor cache for the destination IPv6 address and an associated Ethernet MAC address. If there is no match, the router sends an ICMPv6 Neighbor Solicitation (NS) message. The destination device will return an ICMPv6 Neighbor Advertisement (NA) message with its MAC address. The router can now forward the IPv6 packet in an Ethernet frame with the proper destination MAC address.

The table classifies the protocols. Routers configured with these protocols will periodically send messages to other routers. As a cybersecurity analyst, you will see these messages in various logs and packet captures.

|**Protocol**|**Interior Gateway Protocols**|   |   |   |**Exterior Gateway Protocols**|
|---|---|---|---|---|---|
||**Distance Vector**|   |**Link State**|   |**Path Vector**|
|IPv4|RIPv2|EIGRP|OSPFv2|IS-IS|BGP-4|
|IPv6|RIPng|EIGRP for IPv6|OSPFv3|IS-IS for IPv6|BGP-MP|


## Wireless
![[Pasted image 20250218110711.png]]
BSA: Basic Service Area
BSSID: Basic Service Set Identifier

![[Pasted image 20250218110927.png]]

![[Pasted image 20250218111202.png]]

![[Pasted image 20250218111246.png]]

WLANs also differ from wired LANs as follows:
- WLANs connect clients to the network through a wireless access point (AP) or wireless router, instead of an Ethernet switch.
- WLANs connect mobile devices that are often battery powered, as opposed to plugged-in LAN devices. Wireless NICs tend to reduce the battery life of a mobile device.
- WLANs support hosts that contend for access on the RF media (frequency bands). 802.11 prescribes collision-avoidance (CSMA/CA) instead of collision-detection (CSMA/CD) for media access to proactively avoid collisions within the media.
- WLANs use a different frame format than wired Ethernet LANs. WLANs require additional information in the Layer 2 header of the frame.
- WLANs raise more privacy issues because radio frequencies can reach outside the facility.

| **Characteristic**      | **802.11 Wireless LAN**                                | **802.3 Wired Ethernet LANs**      |
| ----------------------- | ------------------------------------------------------ | ---------------------------------- |
| **Physical Layer**      | radio frequency (RF)                                   | physical cables                    |
| **Media Access**        | collision avoidance                                    | collision detection                |
| **Availability**        | anyone with a wireless NIC in range of an access point | physical cable connection required |
| **Signal Interference** | yes                                                    | minimal                            |
| **Regulation**          | different regulations by country                       | IEEE standard dictates             |

![[Pasted image 20250218112322.png]]
All 802.11 wireless frames contain the following fields:
- **Frame Control** - This identifies the type of wireless frame and contains subfields for protocol version, frame type, address type, power management, and security settings.
- **Duration** - This is typically used to indicate the remaining duration needed to receive the next frame transmission.
- **Address1** - This usually contains the MAC address of the receiving wireless device or AP.
- **Address2** - This usually contains the MAC address of the transmitting wireless device or AP.
- **Address3** - This sometimes contains the MAC address of the destination, such as the router interface (default gateway) to which the AP is attached.
- **Sequence Control** - This contains information to control sequencing and fragmented frames.
- **Address4** - This usually missing because it is used only in ad hoc mode.
- **Payload** - This contains the data for transmission.
- **FCS** - This is used for Layer 2 error control.

Wireless devices complete the following three stage process, as shown in the figure:
- Discover a wireless AP
- Authenticate with AP
- Associate with AP

Parameters must then be configured on the AP and subsequently on the client to enable the negotiation of a successful association.
- **SSID** -The SSID name appears in the list of available wireless networks on a client. In larger organizations that use multiple VLANs to segment traffic, each SSID is mapped to one VLAN. Depending on the network configuration, several APs on a network can share a common SSID.
- **Password** - This is required from the wireless client to authenticate to the AP.
- **Network mode** - This refers to the 802.11a/b/g/n/ac/ad WLAN standards. APs and wireless routers can operate in a Mixed mode meaning that they can simultaneously support clients connecting via multiple standards.
- **Security mode** - This refers to the security parameter settings, such as WEP, WPA, or WPA2. Always enable the highest security level supported.
- **Channel settings** - This refers to the frequency bands used to transmit wireless data. Wireless routers and APs can scan the radio frequency channels and automatically select an appropriate channel setting. The channel can also be set manually if there is interference with another AP or wireless device.







































































