# Level 1 - Reading Files

## Task

The password for the next level is stored in a file called `readme` located in the home directory. Use this password to log into bandit1 using SSH. Whenever you find a password for a level, use SSH (on port 2220) to log into that level and continue the game.

```sh
Server: bandit.labs.overthewire.org
Port: 2220
Username: bandit1
Password: [found in readme file]
```

## Theory

This level introduces fundamental file system navigation and file reading commands in Unix-like systems:

**`ls` (list directory contents)**: Displays the files and directories in the current directory. This is one of the most commonly used commands for exploring the file system.

**`cat` (concatenate and display)**: Outputs the contents of one or more files to the terminal. The name comes from "concatenate" because it can combine multiple files, but it's most commonly used to display single file contents.

**File paths**: In Unix systems, when you log in via SSH, you typically start in your home directory (represented as `~` or `/home/username`). Understanding your current location in the file system is crucial for navigation.

## Solution

1. **Connect to the bandit0 server** (if not already connected):
   ```sh
   ssh bandit0@bandit.labs.overthewire.org -p 2220
   ```
   Use password: `bandit0`

2. **List the contents of the home directory**:
   ```sh
   ls
   ```
   This will show you all files in the current directory. You should see:
   ```
   readme
   ```

3. **Read the contents of the readme file**:
   ```sh
   cat readme
   ```
   This will display the password for the next level:
   ```
   <password>
   ```

4. **Connect to bandit1 using the found password**:
   ```sh
   ssh bandit1@bandit.labs.overthewire.org -p 2220
   ```
   When prompted for the password, enter: `<password>`

## Key Concepts Learned

- **File system navigation**: Understanding your current location and listing directory contents
- **File reading**: Using `cat` to display file contents
- **Password progression**: How each level's password is used to access the next level
- **SSH workflow**: The pattern of finding credentials and using them to progress

## Additional Tips

- Use `pwd` (print working directory) if you need to confirm your current location
- The `ls -la` command shows more detailed information including hidden files

This level establishes the fundamental workflow for the Bandit challenges: explore, find credentials, and progress to the next level.
