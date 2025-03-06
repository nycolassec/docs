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

A host-based intrusion detection system (HIDS) is designed to protect hosts against known and unknown malware. A HIDS can perform detailed monitoring and reporting on the system configuration and application activity. It can provide log analysis, event correlation, integrity checking, policy enforcement, rootkit detection, and alerting.
It can be said that host-based security systems function as both detection and prevention systems because they prevent known attacks and detect unknown potential attacks.

 An additional set of strategies are used to detect the possibility of successful intrusions by malware that evades signature detection:
- **Anomaly-based** - Host system behavior is compared to a learned baseline model of normal behavior.
- **Policy-based** - Normal system behavior is described by rules, or the violation of rules, that are predefined.

 Examples are Cisco AMP, AlienVault USM, Tripwire, and Open Source HIDS SECurity (OSSEC).

---
- **Windows Defender Firewall** - First included with Windows XP, Windows Firewall (now Windows Defender Firewall) uses a profile-based approach to firewall functionality. Access to public networks is assigned the restrictive Public firewall profile.
- **iptables** - This is an application that allows Linux system administrators to configure network access rules that are part of the Linux kernel Netfilter modules.
- **nftables** - The successor to iptables, nftables is a Linux firewall application that uses a simple virtual machine in the Linux kernel. Code is executed within the virtual machine that inspects network packets and implements decision rules regarding packet acceptance and forwarding.
- **TCP Wrappers** - This is a rule-based access control and logging system for Linux. Packet filtering is based on IP addresses and network services.

---

The SANS Institute describes three components of the attack surface:
- **Network Attack Surface** - The attack exploits vulnerabilities in networks. This can include conventional wired and wireless network protocols, as well as other wireless protocols used by smartphones or IoT devices. Network attacks also exploit vulnerabilities at the network and transport layers.
- **Software Attack Surface** - The attack is delivered through exploitation of vulnerabilities in web, cloud, or host-based software applications.
- **Human Attack Surface** - The attack exploits weaknesses in user behavior. Such attacks include social engineering, malicious behavior by trusted insiders, and user error.























































