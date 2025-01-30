Antivirus Evasion is the third and final primary teaching point of the Wreath network.

By nature, AV Evasion is a rapidly changing topic. It's a constant dance between hackers and developers. Every time the developers release a new feature, the hackers develop a way around it. Every time the hackers bypass a new feature, the developers release another feature to close off the exploit, and so the cycle continues. Due to the speed of this process, it is nigh impossible to teach bleeding-edge techniques (and expect them to stay relevant for any length of time), so we are only going to be covering the fundamentals of the topic here. Without further ado, let's dive in!  

---

When it comes to AV evasion we have two primary types available:

- On-Disk evasion
- In-Memory evasion

On-Disk evasion is when we try to get a file (be it a tool, script, or otherwise) saved on the target, then executed. This is very common when working with executable (`.exe`) files.

In-Memory evasion is when we try to import a script directly into memory and execute it there. For example, this could mean downloading a PowerShell module from the internet or our own device and directly importing it without ever saving it to the disk.  

In ages past, In-Memory evasion was enough to bypass most AV solutions as the majority of antivirus software was unable to scan scripts stored in the memory of a running process. This is no longer the case though, as Microsoft implemented a feature called the **A**nti-**M**alware **S**can **I**nterface (AMSI). AMSI is essentially a feature of Windows that scans scripts as they enter memory. It doesn't actually check the scripts itself, but it does provide hooks for AV publishers to use -- essentially allowing existing antivirus software to obtain a copy of the script being executed, scan it, and decide whether or not it's safe to execute. Whilst there are various bypasses for this (often involving tricking AMSI into failing to load), these are out of scope for this room.

In terms of methodology: ideally speaking, we would start by attempting to fingerprint the AV on the target to get an idea of what solution we're up against. As this is often an interactive (social-engineering reliant) process, we will skip it for now and assume that the target is running the default Windows Defender so that we can get straight into the meat of the topic. If we already have a shell on the target, we may also be able to use programs such as [SharpEDRChecker](https://github.com/PwnDexter/SharpEDRChecker) and [Seatbelt](https://github.com/GhostPack/Seatbelt) to identify the antivirus solution installed. Once we know the OS version and AV of the target, we would then attempt to replicate this environment in a virtual machine which we can use to test payloads against. 

> [!Note]
>Note that we should _always_ disable any kind of cloud-based protection in the AV settings (potentially by outright disconnecting the VM from the internet) so that the AV doesn't upload our carefully crafted payloads to a server somewhere for analysis, destroying all our hard work. Once we have a working payload, we can then deploy it against the target!

AV Evasion usually involves some form of obfuscation when it comes to payloads. This could mean anything from moving things around in the exploit and changing variable names, to encoding aspects of the script, to outright encrypting the payload and writing a wrapper to decrypt and execute the code section-by-section. The aim is to switch things enough that the AV software is unable to detect anything bad.

> [!Question]
>1. Which category of evasion covers uploading a file to the storage on the target before executing it?
>`On-Disk Evasion`
>2. What does AMSI stand for?
>`Anti-Malware Scan Interface`
>3. Which category of evasion does AMSI affect?
>`In-Memory Evasion`


**Next step:** [[AV Detection Methods]]
