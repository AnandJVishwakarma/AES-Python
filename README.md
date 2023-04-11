# Introduction:

**AES** stands for Advanced Encryption Standard, also known as Rijndael.

AES is an encoding algorithm that transforms plain text data into a version known as ciphertext that’s not possible for humans or machines to understand without an encryption key―a password.

# Symmetric Vs Asymmetric Encryption:

AES is a symmetric-key algorithm, meaning the same key (aka passphrase or password) is used to encrypt and decrypt the data.

Asymmetric methods use a public key for encryption and a secret key for decryption. Anyone can send encrypted messages but only the receiver knows how to decrypt them. TLS certificates used for secure HTTP communication (HTTPS) leverage asymmetric encryption.


**The AES algorithm can be divided into 3 main parts:**

1. Generate a key
2. Generate a cipher
3. Encrypt/decrypt the data with the cipher

## Generating the AES key

AES requires a secret passphrase known as a “key” to encrypt/decrypt data. Anybody with the key can decrypt your data, so you need it to be strong and hidden from everyone―only the software program should be able to access it.

The key can be either 128, 192, 256, or 512 bit long. An AES cipher using a 512-bit key is abbreviated as AES 512, for example. The longer the key, the more secure it is, but the slower the encryption/decryption will be as well. 128 bits is equivalent to 24 characters in base64 encoding, 44 characters for 256 bits. Since storage space isn’t usually a problem and the difference in speed between the versions is negligible, a good rule of thumb is to use 256-bit keys.

## Generating the cipher

The cipher is the algorithm used to perform encryption/decryption. To handle data of arbitrary length and nature, several modes of operation for AES are possible. Each mode changes the general algorithm while offering pros and cons, as we will later read.

## Encrypting/decrypting the data

AES operates on data blocks of 16 bytes represented as 4x4 two-dimensional arrays. The byte content of these matrices is transformed using a mathematical function defined by the cipher (also called block cipher), which gives us a ciphertext―the encrypted result.

Decryption is simply the inverse operation.

Without going into the technical details, the mathematical function guarantees the strength of the encryption, which is why AES is considered unbreakable as long as the properties of the function are respected―strong key, correctly implemented cipher, unique nonce, unique initialization vector, etc.

# AES Implementation in Python Using PyCryptodome

## ECB (Electronic Code Book) mode (AES-ECB)
Each 16-byte block of plaintext is encrypted independently. This mode is not advised as it’s the least secure.

### Pros:
- Simplest

### Cons:
- Weakest cipher
- Padding needed to fit the data into 16-byte blocks

# Reference:
https://onboardbase.com/blog/aes-encryption-decryption/#:~:text=AES%20is%20an%20encoding%20algorithm,
store%20passwords%20in%20a%20database.
