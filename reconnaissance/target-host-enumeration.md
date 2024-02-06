---
description: 'Common writable dir: /tmp ; /var/tmp'
---

# Target Host Enumeration

### Common flow

#### Linux

{% hint style="info" %}
lookfor /home/user/.bash\_histroy for creds
{% endhint %}

OS: `cat /etc/*-release`

Architecture / Kernel: `uname -a`

Who: `id`

Where: `pwd`

All account: `cat /etc/passwd`

Shell user: `grep -vE "nologin|false" /etc/passwd`

Current process: `ps aux`

Active network service: `netstat -antup` _(services that are listed here but weren't detected could mean a firewall rule blocked its detection)_

Mount share: `cat /etc/fstab`

Custom scripts: `ls /mnt/scripts`&#x20;

Scheduled tasks: `ls -lah /etc/cron*` ; `cat /etc/crontab`

Installed package: `dpkg -l` (debian) ; `rpm -l` or `-qa` (cent/opensuse)

Firewall: `/etc/iptables`

#### Windows

{% hint style="info" %}
C:\Users\cureentuser\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost\_history.txt
{% endhint %}

OS: `systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"`

Who: `whoami /groups` `/priv` `gpresult /R`

Active network service: `netstat -abno`
