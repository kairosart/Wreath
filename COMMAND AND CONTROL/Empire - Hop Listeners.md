As mentioned previously, Empire agents can't be proxied with a socat relay or any equivalent redirects; but there must be a way to get an agent back from a target with no outbound access, right?

The answer is yes. We use something called a Hop Listener.

Hop Listeners create what looks like a regular listener in our list of listeners (like the http listener we used before); however, rather than opening a port to receive a connection, hop listeners create files to be copied across to the compromised "jump" server and served from there. These files contain instructions to connect back to a normal (usually HTTP) listener on our attacking machine. As such, the hop listener in the listeners menu can be thought of as more of a placeholder -- a reference to be used when generating stagers.

If this doesn't make much sense just now, don't worry! Hopefully it will once we have worked through an example.

The hop listener we will be working with is the most common kind: the `http_hop` listener.

When created, this will create a set of `.php` files which must be uploaded to the jumpserver (our compromised webserver) and served by a HTTP server. Under normal circumstances this would be a trivial task as the compromised server already has a webserver running; however, out of courtesy to anyone else attempting the network, we will not be using the installed webserver.

