# Level 3 - Hidden Files

## Task

The password for the next level is stored in a hidden file in the `inhere` directory. Use this password to log into bandit3 using SSH.

```sh
Server: bandit.labs.overthewire.org
Port: 2220
Username: bandit3
Password: [found in hidden file in inhere directory]
```

## Theory

This level introduces the concept of hidden files in Unix-like systems. Hidden files are a fundamental aspect of file system organization and security:

**Hidden Files**: In Unix-like systems, any file or directory whose name begins with a dot (`.`) is considered "hidden." These files don't appear in standard directory listings by default. This convention is used for:
- Configuration files (e.g., `.bashrc`, `.gitconfig`)
- System files that users don't typically need to see
- Temporary files and caches
- Files that should be protected from accidental modification

**The `ls` command and its options**:
- `ls` - Lists visible files and directories
- `ls -a` - Lists **all** files including hidden ones (the `-a` stands for "all")
- `ls -la` - Lists all files with detailed information (long format)
- `ls -A` - Lists all files except `.` (current directory) and `..` (parent directory)

**Directory Navigation**:
- `cd directory_name` - Change to a specific directory
- `pwd` - Print working directory (shows your current location)
- `cd ..` - Move up one directory level
- `cd ~` or `cd` - Return to home directory

## Solution

1. **Connect to the bandit2 server** using the password from Level 2:
   ```sh
   ssh bandit2@bandit.labs.overthewire.org -p 2220
   ```
   Use the password you found in the "spaces in this filename" file from Level 2.

2. **List the contents of the home directory**:
   ```sh
   ls
   ```
   You should see a directory called:
   ```
   inhere
   ```

3. **Navigate to the inhere directory**:
   ```sh
   cd inhere
   ```

4. **List all files including hidden ones**:
   ```sh
   ls -a
   ```
   This will show all files, including hidden ones. You should see something like:
   ```
   .  ..  .hidden
   ```
   Where:
   - `.` represents the current directory
   - `..` represents the parent directory
   - `.hidden` is the hidden file containing the password

5. **Read the contents of the hidden file**:
   ```sh
   cat .hidden
   ```
   This will display the password for the next level:
   ```
   <password>
   ```

6. **Connect to bandit3 using the found password**:
   ```sh
   ssh bandit3@bandit.labs.overthewire.org -p 2220
   ```
   When prompted for the password, enter the password you found.

## Key Concepts Learned

- **Hidden files**: Understanding the dot-file convention in Unix systems
- **Directory navigation**: Using `cd` to move between directories
- **Enhanced listing**: Using `ls -a` to reveal hidden files
- **File system structure**: Understanding current (`.`) and parent (`..`) directory references
- **Security through obscurity**: How hidden files can be used to store sensitive information

## Additional Tips

- **Useful `ls` variations**:
  - `ls -la` - Detailed view of all files including permissions, ownership, and timestamps
  - `ls -lah` - Same as above but with human-readable file sizes
  - `ls -A` - Shows hidden files but excludes `.` and `..`

- **Other ways to find hidden files**:
  - `find . -name ".*"` - Find all hidden files in current directory and subdirectories
  - `echo .*` - Shell globbing to show hidden files (though less reliable)

- **Configuration files**: Many programs store their configuration in hidden files in your home directory
- **Git repositories**: The `.git` directory is hidden and contains all version control information

## Common Pitfalls

- **Forgetting `-a` flag**: Using just `ls` won't show hidden files
- **Case sensitivity**: Remember that `.Hidden` and `.hidden` are different files
- **Directory confusion**: Make sure you're in the correct directory before looking for files
- **Assuming all dot-files are hidden**: While the convention is strong, always verify with `ls -a`

## Security Implications

- **Information disclosure**: Hidden files might contain sensitive information
- **Persistence mechanisms**: Malware often uses hidden files to maintain persistence
- **Configuration exposure**: Misconfigured hidden files can expose credentials or sensitive settings

This level demonstrates how attackers might hide information in plain sight using standard Unix conventions, and emphasizes the importance of thorough file system exploration during security assessments.
