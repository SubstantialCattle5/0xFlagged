# Mod 26

**Author:** Pandu  
**Category:** Cryptography  
**Difficulty:** Easy  
**Platform:** PicoCTF

## Description

Cryptography can be easy, do you know what ROT13 is?

**Given Flag (Encrypted):** `cvpbPGS{arkg_gvzr_V'yy_gel_2_ebhaqf_bs_ebg13_jdJBFOXJ}`

## Analysis

This challenge involves ROT13, which is a simple letter substitution cipher that replaces each letter with the letter 13 positions ahead in the alphabet. Since there are 26 letters in the alphabet, applying ROT13 twice returns the original text (26 mod 26 = 0).

The name "Mod 26" refers to modular arithmetic with the alphabet size (26 letters).

## Solution

ROT13 is a Caesar cipher with a shift of 13. To decode it, we can:

1. **Method 1: Use the `tr` command**
   ```bash
   echo "cvpbPGS{arkg_gvzr_V'yy_gel_2_ebhaqf_bs_ebg13_jdJBFOXJ}" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
   ```

2. **Method 2: Apply ROT13 again** (since ROT13 is its own inverse)

3. **Method 3: Manual substitution**
   - a↔n, b↔o, c↔p, d↔q, e↔r, f↔s, g↔t, h↔u, i↔v, j↔w, k↔x, l↔y, m↔z

## Step-by-Step Decoding

```
Original: cvpbPGS{arkg_gvzr_V'yy_gel_2_ebhaqf_bs_ebg13_jdJBFOXJ}
Decoded:  picoCTF{next_time_I'll_try_2_rounds_of_rot13_wqWOSBKW}
```

**Flag:** `picoCTF{next_time_I'll_try_2_rounds_of_rot13_wqWOSBKW}`

## Key Concepts

- **ROT13**: A Caesar cipher with shift value 13
- **Modular Arithmetic**: Operations with remainders (mod 26 for alphabet)
- **Self-inverse property**: ROT13(ROT13(text)) = text
- **Caesar Cipher**: Substitution cipher that shifts letters by a fixed amount

## Tools Used

- `tr` command (Unix/Linux text transformation utility)
- Manual decoding
- Online ROT13 decoders (alternative)

## Notes

The flag hints at "2 rounds of rot13" which would actually return us back to the original encrypted text, since ROT13 applied twice cancels out. This is a clever reference to the mathematical property of ROT13.
