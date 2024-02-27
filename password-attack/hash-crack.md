---
description: /usr/share/wordlists/rockyou.txt
---

# Hash Crack

### Crack NTLM

```

john --wordlist={word list} {pwdhash.txt} --format=NT

hashcat -m 1000 hash rockyou.txt --force
```

### Hashcat crack rar

```
rar2john xx.rar > hash.txt
zip2john
trim unnecessary ":" data
hashcat -m 23800 --force hash.txt /usr/share/wordlists/rockyou.txt --quiet 
```

[https://hashcat.net/wiki/doku.php?id=example\_hashes](https://hashcat.net/wiki/doku.php?id=example\_hashes)

```
hashcat --help | grep "apr" {pattern} to check -m 
```

```
hashcat -I //display hardware info
hashcat -d # //specify the device for cracking
```

### Linux shadow

```
unshadow passwd-file.txt shadow-file.txt (/etc/passwd & /etc/shadow) > unshadowed.txt
john --rules --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt
```

