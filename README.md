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

# AES Implementation in Python Using PyCryptodome:

- ECB (Electronic Code Book) mode (AES-ECB)
- CBC (Cipher Block Chaining) mode (AES-CBC)
- CFB (Cipher FeedBack) mode (AES-CFB)
- OFB (Output FeedBack) mode (AES-OFB)
- CTR (Counter) mode (AES-CTR)
- GCM (Galois Counter Mode) mode (AES-GCM)
- EAX (Encrypt-then-authenticate-then-translate) mode (EAX)


## ECB (Electronic Code Book) mode (AES-ECB)
Each 16-byte block of plaintext is encrypted independently. This mode is not advised as it’s the least secure.

### Pros:
- Simplest

### Cons:
- Weakest cipher
- Padding needed to fit the data into 16-byte blocks

```python
cipher = AES.new(key, AES.MODE_ECB)
```

## CBC (Cipher Block Chaining) mode (AES-CBC)

Each plaintext block gets XOR-ed with the previous ciphertext block prior to encryption. An initialization vector is used to make sure each encryption has a different ciphertext result.

### Pros:

Each ciphertext is different, so it’s not possible to know if two hashed data strings originate from the same plain text
### Cons:

Padding needed to fit the data into 16-byte blocks
An error in one plain text block will affect all the following blocks
Vulnerable to attacks like padding oracle attacks, chosen-plaintext attacks, and chosen-ciphertext attacks if the initialization vector isn’t changed for every encryption
Since you need to know the previous block to encrypt the next one, you can’t parallelize the operations, which results in a slower algorithm

```python
cipher = AES.new(key, AES.MODE_CBC)
cipher_text = cipher.encrypt(pad(data, AES.block_size))
iv = cipher.iv

decrypt_cipher = AES.new(key, AES.MODE_CBC, iv)
plain_text = decrypt_cipher.decrypt(cipher_text)
```

## CFB (Cipher FeedBack) mode (AES-CFB)
Turns the block cipher into a stream cipher. Each content byte is XOR-ed with a byte taken from a keystream. The keystream is obtained by encrypting the last ciphertext produced with the block cipher.

### Pros:

A stream cipher accepts data of any length (i.e. padding is not needed)
### Cons:

Corrupted data cannot be recovered as each ciphertext relies on the other to be decrypted
Since you need to know the previous block to encrypt the next one, you can’t parallelize the operations, which results in a slower algorithm

```python
cipher = AES.new(key, AES.MODE_CFB)
cipher_text = cipher.encrypt(data)
iv = cipher.iv

decrypt_cipher = AES.new(key, AES.MODE_CFB, iv=iv)
plain_text = decrypt_cipher.decrypt(cipher_text)
```

## OFB (Output FeedBack) mode (AES-OFB)
Turns the block cipher into a stream cipher. Each content byte is XOR-ed with a byte taken from a keystream. The keystream is obtained by recursively encrypting the Initialization Vector.

### Pros:

A stream cipher accepts data of any length (i.e. padding is not needed)
### Cons:

Corrupted data cannot be recovered as you need the correct initialization vector to decrypt the whole thing.
Cannot be parallelized (slower)

```python
cipher = AES.new(key, AES.MODE_OFB)
cipher_text = cipher.encrypt(data)
iv = cipher.iv

decrypt_cipher = AES.new(key, AES.MODE_OFB, iv=iv)
plain_text = decrypt_cipher.decrypt(cipher_text)
```

## CTR (Counter) mode (AES-CTR)
Turns the block cipher into a stream cipher. Each content byte is XOR-ed with a byte taken from a keystream. The keystream is generated by encrypting a sequence of counter blocks with ECB.

### Pros:

Accepts data of any length (i.e. padding is not needed)
Each counter block can be encrypted separately. Parallelization makes the algorithm faster.
### Cons:

Corrupted data cannot be recovered
Need a different IV for each message to be truly secure

```python
cipher = AES.new(key, AES.MODE_CTR)
cipher_text = cipher.encrypt(data)
nonce = cipher.nonce

decrypt_cipher = AES.new(key, AES.MODE_CTR, nonce=nonce)
plain_text = decrypt_cipher.decrypt(cipher_text)
```

## GCM (Galois Counter Mode) mode (AES-GCM)
A combination of Counter mode (CTR) and Authentication. Uses a Message Authentication Code (MAC) for authentication.

### Pros:

Guarantees integrity (to establish if the ciphertext was modified in transit or if it really originates from a certain source)
Accepts pipelined and parallelized implementations and have a minimal computational footprint
### Cons:

Complex implementation, hard to code yourself from scratch

```python
header = b"header"

#Encryption
cipher = AES.new(key, AES.MODE_GCM)
cipher.update(header)

cipher_text, tag = cipher.encrypt_and_digest(data)
nonce = cipher.nonce

#Decryption
decrypt_cipher = AES.new(key, AES.MODE_GCM, nonce=nonce)
decrypt_cipher.update(header)

plain_text = decrypt_cipher.decrypt_and_verify(cipher_text, tag)
```

## EAX (Encrypt-then-authenticate-then-translate) mode (EAX)
Another method for Authenticated Encryption with AES.

### Pros:

Detects any unauthorized modification
Simpler than GCM to implement
### Cons:

Slower than GCM
```python

header = b"header"

#Encryption
cipher = AES.new(key, AES.MODE_EAX)
cipher.update(header)

cipher_text, tag = cipher.encrypt_and_digest(data)
nonce = cipher.nonce

#Decryption
decrypt_cipher = AES.new(key, AES.MODE_EAX, nonce=nonce)
decrypt_cipher.update(header)

plain_text = decrypt_cipher.decrypt_and_verify(cipher_text, tag)
```

# Reference:
https://onboardbase.com/blog/aes-encryption-decryption/#:~:text=AES%20is%20an%20encoding%20algorithm,
store%20passwords%20in%20a%20database.
