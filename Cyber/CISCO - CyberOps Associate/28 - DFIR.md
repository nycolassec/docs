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
The model represents a security incident or event. In the Diamond Model, an event is a time-bound activity that is restricted to a specific step in which an adversary uses a capability over infrastructure to attack a victim to achieve a specific result.

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
- Filling out forms
- Following checklists
***
# Incident Response Stakeholders
- **Management** - Managers create the policies that everyone must follow. They also design the budget and are in charge of staffing all of the departments. Management must coordinate the incident response with other stakeholders and minimize the damage of an incident.
- **Information Assurance** - This group may need to be called in to change things such as firewall rules during some stages of incident management such as containment or recovery.
- **IT Support** - This is the group that works with the technology in the organization and understands it the most. Because IT support has a deeper understanding, it is more likely that they will perform the correct action to minimize the effectiveness of the attack or preserve evidence properly.
- **Legal Department** - It is a best practice to have the legal department review the incident policies, plans, and procedures to make sure that they do not violate any local or federal guidelines. Also, if any incident has legal implications, a legal expert will need to become involved. This might include prosecution, evidence collection, or lawsuits.
- **Public Affairs and Media Relations** - There are times when the media and the public might need to be informed of an incident, such as when their personal information has been compromised during an incident.
- **Human Resources** - The human resources department might need to perform disciplinary measures if an incident caused by an employee occurs.
- **Business Continuity Planners** - Security incidents may alter an organization’s business continuity. It is important that those in charge of business continuity planning are aware of security incidents and the impact they have had on the organization as a whole. This will allow them to make any changes in plans and risk assessments.
- **Physical Security and Facilities Management** - When a security incident happens because of a physical attack, such as tailgating or shoulder surfing, these teams might need to be informed and involved. It is also their responsibility to secure facilities that contain evidence from an investigation.
***
# The Cybersecurity Maturity Model Certification
The Cybersecurity Maturity Model Certification (CMMC) framework was created to assess the ability of organizations that perform functions for the U.S. Department of Defense (DoD) to protect the military supply chain from disruptions or losses due to cybersecurity incidents.

The CMMC specifies 17 domains, each of which has a varying number of capabilities that are associated with it.

The capabilities that are associated with the incident response domain are as follows:
- Plan incident response
- Detect and report events
- Develop and implement a response to a declared incident
- Perform post incident reviews
- Test incident response

The CMMC certifies organizations by level. For most domains, there are five levels, however for incident response, there are only four.
- **Level 2** - Establish an incident response plan that follows the NIST process. Detect, report, and prioritize events. Respond to events by following predefined procedures. Analyze the cause of incidents in order to mitigate future issues.
- **Level 3** - Document and report incidents to stakeholders that have been identified in the incident response plan. Test the incident response capability of the organization.
- **Level 4** - Use knowledge of attacker tactics, techniques, and procedures (TTP) to refine incident response planning and execution. Establish a security operation center (SOC) that facilitates a 24/7 response capability.
- **Level 5** - Utilize accepted and systematic computer forensic data gathering techniques including the secure handling and storage of forensic data. Develop and utilize manual and automated real-time responses to potential incidents that follow known patterns.
***
# NIST Incident Response Life Cycle
NIST defines four steps in the incident response process life cycle, as shown in the figure.

- **Preparation** - The members of the CSIRT are trained in how to respond to an incident. CSIRT members should continually develop knowledge of emerging threats.
- **Detection and Analysis** - Through continuous monitoring, the CSIRT quickly identifies, analyzes, and validates an incident.
- **Containment, Eradication, and Recovery** - The CSIRT implements procedures to contain the threat, eradicate the impact on organizational assets, and use backups to restore data and software. This phase may cycle back to detection and analysis to gather more information, or to expand the scope of the investigation.
- **Post-Incident Activities** - The CSIRT then documents how the incident was handled, recommends changes for future response, and specifies how to avoid a reoccurrence.

![[Pasted image 20250311104657.png]]

#### Preparation
The preparation phase is when the CSIRT is created and trained. This phase is also when the tools and assets that will be needed by the team to investigate incidents are acquired and deployed.
#### Detection and Analysis
Because there are so many different ways in which a security incident can occur, it is impossible to create instructions that completely cover each step to follow to handle them. Different types of incidents will require different responses.
###### **Attack Vectors**
An organization should be prepared to handle any incident but should focus on the most common types of incidents so that they can be dealt with swiftly.
- **Web** - Any attack that is initiated from a website or application hosted by a website.
- **Email** - Any attack that is initiated from an email or email attachment.
- **Loss or Theft** - Any equipment that is used by the organization such as a laptop, desktop, or smartphone can provide the required information for someone to initiate an attack.
- **Impersonation** - When something or someone is replaced for the purpose of malicious intent.
- **Attrition** - Any attack that uses brute force to attack devices, networks, or services.
- **Media** - Any attack that is initiated from external storage or removable media.
###### **Detection**
Some incidents are easy to detect while others may go undetected for months.
There are two categories for the signs of an incident:
- **Precursor** - This is a sign that an incident might occur in the future. When precursors are detected, an attack might be avoided by altering security measures to specifically address the type of attack detected.
- **Indicator** - This is a sign that an incident might already have occurred or is currently occurring.
###### **Analysis**
Incident analysis is difficult because not all of the indicators are accurate. In a perfect world, each indicator should be analyzed to find out if it is accurate. This is nearly impossible due to the number and variety of logged and reported incidents.
###### **Scoping**
When the CSIRT believes that an incident has occurred, it should immediately perform an initial analysis to determine the incident’s scope, such as which networks, systems, or applications are affected, who or what originated the incident, and how the incident is occurring.
###### **Incident Notification**
When an incident is analyzed and prioritized, the incident response team needs to notify the appropriate stakeholders and outside parties so that all who need to be involved will play their roles.
#### Containment, Eradication, and Recovery
After a security incident has been detected and sufficient analysis has been performed to determine that the incident is valid, it must be contained in order to determine what to do about it. Strategies and procedures for incident containment need to be in place before an incident occurs and implemented before there is widespread damage.
###### **Containment strategy**
For every type of incident, a containment strategy should be created and enforced.
During containment, additional damage may be incurred. For example, it is not always advisable to unplug the compromised host from the network. The malicious process could notice this disconnection to the CnC controller and trigger a data wipe or encryption on the target.
###### **Evidence**
Evidence is also important for subsequent investigation by authorities. For evidence to be admissible in court, evidence collection must conform to specific regulations. After evidence collection, it must be accounted for properly. This is known as the chain of custody. These are some of the most important items to log when documenting evidence used in the chain of custody

- Location of the recovery and storage of all evidence
- Any identifying criteria for all evidence such as serial number, MAC address, hostname, or IP address
- Identification information for all of the people that participated in collecting or handling the evidence
- Time and date that the evidence was collected and each instance it was handled
###### **Attacker identification**
Identifying attackers is secondary to containing, eradicating, and recovering hosts and services.
These are some of the most important actions to perform to attempt to identify an attacking host during a security incident:
- Use incident databases to research related activity.
- Validate the attacker’s IP address to determine if it is a viable one.
- Use an internet search engine to gain additional information about the attack.
- Monitor the communication channels that some attackers use, such as IRC.
###### **Eradication, recovery and remediation**
After containment, the first step to eradication is identifying all of the hosts that need remediation. All of the effects of the security incident must be eliminated. All of the vulnerabilities that were exploited by the attacker must also be corrected or patched so that the incident does not occur again.

To recover hosts, use clean and recent backups, or rebuild them with installation media if no backups are available or they have been compromised.
#### Post-Incident Activity Phase
After incident response activities have eradicated the threats and the organization has begun to recover from the effects of the attack, it is important to take a step back and periodically meet with all of the parties involved to discuss the events that took place and the actions of all of the individuals while handling the incident.

**Lessons-based hardening**
After a major incident has been handled, the organization should hold a “lessons learned” meeting to review the effectiveness of the incident handling process and identify necessary hardening needed for existing security controls and practices.
***
# Incident Data Collection and Retention
By having ‘lessons learned’ meetings, the collected data can be used to determine the cost of an incident for budgeting reasons, as well as to determine the effectiveness of the CSIRT, and identify possible security weaknesses throughout the system. Only collect data that can be used to define and refine the incident handling process.

Evidence is often retained for many months or many years after an incident has taken place. In some cases, compliance regulations may mandate the retention period. These are some of the determining factors for evidence retention:
- **Prosecution** - When an attacker will be prosecuted because of a security incident, the evidence should be retained until after all legal actions have been completed. This may be several months or many years. 
- **Data Type** - An organization may state that particular types of data should be kept for a specific period of time. Items such as email or text may only need to be kept for 90 days. 
- **Cost** - If there is a lot of hardware and storage media that needs to be stored for a long time, it can become costly. 
***
# Reporting Requirements and Information Sharing
Governmental regulations should be consulted by the legal team to determine precisely the organization’s responsibility for reporting the incident.

Beyond the legal requirements and stakeholder considerations, NIST recommends that an organization coordinate with organizations to share details for the incident. For example, the organization could log the incident in the VERIS community database.

The critical recommendations from NIST for sharing information are as follows:
- Plan incident coordination with external parties before incidents occur.
- Consult with the legal department before initiating any coordination efforts.
- Perform incident information sharing throughout the incident response life cycle.
- Attempt to automate as much of the information sharing process as possible.
- Balance the benefits of information sharing with the drawbacks of sharing sensitive information.







































































































































































































































