#### Event 1644
The LDAP performance monitoring log.


***
During user access to network resources, several Windows Event Logs are generated to record the logon process and related activities.

- `Event ID 4648 (Explicit Credential Logon Attempt)`: This event is logged when explicit credentials (e.g., username and password) are provided during logon.
- `Event ID 4624 (Logon)`: This event indicates that a user has successfully logged on to the system.
- `Event ID 4672 (Special Logon)`: This event is logged when a user's logon includes special privileges, such as running applications as an administrator.
- `Event ID 4768 (Kerberos TGT Request)`: This event is logged when a client requests a Ticket Granting Ticket (TGT) during the Kerberos authentication process.
- `Event ID 4769 (Kerberos Service Ticket Request)`: When a client requests a Service Ticket (TGS Ticket) to access a remote service during the Kerberos authentication process, Event ID 4769 is generated.