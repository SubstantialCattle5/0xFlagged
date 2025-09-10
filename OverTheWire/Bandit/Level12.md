# Level 12 - Hexdump and Multiple Compression

## Task

The password for the next level is stored in the file `data.txt`, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under `/tmp` in which you can work using `mkdir`. For example: `mkdir /tmp/myname123`. Then copy the datafile using `cp`, and rename it using `mv` (read the manpages!)

Use this password to log into bandit13 using SSH.

```sh
Server: bandit.labs.overthewire.org
Port: 2220
Username: bandit12
Password: [found after reversing hexdump and decompressing multiple times]
```

## Task Overview

This is one of the most complex Bandit levels, involving:
1. **Hexdump reversal** - Converting hex representation back to binary
2. **File type identification** - Determining what kind of compressed file you have
3. **Multiple decompression** - Repeatedly decompressing different formats
4. **File manipulation** - Copying, renaming, and managing temporary files

## Theory

This level combines several important system administration and forensics concepts:

**Hexdump and Binary Data**:
A hexdump is a human-readable representation of binary data where each byte is shown as two hexadecimal digits:
- `hexdump -C file` - Create canonical hexdump
- `xxd file` - Another hexdump tool with different format
- `xxd -r` - Reverse a hexdump back to binary data
- Each line typically shows 16 bytes with their hex and ASCII representation

**File Type Detection**:
The `file` command analyzes file contents to determine type:
- `file filename` - Identify file type based on magic numbers
- Works regardless of file extension
- Essential for determining which decompression tool to use
- Recognizes hundreds of file formats

**Common Compression Formats**:

**gzip (.gz)**:
- `gzip file` - Compress file
- `gunzip file.gz` or `gzip -d file.gz` - Decompress
- Creates `.gz` extension
- Single file compression

**bzip2 (.bz2)**:
- `bzip2 file` - Compress file  
- `bunzip2 file.bz2` or `bzip2 -d file.bz2` - Decompress
- Creates `.bz2` extension
- Better compression than gzip but slower

**tar (tape archive)**:
- `tar -cf archive.tar files` - Create archive
- `tar -xf archive.tar` - Extract archive
- `tar -tf archive.tar` - List contents
- Often combined with compression: `.tar.gz`, `.tar.bz2`

**File Extensions vs. Actual Format**:
- File extensions can be misleading or missing
- Always use `file` command to verify actual format
- Magic numbers in file headers determine true type
- Compressed files can be nested (gzip inside tar inside bzip2)

## Solution

1. **Connect to the bandit11 server** using the password from Level 11:
   ```sh
   ssh bandit11@bandit.labs.overthewire.org -p 2220
   ```
   Use the ROT13 decoded password from Level 11.

2. **Create a working directory**:
   ```sh
   mkdir /tmp/mywork123
   cd /tmp/mywork123
   ```

3. **Copy the data file to your working directory**:
   ```sh
   cp ~/data.txt .
   ```

4. **Examine the original file**:
   ```sh
   cat data.txt
   ```
   You'll see hexadecimal data that looks like:
   ```
   00000000: 1f8b 0808 2773 4564 0003 6461 7461 322e  ...'sEd..data2.
   00000010: 6269 6e00 0213 4fdc 91b4 4815 47e4 9484  bin...O...H.G...
   ```

5. **Convert hexdump back to binary**:
   ```sh
   xxd -r data.txt data.bin
   ```

6. **Check the file type**:
   ```sh
   file data.bin
   ```
   This will tell you what kind of compressed file it is.

7. **Begin the decompression process**:
   
   Based on the file type, use the appropriate tool. You'll need to repeat this process multiple times:

   **If it's gzip**:
   ```sh
   mv data.bin data.gz
   gunzip data.gz
   file data  # Check new file type
   ```

   **If it's bzip2**:
   ```sh
   mv data.bin data.bz2
   bunzip2 data.bz2
   file data  # Check new file type
   ```

   **If it's tar**:
   ```sh
   mv data.bin data.tar
   tar -xf data.tar
   ls  # See what was extracted
   file *  # Check all extracted files
   ```

8. **Repeat the process**:
   Keep checking file types and decompressing until you get a plain text file:
   ```sh
   # Example workflow (actual sequence may vary):
   file data
   mv data data.gz
   gunzip data.gz
   file data
   mv data data.bz2
   bunzip2 data.bz2
   file data
   mv data data.tar
   tar -xf data.tar
   ls
   file data5.bin
   # Continue until you find ASCII text...
   ```

9. **Find the final password**:
   ```sh
   cat final_file  # Whatever the final decompressed file is named
   ```

10. **Connect to bandit13**:
    ```sh
    ssh bandit13@bandit.labs.overthewire.org -p 2220
    ```
    Use the password found in the final decompressed file.

## Key Concepts Learned

- **Hexdump analysis**: Converting between hex representation and binary data
- **File type identification**: Using magic numbers to determine file formats
- **Multiple compression**: Understanding nested/layered compression
- **Temporary directories**: Working in `/tmp` for file manipulation
- **File management**: Copying, moving, and renaming files appropriately
- **Decompression tools**: Using gzip, bzip2, and tar utilities

## Detailed Workflow Strategy

**Step-by-step approach**:
1. Always use `file` to identify current file type
2. Rename file with appropriate extension before decompressing
3. Decompress using the correct tool
4. Check the result with `file` again
5. Repeat until you get ASCII text

**Common sequence** (may vary):
```sh
xxd -r data.txt data.bin
file data.bin          # gzip compressed
mv data.bin data.gz
gunzip data.gz
file data              # bzip2 compressed  
mv data data.bz2
bunzip2 data.bz2
file data              # POSIX tar archive
mv data data.tar
tar -xf data.tar
ls                     # Shows data5.bin
file data5.bin         # tar archive
mv data5.bin data5.tar
tar -xf data5.tar
ls                     # Shows data6.bin
file data6.bin         # bzip2 compressed
mv data6.bin data6.bz2
bunzip2 data6.bz2
file data6             # tar archive
mv data6 data6.tar
tar -xf data6.tar
ls                     # Shows data8.bin
file data8.bin         # gzip compressed
mv data8.bin data8.gz
gunzip data8.gz
file data8             # ASCII text
cat data8              # Finally! The password!
```

## Alternative Tools

**Other hexdump tools**:
```sh
# If xxd is not available
od -t x1 -A x data.txt  # Create hexdump with od
# Manual conversion may be needed
```

**Alternative decompression**:
```sh
# Using zcat for gzip (doesn't modify original)
zcat file.gz > decompressed

# Using bzcat for bzip2
bzcat file.bz2 > decompressed

# Verbose tar
tar -xvf archive.tar
```

## Common Mistakes

1. **Not using `file` command**: Guessing file types instead of checking
2. **Wrong file extensions**: Not renaming files before decompression
3. **Working in home directory**: Creating mess instead of using `/tmp`
4. **Not tracking files**: Losing track of which file to process next
5. **Stopping too early**: Not decompressing until reaching plain text
6. **Overwriting files**: Not being careful with file names during process

## Tips for Success

1. **Use descriptive names**: Rename files to track the decompression step
2. **List frequently**: Use `ls` to see what files you have
3. **Document your steps**: Keep track of what you've done
4. **Be patient**: This level requires multiple iterations
5. **Use tab completion**: Avoid typos in filenames
6. **Check file sizes**: Decompressed files should generally be larger

## Security and Forensics Applications

- **Malware analysis**: Unpacking compressed/obfuscated malware
- **Digital forensics**: Recovering data from compressed archives
- **Data exfiltration**: Understanding how attackers hide data
- **File format analysis**: Identifying unknown file types
- **Archive exploration**: Extracting nested archive structures

This level teaches essential skills for handling compressed data and file analysis that are crucial in cybersecurity, system administration, and digital forensics.
