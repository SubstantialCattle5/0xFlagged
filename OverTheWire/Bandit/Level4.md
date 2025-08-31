# Level 4 - Human-Readable Files

## Task

The password for the next level is stored in the only human-readable file in the `inhere` directory. Somewhere among all the files. Use this password to log into bandit5 using SSH.

```sh
Server: bandit.labs.overthewire.org
Port: 2220
Username: bandit4
Password: [found in the only human-readable file in inhere directory]
```

## Theory

This level introduces file type identification and the concept of binary vs. text files. Understanding file types is crucial for security analysis and system administration:

**File Types in Unix**:
- **Text files**: Contain human-readable characters (ASCII/UTF-8)
- **Binary files**: Contain non-text data (executables, images, compiled code)
- **Special files**: Device files, pipes, sockets, etc.

**The `file` command**:
The `file` command determines file type by examining the file's content, not just its extension:
- `file filename` - Shows the file type and encoding
- `file *` - Check all files in current directory
- `file -b filename` - Brief output (just the file type)

**Common file types you might encounter**:
- `ASCII text` - Human-readable text file
- `data` - Binary data
- `executable` - Binary executable file
- `empty` - Empty file
- `symbolic link` - Link to another file

**Wildcards and Globbing**:
- `*` - Matches any number of characters
- `?` - Matches exactly one character
- `[abc]` - Matches any one of the characters a, b, or c
- `{txt,md}` - Matches either txt or md

## Solution

1. **Connect to the bandit3 server** using the password from Level 3:
   ```sh
   ssh bandit3@bandit.labs.overthewire.org -p 2220
   ```
   Use the password you found in the hidden file from Level 3.

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

4. **List all files in the directory**:
   ```sh
   ls
   ```
   You'll see multiple files, typically named something like:
   ```
   -file00  -file01  -file02  -file03  -file04  -file05  -file06  -file07  -file08  -file09
   ```
   Note: The filenames start with a dash (`-`), which can cause issues with some commands.

5. **Check the file types of all files**:
   ```sh
   file ./*
   ```
   This will show the type of each file. You'll see output like:
   ```
   ./-file00: data
   ./-file01: data
   ./-file02: data
   ./-file03: ASCII text
   ./-file04: data
   ./-file05: data
   ./-file06: data
   ./-file07: data
   ./-file08: data
   ./-file09: data
   ```
   Look for the file marked as "ASCII text" - this is the human-readable file.

6. **Read the human-readable file**:
   ```sh
   cat ./-file03
   ```
   (Replace `file03` with whichever file was identified as ASCII text)
   
   Alternatively, you can use a one-liner to find and read the text file:
   ```sh
   cat $(file ./* | grep "ASCII text" | cut -d: -f1)
   ```

7. **The password will be displayed**:
   ```
   <password>
   ```

8. **Connect to bandit4 using the found password**:
   ```sh
   ssh bandit4@bandit.labs.overthewire.org -p 2220
   ```

## Key Concepts Learned

- **File type identification**: Using the `file` command to determine file content types
- **Handling special filenames**: Dealing with files that start with dashes
- **Wildcards**: Using `*` to operate on multiple files
- **Path prefixes**: Using `./` to explicitly specify current directory
- **Command chaining**: Combining commands with pipes and command substitution

## Alternative Solutions

**Method 1: Loop through all files**
```sh
for f in ./*; do
    if file "$f" | grep -q "ASCII text"; then
        cat "$f"
    fi
done
```

**Method 2: Using find command**
```sh
find . -type f -exec file {} \; | grep "ASCII text" | cut -d: -f1 | xargs cat
```

**Method 3: Check each file manually**
```sh
file ./-file00
file ./-file01
# ... continue until you find ASCII text
cat ./-file0X  # X = number of the ASCII text file
```

## Additional Tips

- **Dealing with dashes in filenames**: 
  - Always use `./` prefix: `cat ./-filename`
  - Or use `--` to end option parsing: `cat -- -filename`
  
- **File command variations**:
  - `file -i filename` - Shows MIME type
  - `file -b filename` - Brief output without filename
  - `file -L filename` - Follow symbolic links

- **Useful file identification commands**:
  - `head -n 1 filename` - Show first line (often reveals if it's text)
  - `strings filename` - Extract readable strings from any file
  - `hexdump -C filename | head` - View file in hexadecimal

## Common Pitfalls

- **Filename issues**: Forgetting to use `./` with dash-prefixed filenames
- **Assuming by extension**: Don't rely on file extensions; use the `file` command
- **Missing files**: Make sure you're checking all files in the directory
- **Case sensitivity**: File types are case-sensitive in output

## Security Implications

- **File type spoofing**: Attackers might disguise malicious files with misleading extensions
- **Data exfiltration**: Understanding file types helps identify what data might be hidden
- **Forensic analysis**: File type identification is crucial for digital forensics
- **Steganography**: Data can be hidden in files that appear to be of different types

## Advanced Techniques

**Using magic numbers**: Files are identified by their "magic numbers" (specific byte sequences at the beginning):
```sh
hexdump -C filename | head -1
```

**Bulk file analysis**:
```sh
file * | grep -v "data" | grep -v "empty"
```

**Finding specific file types**:
```sh
find . -type f -exec file {} \; | grep "text" | cut -d: -f1
```

This level teaches the importance of understanding file types beyond their names or extensions, a critical skill for cybersecurity professionals when analyzing unknown files or conducting forensic investigations.
