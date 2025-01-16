As mentioned previously, modules are used to perform various tasks on a compromised target, through an active Empire agent. For example, we could use Mimikatz through its Empire module to dump various secrets from the target.

As per usual, let's look at loading modules in both Empire CLI and Starkiller.

---

## Empire CLI

Starting with Empire CLI:

Inside the context of an agent, type `usemodule` . As expected, this will show a dropdown with a huge list of modules which can be loaded into the agent for execution.

It doesn't really matter here as we already have full access to the target, but for the sake of learning, let's try loading in the Sherlock Empire module. This checks for potential privilege escalation vectors on the target.

`usemodule powershell/privesc/sherlock`

![[Empire - Modules-20250115141626143.webp]]

As previously, we can use `options` to get information about the module after loading it in.

This module requires one option to be set: the `Agent` value. This is already set for us here; however, if it was incorrect or there was no option set already then we could set it using the command: `set Agent AGENT_NAME`, (the same syntax as in previous parts of the framework).  

We start the module using the usual `execute` command. The module will then run as a **background job, returning the results when it completes**.

![[Empire - Modules-20250115141730450.webp]]

If we know approximately what we want to do, but don't know the exact path to a module, we can just type `usemodule NAME_OF_MODULE` and it should come up in the dropdown menu:

![[Empire - Modules-20250115141818665.webp]]


---
## Starkiller

Now let's do the same thing in Starkiller.

First we switch over to the modules menu:

![[Empire - Modules-20250115141921142.webp]]

In the top right corner we can search for our desired module. Let's search for the Sherlock module again:

![[Empire - Modules-20250115142015679.webp]]

Select the module by clicking on its name.  

From here we click on the Agents menu, then select the agent(s) to use the module through:

![[Empire - Modules-20250115142058801.webp]]

Click Submit to run the module!

To view the results we need to switch over to the "Reporting" section of the main menu on the left side of the window:

![[Empire - Modules-20250115142145671.webp]]

From here we can see the task we just ran, showing the Agent in use, the event type, command, user, and a timestamp.

![[Empire - Modules-20250115142221486.webp]]

Clicking on the dropdown arrow to the left of the task gives the task results:

![[Empire - Modules-20250115142257647.webp]]

