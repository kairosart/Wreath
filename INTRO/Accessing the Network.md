Before we get into the content, we need to know how to access the network.

Joining the network requires a 7 day streak or a subscription to TryHackMe. To limit the number of networks which have to stay active at any one point, network access will last for 10 days after joining, at which point you will be automatically be removed; however, rejoining does not require a streak so if you didn't manage to finish within the ten days, you are free to rejoin immediately and keep at it from where you left off. Progress will not be reset.  

Whether you are using the AttackBox or a local machine to connect to the TryHackMe network, you will need to use OpenVPN with a connection pack specifically designed for this network.

If you are using a local machine then you will need to download a configuration pack from the [Access](https://tryhackme.com/access) page.

If you are a subscriber and are using the AttackBox then you will be able to find this connection pack in a directory on your desktop. This will be automatically connected when the AttackBox starts so **don't run the connection pack manually on the AttackBox if you are a subscriber.**  

If you are not subscribed then you will need to download the connection pack as normal, copy and paste the contents into a file on the AttackBox, then connect as you would on a local VM.

Be aware that this is still a VPN (albeit with an automated startup sequence) on the AttackBox so you will need to use `ip a` to see your available IP addresses. Pick the one that starts with 10.50.x.x and use that for all reverse connections in the network.

> [!note]
> You are encouraged to use your own VM when attacking the Wreath Network. The content in this room will be difficult to cover in the time available with a single AttackBox and the persistence of a local VM will be hugely advantageous. Equally, certain sections (such as the Empire section) will be very difficult to perform in the AttackBox. If you don't have a local Kali VM,_  _pre-built versions can be found for [VMware](https://images.kali.org/virtual-images/kali-linux-2020.4-vmware-amd64.7z) or [VirtualBox](https://images.kali.org/virtual-images/kali-linux-2020.4-vbox-amd64.ova); however, installing manually tends to be more reliable if you are comfortable doing so._

On the access page, click on the "Network" tab, then select "Wreath" from the dropdown menu:

![[Accessing the Network-20241125140721892.webp]]

> [!note]
> This will only appear if you have joined the room. If you are only viewing the room just now, click the "Join" button at the top right of this page!

Click on the green download button on the access page and save the configuration pack somewhere on your local machine. If this does not work then you may have to click on the "Regenerate" button first, then give it ten seconds before attempting to download the pack.


Connecting to OpenVPN on Linux (using either Kali or the AttackBox) can be accomplished using the `openvpn` client.

To do this, from the same directory we saved the config in we use the command:  
`sudo openvpn CONFIG_NAME.ovpn`  

Obviously replacing the name of the config with the config that you downloaded. Wreath config packs follow a naming scheme of `USERNAME-wreath.ovpn`, so an example command might be:

![[Accessing the Network-20241125140952640.webp]]

This should give you access to the Wreath network!

Without closing the connection, open a new terminal (`Ctrl + T` in most cases). This is the easiest way (technically speaking) to run the OpenVPN client in the background whilst still being able to use the CLI. If you are comfortable using a terminal multiplexer (e.g. Tmux) to create a connection in the background then doing so would be a more elegant solution.

## Controlling the Network  

The network has three states: Running, Stopped, and Resetting.

The current state can be shown at the top right of the network box at the top of the page:

![[Accessing the Network-20241125141209104.webp]]

- Running means that the network is fully operational and can be connected to at will
- Stopped indicates that the network has gone to sleep. This happens when no one has pressed the "Extend" button within a set time limit so as to prevent the network from being constantly running with no one using it. It can be restarted by pressing the "Start" button. This does _not_ reset the network back to a clean copy, so anything stored on the targets should still be there
- Resetting indicates that the network is currently in the process of being wiped clean and resetting back to its default state. This can be used when something (or someone) has happened to one of the targets rendering it broken

The three buttons below the network map can be used to control this functionality:

![[Accessing the Network-20241125141256865.webp]]

- The "Start" button restarts the network once stopped
- The "Extend" button prevents the network from going to sleep. This button also contains a timer showing how long until the network shuts down.  
- The "Reset" button initiates a full wipe of the network. This requires a percentage of users in the network to click the button, thus preventing a single person from spamming resets

Finally, the "Network Uptime" field at the bottom right of the network map indicates how long the network has been awake for. This is not necessarily the time since the last reset.

**Next step:** [[Backstory]]
