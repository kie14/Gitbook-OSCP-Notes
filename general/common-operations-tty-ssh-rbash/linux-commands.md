---
description: sudo apt-get update && sudo apt-get install ...
---

# Linux commands

### Port status

```
ss -antup
```

### Active network service

```
lsof -i -P -n

sudo netstat -tulpn
```

### Find files / folder

```
find / -name "*.php" 2>/dev/null

find / -type d -name "www" 2>/dev/null
```

### Copy and Rename

```
cp /tmp/a /tmp2/a

mv index.html rev.php
```

### Make empty file&#x20;

```
touch newfile.html
```
