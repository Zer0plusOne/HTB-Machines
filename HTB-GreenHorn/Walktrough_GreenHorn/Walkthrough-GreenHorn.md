# HTB Machine Walkthrough: GreenHorn

> [!IMPORTANT]  
> **Please use this as a hint or guide if you're blocked at some point of this machine, i make those files for myself and to help some friends that are also doing those machines.**


> [!NOTE]  
> **Try to get as far as you can by yourself or you will never learn to make machines alone. Hope you the best and happy hacking <3.**

## 1. Nmap Scan

Execute a Nmap scan on the provided IP:

```bash
nmap -sC -sV -n -Pn --min-rate 300 -vvv
```

**RESULT:**

```cpp
OpenPorts: 	   22/TCP	SSH
		   80/TCP	HTTP
		   3000/TCP	ppp?
```

## 2. Update /etc/hosts

Add the IP to the \`/etc/hosts\` file and link it to \`greenhorn.htb\`.

## 3. Find Login Page on Port 80

Open the page on port 80 and find the login page.

**NOTE:** Default passwords do not work.

## 4. Access Port 3000

Open the page on port 3000.

## 5. Search for Interesting Content

Look for something interesting. 

**HINT:** The \`pass.php\` file might be relevant.

## 6. Crack the Hash

Crack the hash contained in the file using John the Ripper:

```bash
john --format=raw-sha512 --wordlist=../wordlists/rockyou.txt hash.txt
```

**RESULT:** Crack it yourself.

## 7. Login on Port 80

Use the cracked password on the login page on port 80.

## 8. Explore the Page

Once logged in, navigate through the page and search for vulnerabilities.

## 9. Exploiting the Vulnerability

The Pluk version used had an exploitable vulnerability. Inserting a reverse shell into the \`manage-modules\` area can exploit it.

**NOTE:** Any \`.zip\` file uploaded as a module is automatically unzipped and executed. Use the \`.php\` exploit from [pentestmonkey](https://github.com/pentestmonkey/) to gain access to the server.

## 10. Edit and Upload the Exploit

Before uploading the file, edit the \`.php\` file with your IP and port for netcat to listen:

```bash
nc -lvnp \$PORT
```

## 11. Spawn a Shell

Once you have access, check if Python is installed. If so, spawn a shell:

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

## 12. Access User

If you do not have access to the user flag, try logging in with the home page name "junior" using the password from the web page on port 80.

**RESULT:** "ACCESS GRANTED"

## 13. Retrieve User Flag

Retrieve the \`user.txt\` flag.

## 14. Get the PDF from User's Home

Find and get the PDF from the user's home directory. It contains a password.

**HINT:** Check the censored part of the content.

## 15. Escalate to Root

Use the password obtained to escalate to root.

## 16. Retrieve Root Flag

Retrieve the \`root.txt\` flag.

**MACHINE COMPLETED**



