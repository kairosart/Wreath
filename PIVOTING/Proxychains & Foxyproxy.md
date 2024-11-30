In this task we'll be looking at two "proxy" tools: Proxychains and FoxyProxy. These both allow us to connect through one of the proxies we'll learn about in the upcoming tasks. When creating a proxy we open up a port on our own attacking machine which is linked to the compromised server, giving us access to the target network.

Think of this as being something like a tunnel created between a port on our attacking box that comes out inside the target network -- like a secret tunnel from a fantasy story, hidden beneath the floorboards of the local bar and exiting in the palace treasure chamber.  

Proxychains and FoxyProxy can be used to direct our traffic through this port and into our target network.

## **Proxychains**  

Proxychains is a tool we have already briefly mentioned in previous tasks. It's a very useful tool -- although not without its drawbacks. Proxychains can often slow down a connection: performing an nmap scan through it is especially hellish. Ideally you should try to use static tools where possible, and route traffic through proxychains only when required.

That said, let's take a look at the tool itself.

Proxychains is a command line tool which is activated by prepending the command `proxychains` to other commands. For example, to proxy netcat  through a proxy, you could use the command:  
`proxychains nc 172.16.0.10 23`  

Notice that a proxy port was not specified in the above command. This is because proxychains reads its options from a config file. The master config file is located at `/etc/proxychains.conf`. This is where proxychains will look by default; however, it's actually the last location where proxychains will look. The locations (in order) are:

1. The current directory (i.e. `./proxychains.conf`)
2. `~/.proxychains/proxychains.conf`
3. `/etc/proxychains.conf`

This makes it extremely easy to configure proxychains for a specific assignment, without altering the master file. Simply execute: `cp /etc/proxychains.conf .`, then make any changes to the config file in a copy stored in your current directory. If you're likely to move directories a lot then you could instead place it in a `.proxychains` directory under your home directory, achieving the same results. If you happen to lose or destroy the original master copy of the proxychains config, a replacement can be downloaded from [here](https://raw.githubusercontent.com/haad/proxychains/master/src/proxychains.conf).  

Speaking of the `proxychains.conf` file, there is only one section of particular use to us at this moment of time: right at the bottom of the file are the servers used by the proxy. You can set more than one server here to chain proxies together, however, for the time being we will stick to one proxy:

![[Proxychains & Foxyproxy-20241130142414937.webp]]

Specifically, we are interested in the "ProxyList" section:  
`[ProxyList]   # add proxy here ...   # meanwhile   # defaults set to "tor"   socks4  127.0.0.1 9050`  

It is here that we can choose which port(s) to forward the connection through. By default there is one proxy set to localhost port 9050 -- this is the default port for a Tor entrypoint, should you choose to run one on your attacking machine. That said, it is not hugely useful to us. This should be changed to whichever (arbitrary) port is being used for the proxies we'll be setting up in the following tasks.  

There is one other line in the Proxychains configuration that is worth paying attention to, specifically related to the Proxy DNS settings:  

![[Proxychains & Foxyproxy-20241130142633475.webp]]

If performing an Nmap scan through proxychains, this option can cause the scan to hang and ultimately crash. Comment out the `proxy_dns` line using a hashtag (`#`) at the start of the line before performing a scan through the proxy!  

![[Proxychains & Foxyproxy-20241130142710420.webp]]

Other things to note when scanning through proxychains:

- You can only use TCP scans -- so no UDP or SYN scans. ICMP Echo packets (Ping requests) will also not work through the proxy, so use the  `-Pn`  switch to prevent Nmap from trying it.
- It will be _extremely_ slow. Try to only use Nmap through a proxy when using the NSE (i.e. use a static binary to see where the open ports/hosts are before proxying a local copy of nmap to use the scripts library).

## FoxyProxy

Proxychains is an acceptable option when working with CLI tools, but if working in a web browser to access a webapp through a proxy, there is a better option available, namely: FoxyProxy!

People frequently use this tool to manage their BurpSuite/ZAP proxy quickly and easily, but it can also be used alongside the tools we'll be looking at in subsequent tasks in order to access web apps on an internal network. FoxyProxy is a browser extension which is available for [Firefox](https://addons.mozilla.org/en-GB/firefox/addon/foxyproxy-basic/) and [Chrome](https://chrome.google.com/webstore/detail/foxyproxy-basic/dookpfaalaaappcdneeahomimbllocnb). There are two versions of FoxyProxy available: Basic and Standard. Basic works perfectly for our purposes, but feel free to experiment with standard if you wish.

After installing the extension in your browser of choice, click on it in your toolbar:

![[Proxychains & Foxyproxy-20241130142956191.webp]]

Click on the "Options" button. This will take you to a page where you can configure your saved proxies. Click "Add" on the left hand side of the screen:

![[Proxychains & Foxyproxy-20241130143034338.webp]]

Fill in the IP and Port on the right hand side of the page that appears, then give it a name. Set the proxy type to the kind of proxy you will be using. SOCKS4 is usually a good bet, although Chisel (which we will cover in a later task) requires SOCKS5. An example config is given here:

![[Proxychains & Foxyproxy-20241130143201635.webp]]

Press Save, then click on the icon in the task bar again to bring up the proxy menu. You can switch between any of your saved proxies by clicking on them:

![[Proxychains & Foxyproxy-20241130143241712.webp]]

Once activated, all of your browser traffic will be redirected through the chosen port (so make sure the proxy is active!). Be aware that if the target network doesn't have internet access (like all TryHackMe boxes) then you will not be able to access the outside internet when the proxy is activated. Even in a real engagement, routing your general internet searches through a client's network is unwise anyway, so turning the proxy off (or using the routing features in FoxyProxy standard) for everything other than interaction with the target network is advised.

With the proxy activated, you can simply navigate to the target domain or IP in your browser and the proxy will take care of the rest!


---
> [!question]
>What line would you put in your proxychains config file to redirect through a socks4 proxy on 127.0.0.1:4242?
>`socks4 127.0.0.1 4242`
>What command would you use to telnet through a proxy to 172.16.0.100:23?
>`proxychains telnet 172.16.0.100 23`
>You have discovered a webapp running on a target inside an isolated network. Which tool is more apt for proxying to a webapp: Proxychains (PC) or FoxyProxy (FP)?
>`FP`


**Next step:** [[SSH Tunnelling  - Port Forwarding]]

