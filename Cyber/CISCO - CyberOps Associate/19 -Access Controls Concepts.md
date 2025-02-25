The table lists various types of access control methods.

| **Access Control Models**                 | **Description**                                                                                                                                                                                                                                                                                                                                                                                |
| ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Discretionary access control (DAC)**    | - This is the least restrictive model and allows users to control access to their data as owners of that data.<br>- DAC may use ACLs or other methods to specify which users or groups of users have access to the information.                                                                                                                                                                |
| **Mandatory access control (MAC)**        | - This applies the strictest access control and is typically used in military or mission critical applications.<br>- It assigns security level labels to information and enables users with access based on their security level clearance.                                                                                                                                                    |
| **Role-based access control (RBAC)**      | - Access decisions are based on an individual’s roles and responsibilities within the organization.<br>- Different roles are assigned security privileges, and individuals are assigned to the RBAC profile for the role.<br>- Roles may include different positions, job classifications or groups of job classifications.<br>- Also known as a type of **non-discretionary access control**. |
| **Attribute-based access control (ABAC)** | ABAC allows access based on attributes of the object (resource) to be accessed, the subject (user) accessing the resource, and environmental factors regarding how the object is to be accessed, such as time of day.                                                                                                                                                                          |
| **Rule-based access control (RBAC)**      | - Network security staff specify sets of rules regarding or conditions that are associated with access to data or systems.<br>- These rules may specify permitted or denied IP addresses, or certain protocols and other conditions.<br>- Also known as **Rule Based RBAC.**                                                                                                                   |
| **Time-based access control (TAC)**       | TAC Allows access to network resources based on time and day.                                                                                                                                                                                                                                                                                                                                  |

The table lists the differences between the two protocols.

| **TACACS+**         | **RADIUS**                                                                                                                                                            |                                                                                                                                                                                                           |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Functionality**   | It separates authentication, authorization, and accounting functions according to the AAA architecture. This allows modularity of the security server implementation. | It combines authentication and authorization but separates accounting, which allows less flexibility in implementation than TACACS+                                                                       |
| **Standard**        | Mostly Cisco supported                                                                                                                                                | Open/RFC standard                                                                                                                                                                                         |
| **Transport**       | TCP port 49                                                                                                                                                           | UDP ports 1812 and 1813, or 1645 and 1646                                                                                                                                                                 |
| **Protocol CHAP**   | Bidirectional challenge and response as used in Challenge Handshake Authentication Protocol (CHAP)                                                                    | Unidirectional challenge and response from the RADIUS security server to the RADIUS client                                                                                                                |
| **Confidentiality** | Encrypts the entire body of the packet but leaves a standard TACACS+ header.                                                                                          | Encrypts only the password in the access-request packet from the client to the server. The remainder of the packet is unencrypted, leaving the username, authorized services, and accounting unprotected. |
| **Customization**   | Provides authorization of router commands on a per-user or per-group basis                                                                                            | Has no option to authorize router commands on a per-user or per-group basis                                                                                                                               |
| **Accounting**      | Limited                                                                                                                                                               | Extensive                                                                                                                                                                                                 |


The table displays the various types of accounting information that can be collected.

|**Type of Accounting Information**|**Description**|
|---|---|
|**Network Accounting**|Network accounting captures information for all Point-to-Point Protocol (PPP) sessions, including packet and byte counts.|
|**Connection Accounting**|Connection accounting captures information about all outbound connections that are made from the AAA client, such as by SSH.|
|**EXEC Accounting**|EXEC accounting captures information about user EXEC terminal sessions (user shells) on the network access server, including username, date, start and stop times, and the access server IP address.|
|**System Accounting**|System accounting captures information about all system-level events (for example, when the system reboots or when accounting is turned on or off).|
|**Command Accounting**|Command accounting captures information about the EXEC shell commands for a specified privilege level, as well as the date and time each command was executed, and the user who executed it.|
|**Resource Accounting**|The Cisco implementation of AAA accounting captures “start” and “stop” record support for connections that have passed user authentication. The additional feature of generating “stop” records for connections that fail to authenticate as part of user authentication is also supported. Such records are necessary for users employing accounting records to manage and monitor their networks.|




















































































































































