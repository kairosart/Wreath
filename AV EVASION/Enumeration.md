We have a reverse shell on the third and final target -- this is cause for celebration!

We don't yet have full system access to the target though. As we saw when we first obtained the webshell, the webserver was (un)fortunately not running with system permissions (contrary to the Xampp defaults), which leaves us with a low-privilege account. Looks like Thomas was sensible with his security on his own PC!

This does mean that we're going to need to enumerate the target for privesc vectors though -- and with Defender active, we'll have to do it quietly. Let's consider our options:

- We could (and should) always start with a little manual enumeration. This will be relatively quiet and gives us a baseline to work with  
    
- Defender would _definitely_ catch a regular copy of WinPEAS; however, it would be unlikely to catch either the `.bat` version or the obfuscated `.exe` version, both of which are released in the [PEAS repository](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/) alongside the regular version
- Chances are that AMSI will alert Defender if we try to load any PowerShell privesc check scripts (e.g. PowerUp), so we'd ideally be looking for obfuscated versions of these if we were to use them

We'll start with some manual enumeration and hopefully come up with something workable!


---

# Your job

On the #Eeverse_Shell use the command `whoami /priv`.

![[Screenshot from 2025-02-05 19-39-21.webp]]

> [!Question]
>**[Research]** One of the privileges on this list is very famous for being used in the PrintSpoofer and Potato series of privilege escalation exploits -- which privilege is this?
>`SeImpersonatePrivilege`

Our current user likely has this privilege due to running XAMPP as a service on the account. Unfortunately this also means that XAMPP won't be a good privesc vector in its own right, but we might be able to use the privileges it gave us!

---

Now use `whoami /groups` to check the current user's groups.

![[Screenshot from 2025-02-05 19-43-16.webp]]

Unfortunately this account isn't in the Local Administrators group as that (combined with the High integrity process we're currently using) would make any further privilege escalation redundant.

Now that we've got an idea of our own user's capabilities. Let's take a look at the box itself.

Windows services are commonly vulnerable to various attacks, so we'll start there. Generally speaking, it's unlikely that core Windows services will be vulnerable to anything -- user installed services are far more likely to have holes in them.

Let's start by looking for non-default services:  
`wmic service get name,displayname,pathname,startmode | findstr /v /i "C:\Windows"`

![[Screenshot from 2025-02-05 19-53-39.png]]

This lists all of the services on the system, then filters so that only services that are _not_ in the `C:\Windows` directory are returned. This should cut out most of the core Windows services (which are unlikely to be vulnerable to this kind of vulnerability), leaving us with primarily lesser-known, user-installed services.  

There should be a bunch of results returned here. Read through them, paying particular attention to the `PathName`  column. Notice that one of the paths does not have quotation marks around it.

> [!Question]
>What is the Name (second column from the left) of this service?
>`SystemExplorerHelpService`

The lack of quotation marks around this service path indicates that it might be vulnerable to an _Unquoted Service Path_ attack. In short, if any of the directories in that path contain spaces (which several do) and are writeable (which we are about to check), then -- assuming the service is running as the `NT AUTHORITY\SYSTEM` account, we might be able to elevate privileges.

First of all, let's check to see which account the service runs under:  
`sc qc SERVICE_NAME`

![[Screenshot from 2025-02-05 20-37-15.png]]

> [!Question]
>Is the service running as the local system account (Aye/Nay)?
>`Aye`

This is looking good!

Let's check the permissions on the directory. If we can write to it, we are golden:  
`powershell "get-acl -Path 'C:\Program Files (x86)\System Explorer' | format-list"`

![[f0b36cf3dfba.png]]

We have full control over this directory! How strange, but hey, Thomas' security oversight will allow us to root this target.

In the interests of learning, it should be noted here that this is far from the only vulnerability here. By the looks of things, Thomas installed the program but couldn't be bothered entering the password for the Administrator account every time he needed to interact with it. As a result, he botched the permissions and gave every user access to every aspect of the program.

This means that we can create our unquoted service path exploit, but we could also perform attacks such as *DLL hijacking*, or even outright *replacing the service executable with a malicious binary*.

That said, we will stick to the *unquoted service path vulnerability* purely to avoid messing with the service itself. This way all we need to do is create our own binary then delete it, rather than alter any of the files in the service itself.

---
**Bonus Question (optional):** Try to get a copy of WinPEAS up to the target (either the obfuscated executable file, or the batch variant) and run it. You will see that there are many more potential vulnerabilities on this target -- mainly due to patches that haven't been installed.

**Next step: ** [[Privilege Escalation]]
