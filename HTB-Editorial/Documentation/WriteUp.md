# HTB Machine Walkthrough: Editorial

---

> [!IMPORTANT]  
> **Please use this as a hint or guide if you're blocked at some point of this machine. I make these files for myself and to help some friends that are also doing these machines.**

> [!NOTE]  
> **Try to get as far as you can by yourself or you will never learn to solve machines alone. Hope you the best and happy hacking <3.**

---

## 1. Enumeration

Start by scanning the target machine to identify open ports and services.

### Nmap Scan
```bash
nmap -sC -sV -T4 -v --min-rate 5000 -Pn $Lab_IP
```
**Results:**

- **Port 80 (HTTP):** Nginx 1.18.0 (Ubuntu)
- **Port 22 (SSH):** OpenSSH 8.2p1 Ubuntu

## 2. Web Application Analysis

Navigate to `http://editorial.htb/` to explore the website.

**Observation:**

- The website is an editorial platform with a "Publish with Us" section that allows file uploads.

### File Upload Functionality

Attempt to upload a reverse shell payload via the file upload feature.

**Observation:**

- Uploaded files are renamed, and their extensions are removed, indicating potential security measures against direct code execution.



## 3. Server-Side Request Forgery (SSRF) Exploitation

### Identifying SSRF Vulnerability

Use Burp Suite to intercept the file upload request and modify the `bookurl` parameter to point to internal services.

**Steps:**

1. Set `bookurl` to `http://127.0.0.1`.
2. Intercept the request and send it to Burp Repeater.
3. Analyze the response for any internal information disclosure.

**Observation:**

- The response indicates that the server fetches resources from the specified URL, confirming an SSRF vulnerability.

---

### Discovering Internal Services

Use Burp Intruder to brute-force internal ports by modifying the `bookurl` parameter to `http://127.0.0.1:<port>`.

**Steps:**

1. Set the payload position at the port number in the URL.
2. Use a payload list ranging from 1 to 65535.
3. Start the attack and monitor responses for anomalies.

**Observation:**

- Port 5000 responds with a 200 OK status, indicating an internal service.



## 4. Accessing Internal API

### Enumerating API Endpoints

Set `bookurl` to `http://127.0.0.1:5000/api/latest/metadata` to retrieve available API endpoints.

**Observation:**

- The API provides several endpoints, including `/messages/authors`.

### Extracting Credentials

Access the `/messages/authors` endpoint to retrieve internal communications.

**Steps:**
```bash
bookurl=http://127.0.0.1:5000/api/latest/metadata/messages/authors
```
1. Analyze the response for sensitive information.

**Observation:**

- The response contains a welcome message with credentials:
  - **Username:** dev
  - **Password:** dev******_dev***** (censored for obvious reasons)

---

## 5. Gaining Initial Access

### SSH Access

Use the extracted credentials to log in via SSH.
```bash
ssh dev@10.10.11.20
```
**Observation:**

- Successful login grants access to the system as the `dev` user.

---

## 6. Privilege Escalation

### Enumerating for Sensitive Information

Search for configuration files or repositories that might contain additional credentials.

**Steps:**
```bash
find ~ -type f -iname '*.conf'
```
1. Check the home directory for hidden files or folders.
2. Look for Git repositories or configuration files.

**Observation:**

- A Git repository contains commit history with potential credentials.

---

### Extracting Higher-Privilege Credentials

Analyze the Git commit history to find credentials for another user.

**Steps:**
```bash
git log
git show <commit-id>
```
**Observation:**

- A commit message reveals credentials for the `editor` user.

---

### Switching User

Use the newly found credentials to switch to the `editor` user.
```bash
su editor
```
**Observation:**

- Successful switch to the `editor` user.

---

### Exploiting GitPython Vulnerability (CVE-2022-24439)

Check for the presence of vulnerable Python scripts that use the GitPython library.

**Steps:**
```bash
grep -r 'import git' /home/editor
```
1. Identify scripts that import `git` from GitPython.

**Observation:**

- A script is found that uses the vulnerable GitPython library.

---

### Achieving Root Access

Exploit the vulnerability to escalate privileges to root.

**Steps:**

1. Modify the vulnerable script to execute arbitrary commands.
2. Run the script to gain a root shell.

**Observation:**

- Theres many ways to do this step to gain root privileges, you can send yourself a root shell with a revsell from [RevShells](https://www.revshells.com/) to give any user sudoers permission.

---

## 7. Capturing Flags

### User Flag

Navigate to the `editor` user's home directory to find the `user.txt` flag.
```bash
cat /home/editor/user.txt
```
---

### Root Flag

Navigate to the `/root` directory to find the `root.txt` flag.
```bash
cat /root/root.txt
```
---

**Congratulations: Machine completed**

---
