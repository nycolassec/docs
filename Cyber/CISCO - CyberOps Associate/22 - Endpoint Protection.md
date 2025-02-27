Specifically, there are two internal LAN elements to secure:
- **Endpoints** - Hosts commonly consist of laptops, desktops, printers, servers, and IP phones, all of which are susceptible to malware-related attacks.
- **Network infrastructure** - LAN infrastructure devices interconnect endpoints and typically include switches, wireless devices, and IP telephony devices. Most of these devices are susceptible to LAN-related attacks including MAC address table overflow attacks, spoofing attacks, DHCP related attacks, LAN storm attacks, STP manipulation attacks, and VLAN attacks.


**Antivirus/Antimalware Software**
This is software that is installed on a host to detect and mitigate viruses and malware. Examples are Windows Defender Virus & Threat Protection, Cisco AMP for Endpoints, Norton Security, McAfee, Trend Micro, and others. Antimalware programs may detect viruses using three different approaches:
- **Signature-based** - This approach recognizes various characteristics of known malware files.
- **Heuristics-based** - This approach recognizes general features shared by various types of malware.
- **Behavior-based** - This approach employs analysis of suspicious behavior.

The following are examples of devices and techniques that implement host protections at the network level.
- **Advanced Malware Protection (AMP)** - This provides endpoint protection from viruses and malware.
- **Email Security Appliance (ESA)** - This provides filtering of SPAM and potentially malicious emails before they reach the endpoint. An example is the Cisco ESA.
- **Web Security Appliance (WSA)** - This provides filtering of websites and block listing to prevent hosts from reaching dangerous locations on the web. The Cisco WSA provides control over how users access the internet and can enforce acceptable use policies, control access to specific sites and services, and scan for malware.
- **Network Admission Control (NAC)** - This permits only authorized and compliant systems to connect to the network.