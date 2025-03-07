Thinking about the interesting service on the next target that we discovered in the previous task, pick a pivoting technique and use it to connect to this service, using the web browser on your attacking machine! 

As a word of advice: *sshuttle* is highly recommended for creating an initial access point into the rest of the network. This is because the firewall on the CentOS target will prove problematic with some of the techniques shown here. We will learn how to mitigate against this later in the room, although if you're comfortable opening up a port using firewalld then port forwarding or a proxy would also work.

---

> [!question]
>1. What is the name of the program running the service? Hint: When you first connect to the service you will see an error screen with three expected routing patterns given. The second pattern (without the symbols at the start and end) is the answer to this question. Append it to the URL to get to a login screen.
>`Gitstack`
>2. Head to the login screen of this application. This can be done by adding the answer to the previous question on at the end of the url, e.g. if using sshuttle:  `http://IP/ANSWER`. When navigating to this URI, we are given the following login page:
>![[Pivoting-20241214145333847.webp]]
>Do these default credentials work (Aye/Nay)?
>`Nay`
>Shucks -- it couldn't be that easy, huh? Back to the drawing board then! Use the command: `searchsploit SERVICENAME`, on Kali to search for exploits related to this service.
>![[Pivoting-20241214145850612.webp]]
>3. You will see that there are three publicly available exploits. There is one Python RCE exploit for version 2.3.10 of the service. What is the EDB ID number of this exploit?
>`43777`


# Your job

#Attacking_Machine 
- Create a sshuttle VPN through which we could move inside the network.

```
sshuttle -r root@10.200.84.200 --ssh-cmd "ssh -i id_rsa" 10.200.84.0/24 -x 10.200.84.200
```

![[Screenshot from 2025-03-07 19-28-39.png]]



**Next step:** [[Visit the webpage of git-serv]]





