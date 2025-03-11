We will soon be moving on to the final teaching point of this network: Anti-virus evasion techniques.

Before we can do that, however, we first need to scope out the final target!

We know from the briefing that this target is likely to be the other Windows machine on the network. By process of elimination we can tell that this is Thomas' PC which he told us has antivirus software installed. If we're very lucky it will be out of date though!

As always, we need to enumerate the target before we can do anything else, but how can we do this from a compromised Windows host? As mentioned way back in the Pivoting Enumeration task, Nmap won't work on Windows unless it's been properly installed on the target. Scanning through one proxy is bad, but at this point we'd be scanning through _two_ proxies, which would be unbearable. We could write a tool to do it for us, but let's leave that for the time being (there will be more than enough coding in the upcoming section as it is!). Instead, let's look closer to home and ask one burning question:

**How do Empire Modules work?**

For the most part Empire modules are quite literally just scripts (usually in PowerShell) that are executed by the framework through an active agent.  In other words, these are just PowerShell scripts, and we have PowerShell access to the target.

For the sake of learning, let's upload the Empire Port Scanning script and execute it manually on the target.


---

In our current situation (on an isolated target, communicating through a jumpserver), under normal circumstances uploading tools manually would usually be something of a chore -- think relays and webservers. Fortunately evil-winrm gives us several easy options for transferring and including tools.

**Upload/Download:** 
The first option available to us is the in-built Upload/Download feature built into the tool. From within evil-winrm we can use `upload LOCAL_FILEPATH REMOTE_FILEPATH` to upload files to the target. Conversely, we can use `download REMOTE_FILEPATH LOCAL_FILEPATH` to download files back from the target. These could come in handy if we, say, wanted to upload a tool to the target, save the results from running it to a log file, then download the log file back to our attacking machine for storage. In both instances if we miss out the destination filepath (e.g. the remote filepath on upload, or the local filepath on download), the tool will be uploaded into our current working directory.  

For example:

![[Enumeration-20250123210128228.webp]]

In this example we upload an example tool (`nc.exe`) to `C:\Windows\Temp`, we then create a new file (`demo.txt`) and download it to the current working directory. Note that in the real world using the `C:\Windows\Temp` directory is often a bad idea as it's flagged as a common location for hackers to upload tools. In this case we are using it to keep the box neat and tidy for other users.

**Local Scripts:**  
Uploading tools is all well and good, but if the tool happens to be a PowerShell script then there is another (even more convenient) method. If you check the help menu for evil-winrm, you will see an interesting `-s` option. This allows us to specify a local directory containing PowerShell scripts -- these scripts will be made accessible for us to import directly into memory using our evil-winrm session (meaning they don't need to touch the disk at all). For example, if we happened to have our scripts located at `/opt/scripts`, we could include them in the connection with:  
`evil-winrm -u USERNAME  -p PASSWORD -i IP -s /opt/scripts`  

Let's use this option to include the Empire Portscan module.

The Empire scripts are stored at `/usr/share/powershell-empire/empire/server/data/module_source/situational_awareness/network/` if you installed using apt as recommended. A copy of this tool is also included in the zipfile attached to Task 1, or can be downloaded [here](https://github.com/BC-SECURITY/Empire/blob/master/empire/server/data/module_source/situational_awareness/network/Invoke-Portscan.ps1), if you can't find it locally.  

Regardless, we can now sign in as the Administrator using the password hash discovered previously, including the Empire network scanning scripts:  
`evil-winrm -u Administrator -H HASH -i IP -s EMPIRE_DIR`  

Type `Invoke-Portscan.ps1` and press enter to initialise the script.  

Now if we type `Get-Help Invoke-Portscan` we should see the help menu for the tool without having to import or upload anything manually!

![[Enumeration-20250123210355909.webp]]

The Empire Portscan module is designed to be similar to Nmap in terms of syntax. You are encouraged to read through the full help menu for the tool; however, we only need two switches: `-Hosts` and `-TopPorts`. We _could_ use the `-Ports` switch and just scan a range of ports, but for the sake of speed we can use the -TopPorts switch to scan a user-specified number of the most commonly open ports. For example, `-TopPorts 50` would scan the 50 most commonly open ports.

The full command would then look like this (using the top 50 ports and our example of 172.16.0.10):  
`Invoke-Portscan -Hosts 172.16.0.10 -TopPorts 50`


---

# Your job

1. Location for powershell-empire scripts. 
#Attacking_Machine 
```
$ ls -la /usr/share/powershell-empire/empire/server/data/module_source/situational_awareness/network
```


2. Sign in as the Administrator using the password hash. 
#Attacking_Machine 
```
$ evil-winrm -u Administrator -H 37db630168e5f82aafa8461e05c6bbd1 -i 10.200.84.150 -s /usr/share/powershell-empire/empire/server/data/module_source/situational_awareness/network
```

3. Run powershell-empire script in evil-winrm shell. 
#Evil-WinRM_shell 
```
Invoke-Portscan.ps1
```

4. Scan the other Windows machine. 
#Evil-WinRM_shell 
```
Invoke-Portscan -Hosts 10.200.84.100 -TopPorts 50
```

![[Enumeration-20250123212128194.webp]]

> [!question]
>Scan the top 50 ports of the last IP address you found in Task 17. Which ports are open (lowest to highest, separated by commas)?
>`80, 3889`


**Next step:**  [[PERSONAL PC/Pivoting|Pivoting]]
