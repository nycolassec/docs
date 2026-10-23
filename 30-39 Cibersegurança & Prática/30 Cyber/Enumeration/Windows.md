#### Policies

```shell-session
crackmapexec smb 172.16.5.5 -u avazquez -p Password123 --pass-pol
```

```shell-session
rpcclient -U "" -N 172.16.5.5
rpcclient $> querydominfo
```


```shell-session
enum4linux-ng -P 172.16.5.5 -oA ilfreight
```


## Enumerating Null Session - from Windows

It is less common to do this type of null session attack from Windows, but we could use the command `net use \\host\ipc$ "" /u:""` to establish a null session from a windows machine and confirm if we can perform more of this type of attack.

#### Establish a null session from windows

```cmd-session
C:\htb> net use \\DC01\ipc$ "" /u:""
The command completed successfully.
```

We can also use a username/password combination to attempt to connect. Let's see some common errors when trying to authenticate:

#### Error: Account is Disabled

```shell
C:\htb> net use \\DC01\ipc$ "" /u:guest
System error 1331 has occurred.

This user can't sign in because this account is currently disabled.
```

#### Error: Password is Incorrect

```shell
C:\htb> net use \\DC01\ipc$ "password" /u:guest
System error 1326 has occurred.

The user name or password is incorrect.
```

#### Error: Account is locked out (Password Policy)

```shell
C:\htb> net use \\DC01\ipc$ "password" /u:guest
System error 1909 has occurred.

The referenced account is currently locked out and may not be logged on to.
```


#### Using ldapsearch

```shell
$ ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength  forceLogoff: -9223372036854775808 lockoutDuration: -18000000000 lockOutObservationWindow: -18000000000 lockoutThreshold: 5
```