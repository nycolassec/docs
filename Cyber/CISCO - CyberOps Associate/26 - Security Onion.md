Security Onion tools provide three core functions for the cybersecurity analyst: full packet capture and data types, network-based and host-based intrusion detection systems, and alert analyst tools.

| Analysis  | Detection | Data             |
| :-------- | :-------- | :--------------- |
| Sguil     | CapME     | PCAPS            |
| Wireshark | Snort     | Content Data     |
| Kibana    | Zeek/Bro  | Transaction Data |
|           | OSSEC     | Session Data     |
|           | Wazuh     | Host Logs        |
|           | Suricata  | Alert Data       |
|           |           | Syslog Data      |
|           |           | Metadata         |

CapME allows viewing of pcap transcripts rendered with the tcpflow or Zeek tools.

### Analysis Tools
**Sguil** - This provides a high-level console for investigating security alerts from a wide variety of sources. Sguil serves as a starting point in the investigation of security alerts.
**Kibana** - Kibana is an interactive dashboard interface to Elasticsearch data. It allows querying of NSM data and provides flexible visualizations of that data.
**Wireshark** - This is a packet capture application that is integrated into the Security Onion suite. It can be opened directly from other tools and will display full packet captures relevant to an analysis.
**Zeek** - This is a network traffic analyzer that serves as a security monitor. Zeek inspects all traffic on a network segment and enables in-depth analysis of that data.
### Alert Generation
In Security Onion, Sguil provides a console that integrates alerts from multiple sources into a timestamped queue. A cybersecurity analyst can work through the security queue investigating, classifying, escalating, or retiring alerts.

 Recall that the five-tuples includes the following information for tracking a conversation between a source and destination application:
- **SrcIP** - the source IP address for the event.
- **SPort** - the source (local) Layer 4 port for the event.
- **DstIP** - the destination IP for the event.
- **DPort** - the destination Layer 4 port for the event.
- **Pr** - the IP protocol number for the event.

The fields available for the real-time events are as follows:
- **ST** - This is the status of the event. RT means real time. The event is color-coded by priority. There are four priority levels: very low, low, medium, and high. The colors range from light yellow to red as the priority increases.
- **CNT** - This is the count for the number of times this event has been detected for the same source and destination IP address. The system has determined that this set of events is correlated. 
- **Sensor** - This is the agent reporting the event. The available sensors and their identifying numbers can be found in the Agent Status tab of the pane which appears below the events window on the left. These numbers are also used in the Alert ID column.
- **Alert ID** - This two-part number represents the sensor that has reported the problem and the event number for that sensor. 
- **Date/Time** - This is the timestamp for the event. In the case of correlated events, it is the timestamp for the first event.
- **Event Message** - This is the identifying text for the event. This is configured in the rule that triggered the alert. 
### Rules and Alerts
Alerts can come from a number of sources:
- **NIDS** - Snort, Zeek, and Suricata
- **HIDS** - OSSEC, Wazuh
- **Asset management and monitoring** - Passive Asset Detection System (PADS)
- **HTTP, DNS, and TCP transactions** - Recorded by Zeek and pcaps
- **Syslog messages** - Multiple sources










































