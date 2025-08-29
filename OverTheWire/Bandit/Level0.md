# Level 0 - SSH Login 

## Task

The goal of this level is for you to log into the game using SSH.
The host to which you need to connect is bandit.labs.overthewire.org, on port 2220. 
The username is bandit0 and the password is bandit0. Once logged in, go to the Level 1 page to find out how to beat Level 1.

```sh
Server: bandit.labs.overthewire.org
Port: 2220
Username: bandit0
Password: bandit0
```

## Theory 
     ssh (SSH client) is a program for logging into a remote machine and for executing commands on a remote
     machine.  It is intended to provide secure encrypted communications between two untrusted hosts over an
     insecure network.  X11 connections, arbitrary TCP ports and UNIX-domain sockets can also be forwarded over
     the secure channel.

     ssh connects and logs into the specified destination, which may be specified as either [user@]hostname or a
     URI of the form ssh://[user@]hostname[:port].  The user must prove their identity to the remote machine
     using one of several methods (see below).

## Solution


```sh
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

you should then be prompted for the password, after entering the correct password one should be able to log into the 
remote machine. This concludes the level 0.
