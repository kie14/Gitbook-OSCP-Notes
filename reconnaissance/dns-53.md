# DNS 53

### DNS Server enumeration

```
sudo nmap -Pn -p 53 ###.###.###.0/24 | grep "open" -B 4
```

### Kali /etc/resolv.conf (root)

To use `#` to quote out existing `nameserver {DNS ip}` and add `nameserver {discovered DNS}` for being able to reverse lookup subdomains of the network.

### Forward lookup (subdomain -> IP)

```
for ad in $(cat {list.txt}); do host $ad.domain.com; done | grep -v "not found"
# Replace {} with a valid list of subdomains to try 
```

```
host {url.com}

gnu.org has address 209.51.188.148
gnu.org has IPv6 address 2001:470:142:3::a
gnu.org mail is handled by 10 eggs.gnu.org.
```

### Reverse lookup (IP -> subdomain)

```
for ip in $(seq 1 256); do host xx.xx.xx.$ip; done | grep -v "not found"
# In $(seq x y) x=starting# y=ending#
```

```
host {lookup ip} ({specific DNS})

host 10.11.1.71 10.11.1.220
Using domain server:
Name: 10.11.1.220
Address: 10.11.1.220#53
Aliases: 

71.1.11.10.in-addr.arpa domain name pointer alpha.thinc.local

```

### List all subdomains

```
host -l -a {domain.com} ({specific DNS}) 
```
