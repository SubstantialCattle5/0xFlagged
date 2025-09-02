# Level 6 - System-wide File Search with Ownership

## Task

The password for the next level is stored in a file somewhere on the server and has all of the following properties:
- Owned by user bandit7
- Owned by group bandit6  
- 33 bytes in size

Use this password to log into bandit7 using SSH.

```sh
Server: bandit.labs.overthewire.org
Port: 2220
Username: bandit6
Password: [found in file with specific ownership and size properties]
```

## Theory

This level expands file searching to the entire filesystem and introduces ownership-based search criteria. Understanding file ownership and system-wide searches is crucial for system administration and security analysis:

**File Ownership in Linux**:
- Every file has an **owner** (user) and a **group**
- Owner permissions apply to the file's owner
- Group permissions apply to members of the file's group
- Other permissions apply to everyone else

**User and Group Identification**:
- Users are identified by username or UID (User ID)
- Groups are identified by group name or GID (Group ID)
- `ls -l` shows ownership: `owner group`
- `id` command shows current user's UID, GID, and groups

**System-wide Searches**:
- Searching from root (`/`) covers the entire filesystem
- Permission denied errors are common when accessing restricted areas
- Error redirection (`2>/dev/null`) helps filter noise
- System searches can be resource-intensive

**Advanced Find Criteria**:
- `-user username` - Files owned by specific user
- `-group groupname` - Files owned by specific group
- `-uid n` - Files owned by user with UID n
- `-gid n` - Files owned by group with GID n
- Combining multiple criteria with logical AND

## Solution

1. **Connect to the bandit5 server** using the password from Level 5:
   ```sh
   ssh bandit5@bandit.labs.overthewire.org -p 2220
   ```
   Use the password you found in the 1033-byte file from Level 5.

2. **Understand the search criteria**:
   We need to find a file that is:
   - Owned by user `bandit7`
   - Owned by group `bandit6`
   - Exactly `33` bytes in size
   - Located anywhere on the server

3. **Perform a system-wide search**:
   ```sh
   find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
   ```
   Breaking down this command:
   - `/` - Search from the root directory (entire filesystem)
   - `-user bandit7` - File must be owned by user bandit7
   - `-group bandit6` - File must be owned by group bandit6
   - `-size 33c` - File must be exactly 33 bytes
   - `2>/dev/null` - Redirect error messages to avoid permission denied spam

   The output will show the path to the matching file:
   ```
   /var/lib/dpkg/info/bandit7.password
   ```

4. **Verify the file properties** (optional):
   ```sh
   ls -la /var/lib/dpkg/info/bandit7.password
   file /var/lib/dpkg/info/bandit7.password
   wc -c /var/lib/dpkg/info/bandit7.password
   ```
   This confirms:
   - Owner: bandit7
   - Group: bandit6
   - Size: 33 bytes
   - Type: ASCII text

5. **Read the contents of the found file**:
   ```sh
   cat /var/lib/dpkg/info/bandit7.password
   ```
   This will display the password for the next level:
   ```
   <password>
   ```

6. **Connect to bandit6 using the found password**:
   ```sh
   ssh bandit6@bandit.labs.overthewire.org -p 2220
   ```

## Key Concepts Learned

- **System-wide file searching**: Using `find` on the entire filesystem
- **File ownership criteria**: Searching by user and group ownership
- **Error handling**: Using redirection to filter permission errors
- **Multi-criteria searches**: Combining user, group, and size requirements
- **Filesystem structure**: Understanding where system files are typically located

## Alternative Solutions

**Method 1: Step-by-step verification**
```sh
# Find files owned by bandit7
find / -user bandit7 2>/dev/null

# Add group criteria
find / -user bandit7 -group bandit6 2>/dev/null

# Add size criteria
find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

**Method 2: Search in specific directories**
```sh
# Common locations for system files
find /var -user bandit7 -group bandit6 -size 33c 2>/dev/null
find /tmp -user bandit7 -group bandit6 -size 33c 2>/dev/null
find /etc -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

**Method 3: Verbose output with file details**
```sh
find / -user bandit7 -group bandit6 -size 33c -exec ls -la {} \; 2>/dev/null
```

**Method 4: Using numeric IDs instead of names**
```sh
# First find the UID and GID
id bandit7
getent group bandit6

# Then search using numeric IDs (if names don't work)
find / -uid 11007 -gid 11006 -size 33c 2>/dev/null
```

## Additional Tips

- **Error handling**:
  ```sh
  # Save errors to a file for later analysis
  find / -user bandit7 -group bandit6 -size 33c 2>errors.log
  
  # Show only permission denied errors
  find / -user bandit7 -group bandit6 -size 33c 2>&1 | grep "Permission denied"
  ```

- **Performance optimization**:
  ```sh
  # Limit search to specific filesystems
  find / -mount -user bandit7 -group bandit6 -size 33c 2>/dev/null
  
  # Skip certain directories
  find / -path "/proc" -prune -o -user bandit7 -group bandit6 -size 33c -print 2>/dev/null
  ```

- **Alternative search approaches**:
  ```sh
  # Use locate (if database is available)
  locate -u bandit7 | xargs ls -la 2>/dev/null | grep bandit6
  
  # Search in user home directories first
  find /home -user bandit7 -group bandit6 -size 33c 2>/dev/null
  ```

## Common Pitfalls

- **Permission errors**: Many directories are not readable by regular users
- **Case sensitivity**: Usernames and group names are case-sensitive
- **Numeric vs names**: Sometimes numeric IDs work when names don't
- **Large output**: System-wide searches can produce lots of permission errors
- **Resource usage**: Searching the entire filesystem can be slow

## Security Implications

- **Information gathering**: Attackers search for files with specific ownership
- **Privilege escalation**: Finding files owned by privileged users
- **Data discovery**: Locating sensitive files across the system
- **System reconnaissance**: Understanding file organization and permissions
- **Forensic analysis**: Tracking file ownership in incident response

## Advanced Techniques

**Complex ownership searches**:
```sh
# Files owned by specific user but NOT in their home directory
find / -user bandit7 ! -path "/home/bandit7/*" 2>/dev/null

# Files with SUID bit set owned by root
find / -user root -perm -4000 2>/dev/null

# Files writable by group members
find / -group bandit6 -perm -020 2>/dev/null

# Recently modified files owned by specific user
find / -user bandit7 -mtime -1 2>/dev/null
```

**Combining with other tools**:
```sh
# Search and examine file contents
find / -user bandit7 -group bandit6 -size 33c -exec cat {} \; 2>/dev/null

# Search and copy matching files
find / -user bandit7 -group bandit6 -size 33c -exec cp {} /tmp/ \; 2>/dev/null

# Count files by ownership
find / -user bandit7 2>/dev/null | wc -l
```

**System analysis**:
```sh
# Find all users with files in /var
find /var -type f -exec stat -c "%U %n" {} \; 2>/dev/null | sort | uniq

# Analyze file distribution by group
find / -type f -exec stat -c "%G" {} \; 2>/dev/null | sort | uniq -c | sort -nr

# Find unusual file locations for specific users
find / -user bandit7 -not -path "/home/bandit7/*" -not -path "/tmp/*" 2>/dev/null
```

## System Directories Overview

Common locations where system files might be found:
- `/var/lib/` - Variable state information for packages
- `/var/log/` - System and application log files  
- `/tmp/` - Temporary files (often writable by all users)
- `/etc/` - System configuration files
- `/usr/` - User binaries and system files
- `/opt/` - Optional application packages
- `/home/` - User home directories

This level teaches comprehensive file searching across the entire filesystem using ownership criteria, a fundamental skill for system administration, security analysis, and digital forensics. Understanding how to efficiently search large filesystems while handling permission restrictions is essential for managing Linux systems.
