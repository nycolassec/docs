NTFS formatting creates important structures on the disk for file storage, and tables for recording the locations of files:

- **Partition Boot Sector** - This is the first 16 sectors of the drive. It contains the location of the Master File Table (MFT). The last 16 sectors contain a copy of the boot sector.
- **Master File Table (MFT)** - This table contains the locations of all the files and directories on the partition, including file attributes such as security information and timestamps.
- **System Files** - These are hidden files that store information about other volumes and file attributes.
- **File Area** - The main area of the partition where files and directories are stored.

Two types of computer firmware exist:
- **Basic Input-Output System (BIOS)** - BIOS firmware was created in the early 1980s and works in the same way it did when it was created. As computers evolved, it became difficult for BIOS firmware to support all the new features requested by users.
- **Unified Extensible Firmware Interface (UEFI)** - UEFI was designed to replace BIOS and support the new features.

In BIOS firmware, the process begins with the BIOS initialization phase. This is when hardware devices are initialized and a power on self-test (POST) is performed to make sure all of these devices are communicating. When the system disk is discovered, the POST ends. The last instruction in the POST is to look for the master boot record (MBR).

In contrast to BIOS firmware, UEFI firmware has a lot of visibility into the boot process. UEFI boots by loading EFI program files, stored as .efi files in a special disk partition, known as the EFI System Partition (ESP).

![[Pasted image 20250213140854.png]]

![[Pasted image 20250313100225.png]]

| **Registry Hive**              | **Description**                                                                                                                                                                                         |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **HKEY_CURRENT_USER (HKCU)**   | Holds information concerning the currently logged in user.                                                                                                                                              |
| **HKEY_USERS (HKU)**           | Holds information concerning all the user accounts on the host.                                                                                                                                         |
| **HKEY_CLASSES_ROOT (HKCR**)   | Holds information about object linking and embedding (OLE) registrations. OLE allows users to embed objects from other applications (like a spreadsheet) into a single document (like a Word document.) |
| **HKEY_LOCAL_MACHINE (HKLM)**  | Holds system-related information.                                                                                                                                                                       |
| **HKEY_CURRENT_CONFIG (HKCC)** | Holds information about the current hardware profile.                                                                                                                                                   |

Registry keys can contain either a subkey or a value. The different values that keys can contain are as follows:
- **REG_BINARY** - Numbers or Boolean values
- **REG_DWORD** - Numbers greater than 32 bits or raw data
- **REG_SZ** - String values

|**Command**|**Description**|
|---|---|
|**net accounts**|Sets password and logon requirements for users|
|**net session**|Lists or disconnects sessions between a computer and other computers on the network|
|**net share**|Creates, removes, or manages shared resources|
|**net start**|Starts a network service or lists running network services|
|**net stop**|Stops a network service|
|**net use**|Connects, disconnects, and displays information about shared network resources|
|**net view**|Shows a list of computers and network devices on the network|

|**Task Manager Tabs**|**Description**|
|---|---|
|**Processes**|- Lists all of the programs and processes that are currently running.<br>- Displays the CPU, memory, disk, and network utilization of each process.<br>- The properties of a process can be examined or ended if it is not behaving properly or has stalled.|
|**Performance**|- A view of all the performance statistics provides a useful overview of the CPU, memory, disk, and network performance.<br>- Clicking each item in the left pane will show detailed statistics of that item in the right pane.|
|**App history**|- The use of resources by application over time provides insight into applications that are consuming more resources than they should.<br>- Click **Options** and **Show history for all processes** to see the history of every process that has run since the computer was started.|
|**Startup**|- All of the applications and services that start when the computer is booted are shown in this tab.<br>- To disable a program from starting at startup, **right-click** the item and choose **Disable**.|
|**Users**|- All of the users that are logged on to the computer are shown in this tab.<br>- Also shown are all the resources that each user’s applications and processes are using.<br>- From this tab, an administrator can disconnect a user from the computer.|
|**Details**|- Similar to the Processes tab, this tab provides additional management options for processes such as setting a priority to make the processor devote more or less time to a process.<br>- CPU affinity can also be set which determines which core or CPU a program will use.<br>- Also, a useful feature called Analyze wait chain shows any process for which another process is waiting.<br>- This feature helps to determine if a process is simply waiting or is stalled.|
|**Services**|- All the services that are loaded are shown in this tab.<br>- The process ID (PID) and a short description are also shown along with the status of either Running or Stopped.<br>- At the bottom, there is a button to open the Services console which provides additional management of services.|

You can also use the **netsh.exe** tool to configure networking parameters from a command prompt.



