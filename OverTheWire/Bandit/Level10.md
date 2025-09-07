# Level 10 - Base64 Decoding

## Task

The password for the next level is stored in the file `data.txt`, which contains base64 encoded data.

Use this password to log into bandit11 using SSH.

```sh
Server: bandit.labs.overthewire.org
Port: 2220
Username: bandit10
Password: [base64 decoded password from data.txt]
```

## Theory

This level introduces base64 encoding and decoding, a fundamental concept in data encoding and web security:

**Base64 Encoding**:
Base64 is a binary-to-text encoding scheme that represents binary data in an ASCII string format. It's commonly used for:
- Email attachments (MIME)
- Data URLs in web browsers
- Storing binary data in text-based formats (JSON, XML)
- Basic authentication in HTTP headers
- Certificate and key storage (PEM format)

**Why Base64?**:
- Ensures data remains intact during transport across systems that handle text
- Safe for use in URLs, email, and other text-based protocols
- 6-bit encoding using 64 printable ASCII characters: A-Z, a-z, 0-9, +, /
- Uses '=' for padding to ensure proper alignment

**Base64 Characteristics**:
- Increases data size by approximately 33% (4 characters for every 3 bytes)
- No line breaks in standard base64 (though some implementations add them)
- Case-sensitive (uppercase and lowercase letters have different meanings)
- Commonly seen ending with '=' or '==' for padding

**The `base64` Command**:
Linux provides the `base64` command for encoding and decoding:
- `base64 file` - Encode file contents to base64
- `base64 -d file` - Decode base64 data from file
- `echo "text" | base64` - Encode text string
- `echo "encoded" | base64 -d` - Decode text string

**Common Base64 Patterns**:
Base64 encoded text typically looks like:
```
TWFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5IGhpcyByZWFzb24=
VGhlIHF1aWNrIGJyb3duIGZveCBqdW1wcyBvdmVyIHRoZSBsYXp5IGRvZw==
```

## Solution

1. **Connect to the bandit9 server** using the password from Level 9:
   ```sh
   ssh bandit9@bandit.labs.overthewire.org -p 2220
   ```
   Use the password you found with the strings and grep commands.

2. **List the contents of the home directory**:
   ```sh
   ls
   ```
   You should see:
   ```
   data.txt
   ```

3. **Examine the file contents**:
   ```sh
   cat data.txt
   ```
   You'll see base64 encoded data that looks like random letters, numbers, and symbols.

4. **Check the file type** (optional):
   ```sh
   file data.txt
   ```
   This might show it as ASCII text.

5. **Decode the base64 data**:
   ```sh
   base64 -d data.txt
   ```
   This will decode the base64 content and reveal the password for the next level.

6. **Alternative: Pipe the output to see it clearly**:
   ```sh
   cat data.txt | base64 -d
   ```

7. **Store the decoded output** (if needed):
   ```sh
   base64 -d data.txt > decoded.txt
   cat decoded.txt
   ```

8. **Connect to bandit11 using the decoded password**:
   ```sh
   ssh bandit11@bandit.labs.overthewire.org -p 2220
   ```
   Enter the decoded password when prompted.

## Key Concepts Learned

- **Base64 encoding/decoding**: Understanding how binary data is represented in text format
- **Data encoding schemes**: Recognizing when data has been encoded
- **Command-line decoding**: Using built-in tools to decode data
- **File format recognition**: Identifying encoded vs. plain text data
- **Data transformation**: Converting between different data representations

## Alternative Solutions

**Method 1: Direct command substitution**
```sh
# Use the decoded password directly for SSH
ssh bandit11@bandit.labs.overthewire.org -p 2220
# When prompted, use: $(base64 -d data.txt)
```

**Method 2: One-liner with output**
```sh
echo "Password is: $(base64 -d data.txt)"
```

**Method 3: Verify the encoding first**
```sh
# Check if it's valid base64
base64 -d data.txt > /dev/null 2>&1 && echo "Valid base64" || echo "Invalid base64"
```

## Security Notes

- Base64 is **NOT encryption** - it's just encoding
- Anyone can easily decode base64 data
- Never use base64 alone for securing sensitive information
- Base64 is often used to obfuscate, not secure, data
- In real security contexts, base64 might encode encrypted data

This level teaches fundamental data encoding concepts essential for understanding how data is transmitted and stored in various systems.
