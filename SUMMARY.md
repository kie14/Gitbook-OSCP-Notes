# Table of contents

## General

* [Common Operations (tty, SSH, rbash)](README.md)
  * [Linux commands](general/common-operations-tty-ssh-rbash/linux-commands.md)
  * [File transfer](general/common-operations-tty-ssh-rbash/file-transfer.md)
  * [run powershell](general/common-operations-tty-ssh-rbash/run-powershell.md)
* [Post-exploit check](general/post-exploit-check.md)
* [Privesc](general/privesc/README.md)
  * [Linux](general/privesc/linux.md)
  * [Windows](general/privesc/windows/README.md)
    * [UAC eventvwr](general/privesc/windows/uac-eventvwr.md)
* [Metasploit](general/metasploit.md)
* [Python](general/python.md)

## Reconnaissance

* [DNS 53](reconnaissance/dns-53.md)
* [Nmap / Masscan / Autorecon](reconnaissance/nmap-masscan-autorecon.md)
* [Searchsploit](reconnaissance/searchsploit.md)
* [Services](reconnaissance/services/README.md)
  * [SMTP 25](reconnaissance/services/smtp-25.md)
  * [SMB 139, 445](reconnaissance/services/smb-139-445.md)
  * [MSSQL 1433](reconnaissance/services/mssql-1433.md)
  * [MYSQL 3306](reconnaissance/services/mysql-3306.md)
  * [TFTP UDP 69](reconnaissance/services/tftp-udp-69.md)
* [Target Host Enumeration](reconnaissance/target-host-enumeration.md)

## Common Attack

* [Buffer Overflow](common-attack/buffer-overflow.md)
* [MS Office](common-attack/ms-office.md)

## Web Attack

* [Web Enumeration](web-attack/web-enumeration.md)
* [Webshell to stable shell](web-attack/webshell-to-stable-shell/README.md)
  * [WordPress](web-attack/webshell-to-stable-shell/wordpress.md)
* [SQL Injections & Enum](web-attack/sql-injections-and-enum.md)

## Password Attack

* [Bruteforce](password-attack/bruteforce.md)
* [Hash Crack](password-attack/hash-crack.md)
* [Custom Wordlist](password-attack/custom-wordlist.md)
* [SSH Authorized\_key reuse](password-attack/ssh-authorized\_key-reuse/README.md)
  * [persisting ssh login with ssh-keygen](password-attack/ssh-authorized\_key-reuse/persisting-ssh-login-with-ssh-keygen.md)

## AD Attack

* [Enumeration](ad-attack/enumeration/README.md)
  * [Flow](ad-attack/enumeration/flow.md)
* [Ticket manipulations mimi](ad-attack/ticket-manipulations-mimi/README.md)
  * [Silver / Golden](ad-attack/ticket-manipulations-mimi/silver-golden.md)
* [DCOM P.664 / AD DCOM .one](ad-attack/dcom-p.664-ad-dcom-.one.md)
* [Password hash dump and reuse](ad-attack/password-hash-dump-and-reuse.md)
* [Impersonating token & pivot](ad-attack/impersonating-token-and-pivot/README.md)
  * [Persisting access with DC admin](ad-attack/impersonating-token-and-pivot/persisting-access-with-dc-admin.md)
* [Tunneling / (with SSH key)](ad-attack/tunneling-with-ssh-key.md)
