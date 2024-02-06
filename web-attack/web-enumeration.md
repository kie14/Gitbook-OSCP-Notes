---
description: /var/www/html
---

# Web Enumeration

### Dirbuster

Wordlists:

<pre data-overflow="wrap"><code><strong>/usr/share/dirb/wordlists/common.txt
</strong>/usr/share/seclists/Discovery/Web-Content/..
/usr/share/dirbuster/wordlists/directory-list-2.3-small.txt

gobuster dir -u http://192.168.120.187:8080 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x zip -t 50
</code></pre>

### ffuf

{% code overflow="wrap" %}
```
ffuf -w /usr/share/dirb/wordlists/common.txt -u http://10.11.1.133/xxxx/xxxx/FUZZ 
```
{% endcode %}

### curl as search engine for robots.txt

{% code overflow="wrap" %}
```
curl -A "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)" http://10.11.1.39/robots.txt 

```
{% endcode %}

### wordpress wp-login username enum

```
wpscan -e u --url {}
wpscan --url http://dc-2/ --enumerate ap,u,t
```
