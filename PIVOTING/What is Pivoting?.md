Pivoting is the art of using access obtained over one machine to exploit another machine deeper in the network. *It is one of the most essential aspects of network penetration testing*, and is one of the three main teaching points for this room.

Put simply, by using one of the techniques described in the following tasks (or others!), it becomes possible for an attacker to gain initial access to a remote network, and use it to access other machines in the network that would not otherwise be accessible:

![[What is Pivoting?-20241129151102233.webp]]

In this diagram, there are four machines on the target network: one public facing server, with three machines which are not exposed to the internet. By accessing the public server, we can then pivot to attack the remaining three targets.

_**Note:** This is an example diagram and is not representative of the Wreath Network._

This section will contain a lot of theory for pivoting from both Linux and Windows compromised targets, which we will then put into practice against the next machine in the network. Remember though: you have a sandbox environment available to you with the compromised machine in the Wreath network. After the enumeration tasks coming up, you'll also know about the next machine in the network. Feel free to use these boxes to play around with the tools as you go through the tasks, but be aware that some techniques may be stopped by the firewalls involved (which we will look at mitigating later in the network).


---

## Summary of the targets and the steps to pivot through the network

![[Diagram.svg]]








**Next step: ** [[High-level Overview]]

