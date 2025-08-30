# Level 2 - Files with Spaces

## Task

The password for the next level is stored in a file called `spaces in this filename` located in the home directory. Use this password to log into bandit2 using SSH.

```sh
Server: bandit.labs.overthewire.org
Port: 2220
Username: bandit2
Password: [found in "spaces in this filename" file]
```

## Theory

This level introduces the concept of handling files with spaces in their names in Unix-like systems. Spaces in filenames can be problematic because the shell interprets spaces as argument separators. There are several methods to handle files with spaces:

**Escaping with backslashes**: You can escape each space with a backslash (`\`) to tell the shell to treat the space as a literal character rather than a separator.

**Quoting**: You can enclose the entire filename in quotes (single or double) to preserve the spaces.

**Tab completion**: Most modern shells support tab completion, which automatically handles spaces by escaping them.

**Wildcards and globbing**: You can use pattern matching characters like `*` and `?` to match parts of filenames.

## Solution

1. **Connect to the bandit1 server** using the password from Level 1:
   ```sh
   ssh bandit1@bandit.labs.overthewire.org -p 2220
   ```
   Use the password you found in the `readme` file from Level 1.

2. **List the contents of the home directory**:
   ```sh
   ls
   ```
   You should see a file with spaces in its name:
   ```
   spaces in this filename
   ```

3. **Read the file using one of these methods**:

   **Method 1 - Using quotes**:
   ```sh
   cat "spaces in this filename"
   ```

   **Method 2 - Using backslash escaping**:
   ```sh
   cat spaces\ in\ this\ filename
   ```

   **Method 3 - Using tab completion**:
   ```sh
   cat sp<TAB>
   ```
   (Press TAB after typing "sp" and the shell will complete the filename with proper escaping)

   **Method 4 - Using wildcards**:
   ```sh
   cat *spaces*
   ```

4. **The file will display the password for the next level**:
   ```
   <password>
   ```

5. **Connect to bandit2 using the found password**:
   ```sh
   ssh bandit2@bandit.labs.overthewire.org -p 2220
   ```
   When prompted for the password, enter the password you found.

## Key Concepts Learned

- **Filename handling**: Understanding how to work with files that have spaces in their names
- **Shell escaping**: Using backslashes to escape special characters
- **Quoting**: Using quotes to preserve literal strings including spaces
- **Tab completion**: Leveraging shell features for easier file navigation
- **Wildcards**: Using pattern matching to select files

## Additional Tips

- **Best practice**: When naming files, avoid spaces and special characters to prevent these issues
- **Alternative characters**: Use underscores (`_`) or hyphens (`-`) instead of spaces
- **Double vs single quotes**: Both work for this purpose, but double quotes allow variable expansion while single quotes are more literal
- **Case sensitivity**: Remember that Unix filenames are case-sensitive

## Common Pitfalls

- Forgetting to escape spaces: `cat spaces in this filename` will try to read four separate files
- Inconsistent quoting: Starting with a quote but not ending with one
- Mixing methods: Don't combine escaping and quoting unnecessarily

This level teaches essential file handling skills that are crucial for working with real-world systems where filenames may contain spaces or special characters.
