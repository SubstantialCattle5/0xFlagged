# Level 11 - ROT13 Cipher Decoding

## Task

The password for the next level is stored in the file `data.txt`, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions.

Use this password to log into bandit12 using SSH.

```sh
Server: bandit.labs.overthewire.org
Port: 2220
Username: bandit11
Password: [ROT13 decoded password from data.txt]
```

## Theory

This level introduces the ROT13 cipher, a simple letter substitution cipher and an important concept in basic cryptography:

**ROT13 (Rotate by 13 places)**:
ROT13 is a simple letter substitution cipher that replaces each letter with the letter 13 positions after it in the alphabet:
- A → N, B → O, C → P, ..., M → Z
- N → A, O → B, P → C, ..., Z → M
- Numbers and special characters remain unchanged
- Case is preserved (uppercase stays uppercase, lowercase stays lowercase)

**Why ROT13?**:
- **Symmetric**: Applying ROT13 twice returns the original text
- **Simple**: Easy to implement and understand
- **Historical**: Used in early computer systems and Usenet for spoiler text
- **Educational**: Good introduction to substitution ciphers
- **Reversible**: Same operation encodes and decodes

**ROT13 Properties**:
- Only affects alphabetic characters (a-z, A-Z)
- Preserves word length and structure
- Preserves punctuation and spacing
- Not secure encryption - easily broken by frequency analysis
- Often used for text obfuscation rather than security

**The `tr` Command**:
The `tr` (translate) command is perfect for character substitution:
- `tr 'set1' 'set2'` - Translates characters from set1 to set2
- `tr 'A-Z' 'N-ZA-M'` - ROT13 for uppercase letters
- `tr 'a-z' 'n-za-m'` - ROT13 for lowercase letters
- Can be combined to handle both cases simultaneously

**Character Set Mappings**:
For ROT13 transformation:
```
Original:  ABCDEFGHIJKLMNOPQRSTUVWXYZ
ROT13:     NOPQRSTUVWXYZABCDEFGHIJKLM

Original:  abcdefghijklmnopqrstuvwxyz  
ROT13:     nopqrstuvwxyzabcdefghijklm
```

**Alternative Tools**:
- **Online ROT13 decoders**: Various web tools available
- **Python**: `'text'.encode('rot13')` (Python 2) or custom function
- **Caesar cipher tools**: ROT13 is Caesar cipher with shift of 13

## Solution

1. **Connect to the bandit10 server** using the password from Level 10:
   ```sh
   ssh bandit10@bandit.labs.overthewire.org -p 2220
   ```
   Use the base64 decoded password from Level 10.

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
   You'll see text that looks like gibberish but maintains readable structure.

4. **Decode using tr command** (Method 1 - Complete solution):
   ```sh
   cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
   ```
   This handles both uppercase and lowercase letters in one command.

5. **Alternative: Separate transformations** (Method 2):
   ```sh
   cat data.txt | tr 'A-Z' 'N-ZA-M' | tr 'a-z' 'n-za-m'
   ```

6. **Save the decoded output** (optional):
   ```sh
   cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m' > decoded.txt
   cat decoded.txt
   ```

7. **One-liner to see the password**:
   ```sh
   tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt
   ```

8. **Connect to bandit12 using the decoded password**:
   ```sh
   ssh bandit12@bandit.labs.overthewire.org -p 2220
   ```
   Enter the ROT13 decoded password when prompted.

## Key Concepts Learned

- **Substitution ciphers**: Understanding how characters can be systematically replaced
- **ROT13 cipher**: Learning a fundamental cipher used in computer science
- **Character translation**: Using `tr` for text transformation tasks
- **Symmetric operations**: Understanding reversible transformations
- **Text processing**: Command-line text manipulation techniques

## Alternative Solutions

**Method 1: Using sed (more complex)**
```sh
cat data.txt | sed 'y/ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz/NOPQRSTUVWXYZABCDEFGHIJKLMnopqrstuvwxyzabcdefghijklm/'
```

**Method 2: Python one-liner**
```sh
python3 -c "import sys; print(''.join([chr((ord(c)-ord('A')+13)%26+ord('A')) if c.isupper() else chr((ord(c)-ord('a')+13)%26+ord('a')) if c.islower() else c for c in sys.stdin.read()]))" < data.txt
```

**Method 3: Manual lookup** (educational)
```sh
# Create a mapping table and manually substitute each character
echo "A B C D E F G H I J K L M N O P Q R S T U V W X Y Z"
echo "N O P Q R S T U V W X Y Z A B C D E F G H I J K L M"
```

**Method 4: Using rot13 if available**
```sh
# Some systems have a rot13 command
rot13 < data.txt
```

## Understanding the tr Command

**Basic tr syntax**:
- `tr 'ABC' 'XYZ'` - Replace A→X, B→Y, C→Z
- `tr 'a-z' 'A-Z'` - Convert lowercase to uppercase
- `tr -d 'aeiou'` - Delete all vowels
- `tr -s ' '` - Squeeze multiple spaces into one

**Character ranges**:
- `A-Z` - All uppercase letters
- `a-z` - All lowercase letters  
- `0-9` - All digits
- `A-Za-z` - All letters
- `[:alpha:]` - All alphabetic characters
- `[:digit:]` - All numeric characters

## Common Mistakes

1. **Wrong character order**: Using 'A-ZN-M' instead of 'N-ZA-M'
2. **Missing case handling**: Only transforming uppercase OR lowercase
3. **Incorrect range syntax**: Using 'A-z' which includes special characters
4. **Pipeline errors**: Forgetting to pipe input to tr command
5. **Character set confusion**: Mixing up source and destination character sets

## Security Notes

- **ROT13 is NOT secure encryption** - it's trivially broken
- **Frequency analysis**: Letter frequencies remain unchanged
- **Pattern recognition**: Word patterns and lengths are preserved
- **Historical use**: Mainly used for spoiler protection, not security
- **Educational value**: Good for learning cipher concepts before stronger methods

## Real-World Applications

- **Usenet newsgroups**: Hiding spoilers in discussions
- **Email obfuscation**: Basic email address protection (though ineffective)
- **Puzzle games**: Simple word games and brain teasers
- **Educational**: Teaching basic cryptographic concepts
- **Text processing**: Sometimes used in data transformation pipelines

This level teaches fundamental text transformation and introduces basic cryptographic concepts that are essential for understanding more complex security challenges.
