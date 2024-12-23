As with any attack, we first begin with the enumeration phase. Completing the [Nmap](https://tryhackme.com/room/furthernmap) room (if you haven't already) will help with this section.

Thomas gave us an IP to work with (shown on the Network Panel at the top of the page). Let's start by performing a port scan on the first `15000` ports of this IP.

> [!note]
> Here (and in general), it's a good idea to save your scan results to a file so you don't have to re-run the same scan twice._

## Nmap

### First 15000 ports

```
$ nmap -p-15000 -vv 10.200.84.200 -oG initial-scan -oN nmap_output.txt -Pn
```

![[Enumeration-20241125145456314.webp]]

### A more in-depth Nmap scan

```
$ sudo nmap -sS -sC -sV -O -A 10.200.84.200 
```

![[Enumeration-20241223144512758.webp]]


## MiniServ 1.890 (Webmin httpd) vulnerability 

**CVE-2019-15107**:  
A backdoor vulnerability in Webmin versions (including Miniserv) allows attackers to bypass authentication and execute commands as root. This issue stems from malicious backdoor code introduced into Webmin's build process.

## /etc/hosts

You will have noticed that the site failed to resolve. Looks like Thomas forgot to set up the DNS!

Add it to your hosts file manually. This can be accomplished by editing the `/etc/hosts` file on Linux/MacOS, or `C:\Windows\System32\drivers\etc\hosts` on Windows, to include the IP address, followed by a tab, then the domain name. 

> [!note]
> This _must_ be done as root/Administrator.

It should look something like this when done, although the _IP address and domain name will be different_:

`10.10.10.10 example.thm`

## Webpage

Reload the webpage -- it should now resolve, but it will give you a different error related to the TLS certificate. This occurs because the box is not really connected to the internet and so cannot have a signed TLS certificate. In this instance it is safe to click "Advanced" -> "Accept Risk"; however, you should never do this in the real world.  

In real life we would perform a **"footprinting"** phase of the engagement at this point. This essentially involves finding as much public information about the target as possible and noting it down. You never know what could prove useful!

---

> [!question]
>1. How many of the first 15000 ports are open on the target?
>`4`
>2. What OS does Nmap think is running?
>`Centos`
>3. Open the IP in your browser -- what site does the server try to redirect you to?
>https://thomaswreath.thm
>4. Read through the text on the page. What is Thomas' mobile phone number?
>`447821548812`
>5. Let's have a look at the highest open port.  Look back at your service scan results: what server version does Nmap detect as running here? 
> `MiniServ 1.890 (Webmin httpd)`
> 6. Put your answer to the last question into Google. It appears that this service is vulnerable to an unauthenticated remote code execution exploit! What is the CVE number for this exploit?
> `CVE-2019-15107`


**Next step:** [[WEBSERVER/Exploitation]]



