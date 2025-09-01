# Level 5 - Advanced File Search

## Task

The password for the next level is stored in a file somewhere under the `inhere` directory and has all of the following properties:
- Human-readable
- 1033 bytes in size  
- Not executable

Use this password to log into bandit6 using SSH.

```sh
Server: bandit.labs.overthewire.org
Port: 2220
Username: bandit5
Password: [found in file with specific properties in inhere directory]
```

## Theory

This level introduces advanced file searching techniques using the `find` command. Understanding file properties and search criteria is essential for system administration and security analysis:

**The `find` Command**:
The `find` command is one of the most powerful tools for locating files based on various criteria:
- `find path expression` - Basic syntax
- Searches recursively through directory trees
- Can combine multiple search criteria
- Performs actions on found files

**File Properties and Search Criteria**:
- **File type**: `-type f` (regular file), `-type d` (directory), `-type l` (symbolic link)
- **File size**: `-size n[cwbkMG]` where:
  - `c` = bytes
  - `w` = words (2 bytes)
  - `b` = blocks (512 bytes)
  - `k` = kilobytes
  - `M` = megabytes
  - `G` = gigabytes
- **Permissions**: `-perm mode`, `-executable`, `! -executable`
- **Ownership**: `-user username`, `-group groupname`
- **Time**: `-mtime`, `-atime`, `-ctime`

**Logical Operators**:
- `!` or `-not` - Negation (NOT)
- `-a` or `-and` - Logical AND (default)
- `-o` or `-or` - Logical OR
- `( )` - Grouping expressions

**Human-readable vs Binary Files**:
- **Human-readable**: Contains printable characters (text files)
- **Binary**: Contains non-printable characters (executables, images, etc.)
- **Executable**: Files with execute permissions set

## Solution

1. **Connect to the bandit4 server** using the password from Level 4:
   ```sh
   ssh bandit4@bandit.labs.overthewire.org -p 2220
   ```
   Use the password you found in the ASCII text file from Level 4.

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

4. **Explore the directory structure**:
   ```sh
   ls -la
   ```
   You'll see multiple subdirectories (typically named `maybehere00` through `maybehere19`):
   ```
   drwxr-xr-x 22 root root 4096 May  7 20:14 .
   drwxr-xr-x  3 root root 4096 May  7 20:14 ..
   drwxr-x--- 2 bandit5 bandit4 4096 May  7 20:14 maybehere00
   drwxr-x--- 2 bandit5 bandit4 4096 May  7 20:14 maybehere01
   ...
   drwxr-x--- 2 bandit5 bandit4 4096 May  7 20:14 maybehere19
   ```

5. **Use the find command with all required criteria**:
   ```sh
   find . -type f -size 1033c ! -executable
   ```
   Breaking down this command:
   - `.` - Search in current directory and subdirectories
   - `-type f` - Look for regular files only
   - `-size 1033c` - File must be exactly 1033 bytes
   - `! -executable` - File must NOT be executable
   
   The output will show the path to the matching file:
   ```
   ./maybehere07/.file2
   ```

6. **Verify the file properties** (optional):
   ```sh
   ls -la ./maybehere07/.file2
   file ./maybehere07/.file2
   wc -c ./maybehere07/.file2
   ```
   This confirms:
   - File permissions (not executable)
   - File type (ASCII text = human-readable)
   - File size (1033 bytes)

7. **Read the contents of the found file**:
   ```sh
   cat ./maybehere07/.file2
   ```
   This will display the password for the next level:
   ```
   <password>
   ```

8. **Connect to bandit5 using the found password**:
   ```sh
   ssh bandit5@bandit.labs.overthewire.org -p 2220
   ```

## Key Concepts Learned

- **Advanced file searching**: Using `find` with multiple criteria
- **File properties**: Understanding size, type, and permissions
- **Logical operators**: Using negation (`!`) in search expressions
- **Recursive searching**: Searching through directory trees
- **Command efficiency**: Finding files without manual checking

## Alternative Solutions

**Method 1: Step-by-step verification**
```sh
# Find files of exact size first
find . -type f -size 1033c

# Check if they're executable
find . -type f -size 1033c -executable
find . -type f -size 1033c ! -executable

# Verify they're human-readable
find . -type f -size 1033c ! -executable -exec file {} \;
```

**Method 2: Using multiple commands**
```sh
# Find all 1033-byte files
find . -size 1033c > candidates.txt

# Check each candidate
for file in $(cat candidates.txt); do
    echo "Checking: $file"
    file "$file"
    ls -la "$file"
done
```

**Method 3: One-liner with verification**
```sh
find . -type f -size 1033c ! -executable -exec sh -c 'echo "Found: $1"; file "$1"; cat "$1"' _ {} \;
```

## Additional Tips

- **Size specifications**:
  - `+n` - Greater than n
  - `-n` - Less than n  
  - `n` - Exactly n
  - Examples: `-size +100c` (>100 bytes), `-size -1k` (<1 KB)

- **Combining with other criteria**:
  ```sh
  find . -type f -size 1033c ! -executable -readable
  find . -type f -size 1033c ! -executable -name ".*"
  ```

- **Useful find expressions**:
  ```sh
  find . -name "*.txt" -size +1M                    # Large text files
  find . -type f -perm -u+x                         # Executable by owner
  find . -type f -mtime -1                          # Modified in last day
  find . -type f -empty                             # Empty files
  ```

## Common Pitfalls

- **Wrong size unit**: Forgetting the `c` suffix for bytes
- **Permission issues**: Not having read access to some directories
- **Executable confusion**: Mixing up `executable` vs `! -executable`
- **Path issues**: Not using proper relative/absolute paths
- **Multiple matches**: Not handling cases where multiple files match

## Security Implications

- **File discovery**: Attackers use `find` to locate sensitive files
- **Permission enumeration**: Finding files with specific permissions
- **Data exfiltration**: Locating files of specific sizes or types
- **Privilege escalation**: Finding SUID/SGID files
- **Log analysis**: Searching for files modified at specific times

## Advanced Techniques

**Complex searches**:
```sh
# Files owned by specific user, modified recently, and executable
find /usr -user root -mtime -7 -executable -type f

# Files with SUID bit set
find / -type f -perm -4000 2>/dev/null

# Large files that might contain data
find / -type f -size +100M 2>/dev/null

# Recently modified configuration files
find /etc -name "*.conf" -mtime -1 2>/dev/null
```

**Performance optimization**:
```sh
# Limit search depth
find . -maxdepth 3 -type f -size 1033c

# Use -prune to skip directories
find . -name ".git" -prune -o -type f -size 1033c -print

# Redirect error output
find . -type f -size 1033c ! -executable 2>/dev/null
```

**Combining with other tools**:
```sh
# Find and examine in one command
find . -type f -size 1033c ! -executable -exec head -5 {} \;

# Find and copy matching files
find . -type f -size 1033c ! -executable -exec cp {} /tmp/ \;

# Count matching files
find . -type f -size 1033c ! -executable | wc -l
```

This level demonstrates the power of the `find` command for complex file searches, a crucial skill for system administrators, security professionals, and anyone working with large file systems. The ability to search by multiple criteria simultaneously makes `find` an indispensable tool for digital forensics and incident response.
