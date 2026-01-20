## Target Overview
- Target: Cap
- OS: Linux
- Difficulty: Easy
- Goal: Root 


## Enumeration

I began with a service and version scan using nmap to identify exposed services on the target host.

nmap -sV -A <ip>

The scan revealed three open ports:

21/tcp (FTP – vsftpd 3.0.3)
22/tcp (SSH – OpenSSH 8.2p1)
80/tcp (HTTP – Gunicorn)

<img width="330" height="107" alt="1" src="https://github.com/user-attachments/assets/6ec9b325-ef70-45c5-b942-49b5a90b41f9" />

# FTP Enumeration

I first attempted to log in via FTP using anonymous credentials; however, authentication was not permitted.

<img width="294" height="173" alt="image" src="https://github.com/user-attachments/assets/13a079b4-e0e7-4281-b440-87abec42f5cd" />

As a result, I proceeded to enumerate the HTTP service.

# Web Enumeration

check if it's a website:

<img width="1102" height="57" alt="image" src="https://github.com/user-attachments/assets/6aa24283-c668-453a-9711-2fa0bb9ea1fd" />

So we know the 80 port is the Gunicorn.


and check it through the Chrome:

<img width="1467" height="633" alt="image" src="https://github.com/user-attachments/assets/b3cce7a0-a021-4bd5-a2e5-7235fbc2522e" />


## Credential Harvesting

While browsing the dashboard, I noticed that the /data/{id} endpoint accepted numeric values without authentication checks.

By manually modifying the ID parameter (e.g. /data/0), I was able to access a packet capture file, indicating a lack of proper access control (IDOR).

Browsing to /data/0 does indeed reveal a packet capture with multiple packets.

<img width="1462" height="776" alt="image" src="https://github.com/user-attachments/assets/b84a0eb8-f583-4aec-a936-4c26dcb758f2" />


Since FTP transmits credentials in plaintext, I analyzed the provided PCAP file using Wireshark.

By filtering on the FTP protocol, I was able to extract valid credentials from the USER and PASS commands.

<img width="1195" height="267" alt="image" src="https://github.com/user-attachments/assets/7e515798-7191-40fb-82c8-fcd673401598" />

And I can get the user credentials. User: nathan; password: Buck3tH4TF0RM3!


## Initial Access

password for the ftp and the ssh are the same. So we try

<img width="259" height="24" alt="image" src="https://github.com/user-attachments/assets/aa62df58-90f6-48c3-af35-741a60ffa648" />


and we can see we are as the nathan user here.

<img width="529" height="37" alt="image" src="https://github.com/user-attachments/assets/12104da0-0e6e-4922-b334-04d000e31d19" />



## Privilege Escalation
We try to find the urls with the Capabilities.

<img width="883" height="102" alt="image" src="https://github.com/user-attachments/assets/c9e660ef-d01f-4ade-aedf-f5db4cf3723f" />

The binary /usr/bin/python3.8 was assigned the cap_setuid capability, allowing it to change its effective UID.

Okay we can use this to get the root 

<img width="700" height="37" alt="image" src="https://github.com/user-attachments/assets/b71cb933-8081-4632-aef2-4d34ac56d922" />


## Attack Chain Summary 
An IDOR vulnerability in the web application exposed a packet capture containing FTP credentials, which were reused for SSH access.
The compromised user account had a misconfigured Linux capability on Python, allowing privilege escalation to root.


## Lessons Learned
Sensitive data such as PCAP files should never be exposed through predictable URLs.







 
