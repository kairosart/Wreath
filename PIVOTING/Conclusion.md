That was a long and theory-heavy section, so kudos for getting this far!

The big take away from this section is: there are _many_ different ways to pivot through a network. Further research in your own time is highly recommended, as there are a great many interesting techniques which we haven't had time to cover here (for example, on a fully rooted target, it's possible to use the installed firewall -- e.g. iptables or Windows Firewall -- to create entry points into an otherwise inaccessible network. Equally, it's possible to set up a route manually in the routing table of your attacking machine to, routing your traffic into the target network without requiring a proxy-tool like Proxychains or Foxyproxy).

As a summary of the tools in this section:

- Proxychains and FoxyProxy are used to access a proxy created with one of the other tools
- SSH can be used to create both port forwards, and proxies
- plink.exe is an SSH client for Windows, allowing you to create reverse SSH connections on Windows
- Socat is a good option for redirecting connections, and can be used to create port forwards in a variety of different ways
- Chisel can do the exact same thing as with SSH portforwarding/tunneling, but doesn't require SSH access on the box
- sshuttle is a nicer way to create a proxy when we have SSH access on a target  
    

Pivoting truly is a vast topic; however, hopefully you've learnt something by covering the theory in this section!

This is a good time to experiment with the techniques demonstrated in the pivoting section, so play around with them all and make sure you're comfortable with them before moving on.

> [!Note]
>If using socat, or any other techniques that open up a port on the compromised host (in the course of this network), please make sure to use a port above 15000, for the sake of other users in earlier sections of the course.


