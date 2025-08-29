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

SSH (Secure Shell) is a program for logging into a remote machine and executing commands on that machine. It provides secure encrypted communications between two untrusted hosts over an insecure network. X11 connections, arbitrary TCP ports and UNIX-domain sockets can also be forwarded over the secure channel.

SSH connects and logs into the specified destination, which may be specified as either `[user@]hostname` or a URI of the form `ssh://[user@]hostname[:port]`. The user must prove their identity to the remote machine using one of several methods including password authentication, public key authentication, or other methods.

## Solution

To connect to the Bandit Level 0 server, use the following SSH command:

```sh
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

Breaking down the command:
- `ssh` - The SSH client program
- `bandit0` - The username to connect with
- `bandit.labs.overthewire.org` - The hostname of the server
- `-p 2220` - Specifies the port number (2220 instead of the default port 22)

After running this command, you will be prompted for the password. Enter `bandit0` as the password. Once authenticated successfully, you will have access to the remote machine and can proceed to Level 1.

This concludes Level 0 of the Bandit challenges.
