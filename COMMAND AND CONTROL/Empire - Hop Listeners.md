As mentioned previously, Empire agents can't be proxied with a socat relay or any equivalent redirects; but there must be a way to get an agent back from a target with no outbound access, right?

The answer is yes. We use something called a Hop Listener.

Hop Listeners create what looks like a regular listener in our list of listeners (like the http listener we used before); however, rather than opening a port to receive a connection, hop listeners create files to be copied across to the compromised "jump" server and served from there. These files contain instructions to connect back to a normal (usually HTTP) listener on our attacking machine. As such, the hop listener in the listeners menu can be thought of as more of a placeholder -- a reference to be used when generating stagers.

If this doesn't make much sense just now, don't worry! Hopefully it will once we have worked through an example.

The hop listener we will be working with is the most common kind: the `http_hop` listener.

When created, this will create a set of `.php` files which must be uploaded to the jumpserver (our compromised webserver) and served by a HTTP server. Under normal circumstances this would be a trivial task as the compromised server already has a webserver running; however, out of courtesy to anyone else attempting the network, we will not be using the installed webserver.


---
## Empire client

Let's first look at starting the listener in Empire CLI.

Switch into the context of the listener using `uselistener http_hop` from the main Empire menu (you may need to use `back` a few times to get out of any agents, etc). There are a few options we're interested in here:

![[Empire - Hop Listeners-20250103131713292.webp]]

Specifically we need:-

- A **RedirectListener** -- this is a regular listener to forward any received agents to. Think of the hop listener as being something like a relay on the compromised server; we still need to catch it with something! You could use the listener you set up earlier for this, or create an entirely new HTTP listener using the same steps we used earlier. Make sure that this matches up with the name of an already active listener though!  

- A **Host** -- the IP of the compromised webserver (`.200`).
-
- A **Port** -- this is the port which will be used for the webserver hosting our hop files. Pick a random port here (above 15000), but remember it!

When filled in, our options should look something like this:

![[Empire - Hop Listeners-20250103132022212.webp]]

As shown in the screenshot, we then once again use `execute` to start the listener.

- This will have written a variety of files into a new `http_hop` directory in `/tmp` of our **attacking machine**. 
- We will need to replicate this file structure on our **jump server** (the compromised `.200` webserver) when we serve the files. Notice that these files (`news.php`, `admin/get.php`, and `login/process.php`) would not look out of place amongst genuine web application files -- and indeed could easily be discretely merged into an existing webapp.


---

## Starkiller

Let's look at setting up a `http_hop` listener in Starkiller.

By this stage you should be fairly familiar with this process, so we will go through this quickly.

Switch back to the Listeners menu in Starkiller using the menu at the left-hand side of the screen:

![[Empire - Hop Listeners-20250103135345279.webp]]

Create a new listener and choose "http_hop" for the type. We then fill in the options much like with the Empire CLI Client:

![[Empire - Hop Listeners-20250103135419445.webp]]

Again, we set the **Host** (`.200`), **Port**, and **RedirectListener**.  

> [!Note]
If you also have a Hop Listener set up using the Empire CLI then you should also change the OutFolder to avoid overwriting the previously generated files.

Click "Submit", and the listener starts!

**Next stet: ** [[Git Server]]

