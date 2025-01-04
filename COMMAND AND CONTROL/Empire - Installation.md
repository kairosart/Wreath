Starkiller and Empire (via Docker) are both already installed on the TryHackMe AttackBox, so if you are not using your own machine then you can skip this task.  

---

That said, if we are using our own VM then we need to install both Empire and Starkiller before we use them. Ultimately it's up to you which you use; both will be covered in the tasks. Regardless, we need to install at least Empire.

In ages past this was a much more complicated process involving the Git repo and setup scripts. These days it's easiest to just use the apt repositories:

`sudo apt install powershell-empire starkiller`  

With both installed, we now need to start an Empire server. This should stay running in the background whenever we want to use either the Empire Client or Starkiller:  
`sudo powershell-empire server`  

The server should now start:

![[Empire - Installation-20250101142029621.webp]]

It would be more common to have an Empire server running on a separate C2 server (usually hosted locally with cloud infrastructure linking back to receive inbound connections through). Multiple pentesters or red teamers would then be able to connect to a single central server.

This is entirely overkill for our uses here -- instead we will just run both the server and the client application(s) on the single Kali instance.

---

## Empire client

With the server started, let's get the Empire CLI Client working. You are welcome to skip this if you would prefer to work exclusively in Starkiller.

Starting the Empire CLI Client is as easy as:

```
$ powershell-empire client
```


![[Empire - Installation-20250101142337561.webp]]

With the server instance hosted locally this should connect automatically by default. If the Empire server was on a different machine then you would need to either change the connection information in the `/usr/share/powershell-empire/empire/client/config.yaml` file, or connect manually from the Empire CLI Client using `connect HOSTNAME --username=USERNAME --password=PASSWORD`.

---
## Starkiller

Starkiller is an Electron app which works by connecting to the REST API exposed by the Empire server

With an Empire server running, we can start Starkiller by executing "`starkiller`" in a new terminal window:

![[Empire - Installation-20250101142448622.webp]]

From here we need to sign into the REST API we deployed previously. By default this runs on `https://localhost:1337`, with a username of `empireadmin` and a password of `password123`:

![[Empire - Installation-20250101142527436.webp]]


**Next step: **[[Empire - Overview]]