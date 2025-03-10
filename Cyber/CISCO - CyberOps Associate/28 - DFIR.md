### The Digital Evidence Forensic Process
**Collection** - This is the identification of potential sources of forensic data and acquisition, handling, and storage of that data.
**Examination** - This entails assessing and extracting relevant information from the collected data. This may involve decompression or decryption of the data. Information that is irrelevant to the investigation may need to be removed.
**Analysis** - This entails drawing conclusions from the data. Salient features, such as people, places, times, events, and so on should be documented. This step may also involve the correlation of data from multiple sources.
**Reporting** - This entails preparing and presenting information that resulted from the analysis. Reporting should be impartial and alternative explanations should be offered if appropriate.
### Types of evidence
legal proceedings, evidence is broadly classified as either direct or indirect. Direct evidence is evidence that was indisputably in the possession of the accused, or is eyewitness evidence from someone who directly observed criminal behavior.

Evidence is further classified as:
- **Best evidence** - This is evidence that is in its original state. This evidence could be storage devices used by an accused, or archives of files that can be proven to be unaltered.
- **Corroborating evidence** - This is evidence that supports an assertion that is developed from best evidence.
- **Indirect evidence** - This is evidence that, in combination with other facts, establishes a hypothesis. This is also known as circumstantial evidence. For example, evidence that an individual has committed similar crimes can support the assertion that the person committed the crime of which they are accused.
### Evidence Collection Order
The collection of digital evidence should begin with the most volatile evidence and proceed to the least volatile. An example of most volatile to least volatile evidence collection order is as follows:
1. Memory registers, caches
2. Routing table, ARP cache, process table, kernel statistics, RAM
3. Temporary file systems
4. Non-volatile media, fixed and removable
5. Remote logging and monitoring data
6. Physical interconnections and topologies
7. Archival media, tape or other backups
****
# Steps of the Cyber Kill Chain
**Reconnaissance** - Researching target information. Reconnaissance is when the threat actor performs research, gathers intelligence, and selects targets.

**Weaponization** - The goal of this step is to use the information from reconnaissance to develop a weapon against specific targeted systems or individuals in the organization. To develop this weapon, the designer will use the vulnerabilities of the assets that were discovered and build them into a tool that can be deployed.

**Delivery** - During this step, the weapon is transmitted to the target using a delivery vector. This may be through the use of a website, removable USB media, or an email attachment.

**Exploitation** - After the weapon has been delivered, the threat actor uses it to break the vulnerability and gain control of the target. The most common exploit targets are applications, operating system vulnerabilities, and users.

**Installation** - This step is where the threat actor establishes a back door into the system to allow for continued access to the target.

**Command & Control (CnC)** - In this step, the goal is to establish command and control (CnC or C2) with the target system. Compromised hosts usually beacon out of the network to a controller on the internet.

**Actions on Objectives** - In this step, the goal is to establish command and control (CnC or C2) with the target system. Compromised hosts usually beacon out of the network to a controller on the internet.
****
# The Diamond Model of Intrusion Analysis

The four core features of an intrusion event are adversary, capability, infrastructure, and victim:
- **Adversary** - These are the parties responsible for the intrusion.
- **Capability** - This is a tool or technique that the adversary uses to attack the victim.
- **Infrastructure** - This is the network path or paths that the adversaries use to establish and maintain command and control over their capabilities.
- **Victim** - This is the target of the attack. However, a victim might be the target initially and then used as part of the infrastructure to launch other attacks.
The adversary uses capabilities over infrastructure to attack the victim.

Meta-features expand the model slightly to include the following important elements:
- **Timestamp** - This indicates the start and stop time of an event and is an integral part of grouping malicious activity.
- **Phase** - This is analogous to steps in the Cyber Kill Chain; malicious activity includes two or more steps executed in succession to achieve the desired result.
- **Result** - This delineates what the adversary gained from the event. Results can be documented as one or more of the following: confidentiality compromised, integrity compromised, and availability compromised.
- **Direction** - This indicates the direction of the event across the Diamond Model. These include Adversary-to-Infrastructure, Infrastructure-to-Victim, Victim-to-Infrastructure, and Infrastructure-to-Adversary.
- **Methodology** - This is used to classify the general type of event, such as port scan, phishing, content delivery attack, syn flood, etc.
- **Resources** - These are one or more external resources used by the adversary for the intrusion event, such as software, adversary’s knowledge, information (e.g., username/passwords), and assets to carry out the attack (hardware, funds, facilities, network access).
****
# Incident Response
The NIST 800-61r2 standard provides guidelines for incident handling, particularly for analyzing incident-related data, and determining the appropriate response to each incident.

The first step for an organization is to establish a computer security incident response capability (CSIRC). NIST recommends creating policies, plans, and procedures for establishing and maintaining a CSIRC.

### Policy Elements
An incident response policy details how incidents should be handled based on the organization’s mission, size, and function.

Policy elements include the following:
- Statement of management commitment
- Purpose and objectives of the policy
- Scope of the policy
- Definition of computer security incidents and related terms
- Organizational structure and definition of roles, responsibilities, and levels of authority
- Prioritization of severity ratings of incidents
- Performance measures
- Reporting and contact forms

### Plan Elements
A good incident response plan helps to minimize damage caused by an incident. It also helps to make the overall incident response program better by adjusting it according to lessons learned.

Plan elements are as follows:
- Mission
- Strategies and goals
- Senior management approval
- Organizational approach to incident response
- How the incident response team will communicate with the rest of the organization and with other organizations
- Metrics for measuring the incident response capacity
- How the program fits into overall organization

### Procedure Elements
The procedures that are followed during an incident response should follow the incident response plan. Procedures elements are as follows:

- Technical processes
- Using techniques
- Filling out forms,
- Following checklists

















