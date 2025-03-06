We have access to the prov-serv server via SSH, but we don’t know whether there are more machines on the internal network or not.

#Attacking_Machine 
- Upload  a static Nmap executable to the “tmp” directory of the prod-server.

```
scp -i id_rsa nmap-KAIROS root@10.200.84.200:/tmp/nmap-KAIROS
nmap-KAIROS 
```

#Reverse_Shell 
- Scan the network with nmap.

```
./nmap-KAIROS -sn 10.200.84.1/24
```

![[Screenshot from 2025-03-06 21-02-13.png]]

After scanning the network, four other hosts could be identified. But only the hosts “10.200.84.100” and “10.200.84.150” were inside the scope of the penetration test.

## Scan the local network

Scan the the two machine into the local network.

```
./nmap-KAIROS -oG Discovery_subnet  10.200.84.100 10.200.84.150
```

![[Screenshot from 2025-03-06 21-23-47.png]]

- The host with the IP 10.200.84.100 has all ports closed. 
- The host with the IP 10.200.84.150 has the  ports 80, 3389 and 5985 opened.
- Based on the simple fingerprinting (*ms-wbt-server*) that Nmap has done, we could assume that the host is running Windows.


**Next section:** [[What is Pivoting?]]