> [!Note]
> If you are using the AttackBox then you are advised to skip to Task 32. The way that Empire is installed in the AttackBox is not representative of the recommended method -- a necessary design choice which was made to accommodate other software running on the machine. If you are comfortable working with Docker (and changing the instructions in the following tasks to accommodate accordingly) then feel free to read on. Otherwise please skip to the next section.


---

So, we have a stable shell. What now?

With a foothold in a target network, we can start looking to bring what is known as a _C2 (Command and Control) Framework_ into play. C2 Frameworks are used to consolidate an attacker's position within a network and simplify post-exploitation steps (privesc, AV evasion, pivoting, looting, covert network tactics, etc), as well as providing red teams with extensive collaboration features. There are many C2 Frameworks available. The most famous (and expensive) is likely [Cobalt Strike](https://www.cobaltstrike.com/); however, there are many others, including the .NET based [Covenant](https://github.com/cobbr/Covenant), [Merlin](https://github.com/Ne0nd0g/merlin), [Shadow](https://github.com/bats3c/shad0w), [PoshC2](https://github.com/nettitude/PoshC2), and many others. An excellent resource for finding (and filtering) C2 frameworks is [The C2 Matrix](https://www.thec2matrix.com/), which provides a great list of the pros and cons of a huge number of frameworks.  

We have a system shell on a Windows host, making this an ideal time to introduce the second of our three teaching topics: the C2 Framework "Empire".

Powershell Empire is, as the name suggests, a framework built primarily to attack Windows targets (although especially with the advent of dotnet core, more and more of the functionality may become usable in other systems). It provides a wide range of modules to take initial access to a network of devices, and turn it into something _much_ bigger. In this section we will be looking at the principles of PS Empire, as well as how to use it (and its GUI interface: Starkiller) to improve our shell and perform post-exploitation techniques on the Git Server.

The Empire project was originally abandoned in early 2019; however, it was soon picked up by a company called [BC-Security](https://www.bc-security.org/), who have maintained and improved it ever since. As such, there are actually two public versions of Empire -- the original (now very outdated), and the current BC-Security fork. Be careful to get the right one!

> [!Note]
Tthis material was originally written for Empire 3.x, but has been updated in response to the release of Empire 4.x which has a very different way of operating. Make sure to use Empire 4.x if following along with these materials.

We will be looking into both Empire and its GUI extension: "Starkiller". Empire is the original CLI based framework but has now been split into a _server_ mode and a _client_ mode. Starkiller is a more recent addition to the toolbox, and can be used instead of (or as well as) the Empire client CLI program.

**Next step: **[[Empire - Installation]]
