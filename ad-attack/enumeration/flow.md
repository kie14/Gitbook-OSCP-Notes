# Flow

1. Foothold to Client 1, privesc & create persistency (create user / [ssh key](../../password-attack/ssh-authorized\_key-reuse/persisting-ssh-login-with-ssh-keygen.md))
2. Enum for Client 2 infomation and creds (e.g. from config files & logs & histories)
   1. or the DB connection (with DB creds) could lead to Client 2 access ([mssql xp\_cmdshell](../../reconnaissance/services/mssql-1433.md))
3. When Client 1 is able to access Client 2 subnet, setup [Proxychains ](../tunneling-with-ssh-key.md#ssh-from-victim-to-kali)in Client 1 to Client 2 subnet for kali's access to Client 2 subnet
   1. or [Proxychains ](../tunneling-with-ssh-key.md#ssh-to-victim-from-kali)in kali to Client 1 when it enabled SSH (with creds / ssh key placed)
   2. Proxychains nmap
   3. If it is DB connection, just tunnel the DB port (-L from kali / -R from client 2)
4. Kali access to Client 2 and privesc for dumping creds
   1. may need upload and run a rev shell if access Client 2 thru DB
5. In Client 2, [dump hash](../password-hash-dump-and-reuse.md) from SAM for local account creds, PtH for access if can't crack
   1. Ensure [persistency ](../../general/privesc/windows/#create-user-for-persistency-as-admin-system)in Client 2 (e.g. having pwd / hash / new admin)
   2. [Look for](./#tradition) Admin accounts&#x20;
   3. [RDP ](../../#rdp)if possible
   4. May find also domain.loca\user accounts from SAM dumps
   5. CME / PsExec PtH
6. In Client 2, enum for DC info & user, PsExec
   1. [Dump hash](../password-hash-dump-and-reuse.md) from LSASS for logged on user account creds, PtH if can't crack
   2. Look for domain.local\admin creds
   3. [Enum SPN](../ticket-manipulations-mimi/) and kerberoast to access service account&#x20;
   4. [Create silver / golden ticket](../ticket-manipulations-mimi/silver-golden.md) with service hash & user account SID (if is domain admin)
7. Or [token impersonation](../impersonating-token-and-pivot/) to access DC&#x20;
