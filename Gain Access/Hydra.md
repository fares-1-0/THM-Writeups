# TryHackMe - Hydra
> personal notes and learning documentation for Hydra room

## About Room
| Item | Details |
|---|---|
| Platform | TryHackMe |
| Name | Hydra |
| Url | https://tryhackme.com/room/hydra |
| Category | Gain Access |
| Difficulty | Easy |
| Focus | Brute-forcing login credentials |

---

## Objectives

I learned:
-   What Hydra is and when to use it
-   Hydra components (flags/options)
-   Brute-forcing SSH
-   Brute-forcing a web login form (POST method)

---

# 1. Hydra

## What is Hydra?
Hydra is a brute-force tool. It tries many usernames and passwords against a login service (SSH, FTP, web form, etc.) until it finds the correct combination, or until the list runs out.

## What are the components (options) of Hydra ?
1. **-l**: a single username to try.
2. **-P**: a path to a wordlist to try, for example rockyou.txt
3. **Target**: the service address, for example an IP or `protocol://IP`.
4. **-t**: number of parallel threads to use. More threads = faster, but can be detected and blocked.
5. **-V**: verbose mode, shows every attempt Hydra makes.

## Hydra in bash 

```bash
# general syntax 
hydra -l <username> -P <wordlist> <target> <protocol>

# ssh 
hydra -l <username> -P <wordlist_path> <target_ip> -t 4 ssh

# http post form (check request type + field names + fail message from browser dev tools first)
hydra -l <username> -P <wordlist> <target_ip> http-post-form "<path>:<login_fields>:<fail_string>"

# Ex: for login file like the room
hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.113.152.34 http-post-form "/login:username=^USER^&password=^PASS^:F=incorrect" -V
```
---

## Key Takeaways
- Hydra needs 3 things at minimum: a username (or list), a password list, and a target+protocol.
- `-t` controls speed (threads), but too many threads can get you blocked or make the service unstable.
- For web forms, you must first inspect the request (GET/POST, field names, fail message) before building the Hydra command — Hydra can't guess this part. you can do that by open inspect and go for Network and try to enter any username and password and see what happened  
- `F=<text>` is the key to brute-forcing forms correctly; if it's wrong, Hydra won't detect failed/successful attempts properly.
- Always check if the service runs on a non-default port (`-s`).

## Flags for THM 
- Flag 1 (web form): `THM{REDACTED}`
- Flag 2 (SSH): `THM{REDACTED}`

## References
1.  https://tryhackme.com/room/hydra
2.  https://youtu.be/B_TTVk9L9ew?si=cwRJFxkM1QwbRBqB

#### By: Fares Mohamed Abd Elaty