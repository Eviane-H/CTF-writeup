## Target Overview
- Target: Cap
- OS: Linux
- Difficulty: Easy
- Goal: Root 


## Enumeration
nmap -sV -A ip

<img width="330" height="107" alt="1" src="https://github.com/user-attachments/assets/6ec9b325-ef70-45c5-b942-49b5a90b41f9" />

try the anonymous ftp connection:

<img width="294" height="173" alt="image" src="https://github.com/user-attachments/assets/13a079b4-e0e7-4281-b440-87abec42f5cd" />


failed, okay and we check the http.

check if it's a website:

<img width="1102" height="57" alt="image" src="https://github.com/user-attachments/assets/6aa24283-c668-453a-9711-2fa0bb9ea1fd" />

So we know the 80 port is the Gunicorn.


and check it through the Chrome:

<img width="1467" height="633" alt="image" src="https://github.com/user-attachments/assets/b3cce7a0-a021-4bd5-a2e5-7235fbc2522e" />


and we can check the Security Snapshot, and we notice the url changed. Very interesting, if we click or refresh the website we can notice the url is changed. Especially the last /id

Browsing to /data/0 does indeed reveal a packet capture with multiple packets.

<img width="1462" height="776" alt="image" src="https://github.com/user-attachments/assets/b84a0eb8-f583-4aec-a936-4c26dcb758f2" />


Use the Wireshark to capture the FTP traffic.

<img width="1195" height="267" alt="image" src="https://github.com/user-attachments/assets/7e515798-7191-40fb-82c8-fcd673401598" />

And we can get the user credentials. User: nathan; password: Buck3tH4TF0RM3!

password for the ftp and the ssh are the same. So we try

<img width="259" height="24" alt="image" src="https://github.com/user-attachments/assets/aa62df58-90f6-48c3-af35-741a60ffa648" />


and we can see we are as the nathan user here.
<img width="529" height="37" alt="image" src="https://github.com/user-attachments/assets/12104da0-0e6e-4922-b334-04d000e31d19" />


## Privilege Escalation
We try to find the urls with the Capabilities.

<img width="883" height="102" alt="image" src="https://github.com/user-attachments/assets/c9e660ef-d01f-4ade-aedf-f5db4cf3723f" />

Here we find the key words: cap_setuid: /usr/bin/python3.8

Okay we can use this to get the root 

<img width="700" height="37" alt="image" src="https://github.com/user-attachments/assets/b71cb933-8081-4632-aef2-4d34ac56d922" />


## Attack Chain Summary 
1. from Http to find the vulnerable parts.
2. From the urls we can shift the different users and their information. And use the Wireshark to get the FTP traffic and get the username and pw.
3. Find the files with Capabilities to escalate privilege.


## Lessons Learned
From urls to change the user and check the information. 







 
