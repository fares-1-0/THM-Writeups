# TryHackMe - Network Services
> personal notes and learning documentation for Network Services room 

## About Room
| Item | Details|
|---|---|
|platform| TryHackMe
|Name| Network Services
|Url| 'https://tryhackme.com/room/networkservices'
|Category| Network 
|Difficulty| Easy
|Foucs| Understand These Protocols 

---

## Objectives

I learned and practiced how to use:
-   SMB 
 

---

# 1. SMB

## What is SMB?
Ans:   
SMB- Server Message Block Protocol - is a client-Server Protocol used to share the access of files, printers and others on a network and also known as a ***response-request protocol***

## What is the SMB protocol used for?
Ans:   
to facilitate shared access to the resources on a network 
Servers provide shared resources on the network, and clients can access and use them, such as shared files and printers
Ex: Company server can provide one printer, for many employees who are have access to log to printer 

## How does the SMB protocol work? 
Ans:  
Client send SMB request to server and when server recieve and accecpt the request, he sends a SMP responce that establishing the communication channel and we can call it a session between them 2-way conversation

### Ports used by SMB
- TCP 139, 445 
- UDP 137

## Enumeration 
Ans:    
Gathering information about usersname, passwords, OS verisons, application data, hostnames and any valuable info could help the attacker
Use ```enum4linux``` to gather info about name of machine, user, workgroup, os and its versions, shared files and anthor valuable information  

like we see in smp workshop by knowing target ip I can do this:
1. Write command ```enum4linux [options] <ip>``` 
2. Try to exploit smb by anonymous user  ``smbclient -L //<ip> -U anonymous``
    2.1 See shared directories and try to access them anonymous   
    2.2 Add file_name to enter it ``smbclient -L //<ip>/<file_name> -U anonymous``
3. Then find information that can give you information to exploit and attack the machine 
Ex:[ username like 'cactus' and file like 'id_rsa' could give me ssh shell] 
  

### Tool  
``` Bash
# Standard enum4linux
enum4linux [options] <ip> 
# [options]
# -U to get user list, -G to get grosub list, -M to get machine list, ..., -a for all above

# To get a session 
sudo smbclient [options] //<ip>/[file_name]   
# -L for file shared access

```


## Key Tackaways


## References