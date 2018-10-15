# Sudo in 2018

If you use Linux or other Unix-like operating systems then you often see shell commands starting with the word sudo. Sudo is a command that executes its argument with elevated privileges, usually the privileges of root. The idea is that sudo allows a non-root user to do some root actions without switching to root. Personally, I don't like sudo. The reason is that it pollutes the syntax of shell commands. Imagine that you would need to put sudo at the beginning of every line in your Java code. Would you like that? I wouldn't. However, I don't want to compromise security either. So, I decided to research whether it is worth using sudo nowadays.

Let's start with the sudo goals. Sudo attempts to address four topics:
- delegating privileges in a multi-user environment
- elevating privileges only when necessary
- elevating privileges as little as necessary
- protecting elevated privileges by reasking the password periodically

All of them help minimize consequences of unauthorized people getting access to the shell. The first three also help protect against authorized people unintentionally running malicious code or accidently breaking the machine by running a wrong command.

Let's start with the first topic and expand it. When a machine has a user and administrator (admin), the user has to ask the admin to run commands requiring admin privileges. It is possible that the admin may decide that she trusts the user to run certain commands by himself. In this case, the admin configures sudo to allow that. However, this case is rare nowadays. In the past, multiple users often used a single Unix machine. Nowadays, a single user often uses multiple Unix machines. So, the Unix machine usually has only one user who serves as both user and admin. Even when there are more than one users, their hierarchy is usually flat, i.e. "admin - admin", not "admin - user", which makes privilege delegation unnecessary.

Unlike the first topic, the second one is actual and important. However, it can be addressed by the su command too. Switching to root and back with su allows to elevate privileges only when necessary.

The least privilege principle is as important as the previous topic. When you install an ordinary package, e.g. a library, you don't want the installation code to have access to /dev/mem or /dev/sda. You want it to only be able to create and modify files in certain directories. Unfortunately, sudo doesn't provide that in default configurations of popular Linux distributions:
- Debian may not have sudo installed by default [1]
- Ubuntu has sudo by default, but it is configured to elevate to the full privileges of root

The fourth topic is helpful, but it can also be addressed by the su command. While sudo automatically forgets the password after a timeout, su allows the user to manually close the shell opened by su.

To summarize the analysis of the topics:
- delegating privileges in a multi-user environment (the topic is not actual)
- elevating privileges only when necessary (the solution is not exclusive to sudo)
- elevating privileges as little as necessary (the topic is not solved by sudo)
- protecting elevated privileges by reasking the password periodically (the solution is not exclusive to sudo)

Now let’s look at the drawbacks of sudo. I already mentioned syntax pollution. The severity of this problem depends on what percentage of all commands requires elevated privileges. In the past, users tended to work on the Unix machine a lot and administrate it little. That is why the percentage of commands with sudo was low. Nowadays, Unix users work less and administrate more, preferring other operating systems as a client and choosing Unix to be a server. Hence, more commands become starting with sudo and the syntax pollution problem worsens.

Another drawback, even more important, is that sudo allows hidden privilege elevation. Imagine that you run a script without sudo which makes you think that the script will be executed without privilege elevation. However, the script contains a command starting with sudo. If sudo still remembers the password from when it was entered earlier, the script will be executed without asking the password, and the command inside the script will be executed with elevated privileges without you knowing this. To be fair, I must say that the sudo password memory can be turned off by configuration [2].

Let’s conclude this research. Sudo has its benefits and drawbacks. For me, the latter outweigh the former. That is why I prefer the su command. Yet, it is not perfect either and I will continue to search for a perfect one.

PS There are no "as far as I know" or "if I am not mistaken" in this text. Yet, they are actually implied in every sentence. My relationship with Unix has quite long history, but it was not very diverse. So, I must have missed something small or big.

[1] https://unix.stackexchange.com/questions/106529/why-is-sudo-not-installed-by-default-in-debian

[2] https://superuser.com/questions/83960/make-sudo-ask-for-my-password-again
