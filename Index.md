![[Index-20241124134005002.webp]]

# Overview
Wreath is designed as a learning resource for beginners with a primary focus on:

- Pivoting
- Working with the Empire C2 (**C**ommand and **C**ontrol) framework
- Simple Anti-Virus evasion techniques

The following topics will also be covered, albeit more briefly:

- Code Analysis (Python and PHP)
- Locating and modifying public exploits  
- Simple webapp enumeration and exploitation  
- Git Repository Analysis
- Simple Windows Post-Exploitation techniques
- CLI Firewall Administration (CentOS and Windows)
- Cross-Compilation techniques
- Coding wrapper programs
- Simple exfiltration techniques  
- Formatting a pentest report  

These will be taught in the course of exploiting the Wreath network.

This is designed as almost a sandbox environment to follow along with the teaching content; the focus will be on the above teaching points, rather than on initial access and privilege escalation exploits (contrary to other boxes on the platform where the focus is on the challenge).

---
_**Tools:**_  
A zipfile containing the tools demonstrated throughout this room is attached to this task. That said, whilst these will work, it would be advisable to download the latest versions of the tools (as instructed by the tasks) during your progression through the content, rather than relying on the provided archive. The password for this zipfile is: `WreathNetwork`.


---
_**Videos:**_  
[@DarkStar7471](https://twitter.com/DarkStar7471) has kindly created a series of videos to accompany the teaching content in the Wreath network. Please use these as your first line of support! Writeups in the form of pentest reports will also be made available.

The videos can be accessed directly from Dark's [YouTube channel](https://www.youtube.com/playlist?list=PLsqUCyw0Jf9sMYXly0uuwfKMu34roGNwk); however, each task in this room also contains a link to the relevant video.  

Look for the "Play" button at the very bottom right of the screen:

![[Index-20241124134408752.webp]]

This will update on a task-by-task basis so that it always points to the correct video.

---
_**Prerequisites:**_  
This network is designed for beginners, but assumes basic competence in the [Linux command line](https://tryhackme.com/room/linuxfundamentalspart1) and fundamental hacking methodology. The ability to read and write a little code will also be useful. Any other required knowledge will be linked throughout the tasks. If you need help, please feel free to ask in the [TryHackMe Discord](https://discord.gg/tryhackme) -- there is a channel set up for this purpose in the help section there.

---
_**Conduct:**_  
As this network is shared amongst a number of people, it goes without saying: please don't mess things up for others in the network. There are no password changes required in any of these tasks, and no files need deleted. At various stages in this network it will be necessary to upload files and tools to the remote box. Please upload these in the format: `toolname-username` (e.g. `socat-MuirlandOracle`, `shell-MuirlandOracle.aspx`, etc) to avoid overwriting work belonging to anyone else. In short, don't be a troll, be respectful, and have fun!  

With that being said:- let's get started!


---
# MAIN STEPS

## WEBSERVER

1. [[WEBSERVER/Enumeration]]
2. [[WEBSERVER/Exploitation]]

## PIVOTING

1. [[What is Pivoting?]]
2. [[High-level Overview]]
3. [[Proxychains & Foxyproxy]]
4. [[SSH Tunnelling  - Port Forwarding]]
5. [[plink.exe]]
6. [[Socat]]
7. [[Chisel]]
8. [[sshuttle]]
9. [[PIVOTING/Conclusion]]

## GIT-SERVER

1. [[GIT SERVER/Enumeration]]
2. [[GIT SERVER/Pivoting]]
3. [[Visit the webpage of git-serv]]
4. [[Searching for public exploit]]
5. [[Code review]]
6. [[GIT SERVER/Exploitation]]
7. [[Stabilisation & Post Exploitation]]

## COMMAND AND CONTROL

1. [[Git Server]]
2. [[Empire - Modules]]
3. [[Empire - Interactive Shell]]
4. [[COMMAND AND CONTROL/Conclusion]]

## PERSONAL PC

1. [[PERSONAL PC/Enumeration]]
2. [[PERSONAL PC/Pivoting]]
3. [[The Wonders of Git]]
4. [[Website Code Analysis]]
5. [[Exploit PoC]]

## AV EVASION

1. [[AV EVASION/Introduction]]
2. [[AV Detection Methods]]
3. [[PHP Payload Obfuscation]]
4. [[Compiling Netcat & Reverse Shell!]]
5. [[AV EVASION/Enumeration]]
6. [[Privilege Escalation]]

## EXFILTRATION

1. [[Exfiltration Techniques & Post Exploitation]]

## CONCLUSION

1. [[Debrief & Report]]
2. [[Final Thoughts]]