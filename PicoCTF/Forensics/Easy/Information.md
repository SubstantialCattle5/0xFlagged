# Information - Metadata Forensics

## Challenge Details

**Author:** susie  
**Category:** Forensics  
**Difficulty:** Easy  
**Points:** 10  

**Description:** Files can always be changed in a secret way. Can you find the flag? cat.jpg

**Files Provided:** `cat.jpg`

## Theory

This challenge introduces the fundamental concept of **metadata forensics** and **steganography** in digital forensics:

### File Metadata
Every digital file contains metadata - data about the data. For images, this includes:
- **EXIF data**: Camera settings, timestamps, GPS coordinates
- **IPTC data**: Copyright, keywords, captions
- **XMP data**: Advanced metadata schemas
- **Custom fields**: Arbitrary data that can be embedded

### EXIF (Exchangeable Image File Format)
EXIF is a standard that specifies the formats for images, sound, and ancillary tags used by digital cameras, scanners, and other systems handling image files. Common EXIF fields include:
- Camera make and model
- Exposure settings (ISO, aperture, shutter speed)
- Date and time taken
- GPS coordinates
- Software used for processing

### Steganography in Metadata
Attackers and CTF creators often hide information in:
- Non-standard metadata fields
- Modified standard fields with unusual values
- Base64 encoded strings in text fields
- Custom metadata schemas

### Tools for Metadata Analysis
- **exiftool**: The most comprehensive metadata reading/writing tool
- **file**: Basic file information
- **strings**: Extract printable strings from files
- **hexdump/xxd**: Binary analysis tools

## Solution

### Step 1: Download and Examine the File
```bash
# Download the file from the challenge (if needed)
wget [challenge_url]/cat.jpg

# Check basic file information
file cat.jpg
```

### Step 2: Extract All Metadata
```bash
# Use exiftool to examine all metadata
exiftool cat.jpg
```

**Expected Output (partial):**
```
ExifTool Version Number         : 12.16
File Name                       : cat.jpg
Directory                       : .
File Size                       : 878 KiB
File Modification Date/Time     : 2021:03:15 20:24:46-04:00
File Access Date/Time           : 2021:03:15 20:24:46-04:00
File Creation Date/Time         : 2021:03:15 20:24:46-04:00
File Permissions                : rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : inches
X Resolution                    : 72
Y Resolution                    : 72
...
License                         : cGljb0NURnt0aGVfbTN0YWRhdGFfMXNfbW9kaWZpZWR9
...
```

### Step 3: Identify Suspicious Data
Notice the `License` field contains what appears to be a Base64-encoded string:
```
License: cGljb0NURnt0aGVfbTN0YWRhdGFfMXNfbW9kaWZpZWR9
```

### Step 4: Decode the Base64 String
```bash
# Method 1: Using command line
echo "cGljb0NURnt0aGVfbTN0YWRhdGFfMXNfbW9kaWZpZWR9" | base64 --decode

# Method 2: Using base64 utility directly
echo "cGljb0NURnt0aGVfbTN0YWRhdGFfMXNfbW9kaWZpZWR9" | base64 -d
```

**Output:**
```
picoCTF{the_m3tadata_1s_modified}
```

### Alternative Methods

**Using Python:**
```python
import base64

encoded_string = "cGljb0NURnt0aGVfbTN0YWRhdGFfMXNfbW9kaWZpZWR9"
decoded = base64.b64decode(encoded_string).decode('utf-8')
print(decoded)
```

**Using Online Tools:**
- CyberChef (https://gchq.github.io/CyberChef/)
- Base64Decode.org
- Any online Base64 decoder

**Using strings command:**
```bash
# Sometimes you can find embedded text directly
strings cat.jpg | grep -i "pico"
```

## Flag
```
picoCTF{the_m3tadata_1s_modified}
```

## Key Concepts Learned

- **Metadata analysis**: Understanding how to extract and examine file metadata
- **EXIF data inspection**: Using exiftool to reveal hidden information
- **Base64 encoding/decoding**: Recognizing and decoding Base64 encoded strings
- **Steganography basics**: How information can be hidden in plain sight
- **Digital forensics workflow**: Systematic approach to file analysis

## Tools and Commands Summary

| Tool | Purpose | Command Example |
|------|---------|-----------------|
| `exiftool` | Extract metadata | `exiftool filename.jpg` |
| `file` | File type identification | `file filename.jpg` |
| `strings` | Extract text strings | `strings filename.jpg` |
| `base64` | Decode Base64 | `echo "string" \| base64 -d` |
| `hexdump` | Binary analysis | `hexdump -C filename.jpg` |

## Advanced Techniques

### Comprehensive Metadata Extraction
```bash
# Extract all metadata in different formats
exiftool -j cat.jpg > metadata.json    # JSON format
exiftool -X cat.jpg > metadata.xml     # XML format
exiftool -csv cat.jpg > metadata.csv   # CSV format
```

### Searching for Specific Patterns
```bash
# Look for specific patterns in metadata
exiftool cat.jpg | grep -i "flag\|ctf\|pico"

# Search for Base64 patterns (ends with = or ==)
exiftool cat.jpg | grep -E "[A-Za-z0-9+/]{20,}={0,2}"
```

### Batch Analysis
```bash
# Analyze multiple files at once
exiftool *.jpg
exiftool -r ./images/  # Recursive directory analysis
```

## Security Implications

### For Defenders
- **Privacy concerns**: Images can leak sensitive location and device information
- **Data exfiltration**: Metadata can be used to hide stolen information
- **Evidence tampering**: Timestamps and other metadata can be modified

### For Attackers
- **Reconnaissance**: Metadata reveals information about systems and users
- **Steganography**: Hiding malicious payloads or stolen data
- **Anti-forensics**: Removing or modifying metadata to cover tracks

## Common Pitfalls

- **Overlooking non-standard fields**: Always examine all metadata fields, not just common ones
- **Case sensitivity**: Some tools are case-sensitive when searching
- **Encoding assumptions**: Data might be encoded in formats other than Base64
- **Tool limitations**: Different tools may show different metadata fields

## Best Practices

1. **Always start with exiftool**: It's the most comprehensive metadata tool
2. **Check multiple encoding schemes**: Base64, hex, ROT13, etc.
3. **Look for patterns**: CTF flags often follow predictable formats
4. **Use multiple tools**: Cross-reference findings with different utilities
5. **Document findings**: Keep track of what you've checked and found

