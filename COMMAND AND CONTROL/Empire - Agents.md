Now that we've started a listener and created a stager, it's time to put them together to get an agent!

We've been building up towards getting an agent on the compromised webserver, so let's do that now.

---
## Empire client

The process for this is identical whether we are using Starkiller or Empire Client. We need to get the file to the target and executed.

There are a variety of ways we could do this. The simplest would simply be to use your preferred CLI text editor to create a file on the target, copy and paste the script in, then execute it. If using this method, please do it in the /tmp directory and follow the `FILENAME-USERNAME.sh` naming convention. We could also use something called a _[here-document](https://tldp.org/LDP/abs/html/here-docs.html)_ to execute the entire script without ever writing it to the disk.

That said, this is overkill. If we read through the script we can see that it is in three main parts:

![[Empire - Agents-20250102141019302.webp]]

- In the green square we have the _shebang_. This tells the shell which interpreter to run the script under. In this case the script would be run using `/bin/bash`.
- The red square contains the payload itself. This is the section we're interested in.
- The blue square contains post processing commands. Specifically these two lines tell the script to delete itself then exit.

Knowing this, we can just copy everything in the red square then execute it in a terminal on the target:

![[Empire - Agents-20250102141146930.webp]]

This results in an agent being received by our waiting listener.

In the Empire CLI receiving a listener looks something like this:

![[Empire - Agents-20250102141300828.webp]]

We can then type `agents` and hit enter to see a full list of available agents:

![[Empire - Agents-20250102141340137.webp]]

To interact with an agent, we use `interact AGENT_NAME` -- as per usual a dropdown with autocompletes will assist us here. This puts us into the context of the agent. We can view the full list of available commands with `help`:

![[Empire - Agents-20250102141428143.webp]]

Note that this menu will change depending on the stager we used.

When we have finished with our agent we use `back` to switch context back to the agents menu. This doesn't destroy the agent, however. If we did want to kill our agent, we would do it with `kill AGENT_NAME`:

![[Empire - Agents-20250102141538161.webp]]

We can also rename agents using the command: `rename AGENT_NAME NEW_AGENT_NAME`.


---

## Starkiller

To interact with agents In Starkiller we go to the Agents tab on the left hand side of the screen:

![[Empire - Agents-20250102141906423.webp]]

Here we will see that our agent has checked in!

![[Empire - Agents-20250102141956913.webp]]

To interact with an agent in Starkiller we can either click on its name, or click on the "pop out" button in the actions menu.  

This results in a menu which gives us access to a variety of amazing features, including the ability to execute modules (more on these soon), execute commands in an interactive shell, browse the file system, and much more. Be sure to play around with this before moving on!

![[Empire - Agents-20250102142046438.webp]]

To delete agents in Starkiller we can use either the trashcan icon in the pop-out agent Window, or the kill button in the action menu for the agent back in the Agents tab of Starkiller.

> [!Question]
>Using the `help` command for guidance: in Empire CLI, how would we run the `whoami` command inside an agent?
>`shell whoami`

We have now covered the basics of Empire, with the exception of modules, which we will look at after getting an agent back from the Git Server.

Kill your agents on the webserver then let's look at proxying Empire agents!

**Next steps: ** [[Empire - Hop Listeners]]

