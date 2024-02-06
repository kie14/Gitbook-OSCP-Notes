# Nmap / Masscan / Autorecon

### General

```
-sS stealth scan faster & slient 
-sT 3way hand shake connect scan
-sU udp scan (can use with -sS)
-sn sweeping {ip.1-254}
-p specify port
-A version detection/script scan/traceroute
--top-ports=# (scan top # common ports)
-O os fingerprinting
-sV service banner (+ -sT + -A)
--open (opened port only)
-sC run with default script
-oG {filename} save scan results in grep format
-Pn directly scan machine w/o pinging
--top-ports=20
```

### Common step-up scans

#### Quick enumeration

```
sudo nmap -sS -p- {ip} -vv -Pn --open -Pn

PORT      STATE SERVICE REASON
80/tcp    open  http    syn-ack ttl 63
```

```
proxychains nmap --top-ports=20 -sT -Pn 10.5.5.20
```

#### Deep enumeration

```
sudo nmap -sV -p{interested,port,#} -sC {ip} -O -sT -Pn -vv   

PORT     STATE SERVICE     REASON  VERSION
80/tcp   open  http        syn-ack Apache httpd 2.2.20 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.2.20 (Ubuntu)
```

### Scripts

Location: `/usr/share/nmap/scripts`

Search: `ls -l /usr/share/nmap/scripts/{searchterm}*`

Use: Add the parameter `--script {scriptname}` or `{service}*` for running all script matched

### No Nmap approach

{% code overflow="wrap" %}
```bash
#Win
for /L %i in (1,1,255) do @ping -n 1 -w 200 10.5.5.%i > nul && echo 10.5.5.%i is up.

#Linux
for i in {1..254} ;do (ping -c 1 -w 200 10.2.2.$i | grep "bytes from" &) ;done
```
{% endcode %}

Enum from pivoting machine to next target machine

```bash
#!/bin/bash
host=10.5.5.11
for port in {1..65535}; do
timeout .1 bash -c "echo >/dev/tcp/$host/$port" &&
echo "port $port is open"
done
echo "Done"
#enumport.sh
```

### Autorecon

{% embed url="https://www.hackingarticles.in/comprehensive-guide-to-autorecon/" %}

```bash
sudo $(which autorecon) ip -v --dirbuster.threads 50

#through tunneling machine to recon subnet
sudo proxychains $(which autorecon) subnetip -v --proxychains
```

