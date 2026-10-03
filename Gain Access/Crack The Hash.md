# TryHackMe - Crack the hash
> personal notes and learning documentation for Crack the hash room 

## About Room
| Item | Details|
|---|---|
|platform|TryHackMe|
|Name|Crack the hash|
|Url|https://tryhackme.com/room/crackthehash|
|Category|Gain Access|
|Difficulty|Easy|
|Focus|Types of hash and crack tools|

---

## Objectives

I learned:
-   Type of hashes
-   Hash tools
-   Others 
 
---

# 1. Hashing  

## What is a Hash?
A hash is a function that transforms an input (plain text) into a fixed-length string of characters, called the hash value or digest.

## What are the components of hashing?
1. **Key**: the input. It can be a string, numbers, or a mix of both.
2. **Hash function**: maps the original value to a hashed value, which is used as an index in the table.
3. **Hash table**: an array of lists. It stores values corresponding to the keys.

## Difference between Hash, Encryption, and Encoding

### Hash
A one-way transformation. It cannot be reversed to get the original input. If someone can reverse it, that is a vulnerability.

### Encryption
A two-way transformation. Data is locked with a key and can be unlocked later with the correct key. It is used to protect data in transit, for example between a browser and a website (HTTPS).

### Encoding
A way to convert data from one format to another so it can be stored or transferred easily (for example text to ASCII). It is not secure, because anyone can decode it.

## Types of hashing
There are many categories (raw hashes, network, OS, databases, some protocols, etc.).
Examples: MD4, MD5, SHA-1, SHA-256, bcrypt.

Each one has its own algorithm and configuration. Some are used **with salt** (a random value added to the password before hashing) and some **without salt**.

## Tool
For a dictionary attack, you need a file that contains many possible passwords to try.
**rockyou.txt** is a well-known wordlist with about 14 million passwords leaked from the RockYou company.
Its path: `/usr/share/wordlists/rockyou.txt`

```bash
# first you get the hashed value like 82fdHJG785BfdgBjfld78sj44hskd?X>
# you must identify it so here you are
# option one
hashcat --identify "<hashed_value>" 
# option two
hashcat-identifier "<hashed_value>"

# after know hash name use one of them know the type mode of the hash used by google it or use
hashcat -hh # look for hash mode

# you can use this site for crack "https://crackstation.net/" or by your hashcat tool
hashcat -m <hash_mode> "<hashed_value>" /usr/share/wordlists/rockyou.txt
```

## Key Takeaways
- Hash functions are one-way; encryption is two-way.
- `hashcat --identify` helps detect the hash type before cracking.
- rockyou.txt is a common wordlist for dictionary attacks.
- Hash mode numbers (-m) must match the hash type exactly, or hashcat won't crack it.

## References
1.  https://www.geeksforgeeks.org/dsa/introduction-to-hashing-2
2.  https://tryhackme.com/room/crackthehash

#### By: Fares Mohamed Abd Elaty