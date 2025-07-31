# VSB

# Authenticated File Encryption with ChaCha20-Poly1305

### Description

- A lightweight, platform-independent C++ tool that performs file-level authenticated encryption using the ChaCha20-Poly1305 AEAD cipher, via the secure libsodium cryptographic library
- Designed for high-speed, secure file transfer (e.g., to USB sticks), this tool ensures that your files remain confidential and tamper-proof across devices and networks

---

## NOTICE

- Please read through this `README.md` to better understand the project's source code and setup instructions
- Also, make sure to review the contents of the `License/` directory
- Your attention to these details is appreciated — enjoy exploring the project!

---

## Problem Statement

- With sensitive file transfers (i.e. custom-built USB storage devices), there's a growing need for fast, modern, and cross-platform encryption tools that avoid outdated ciphers and insecure practices
- Many tools lack authenticated encryption or improperly manage cryptographic material, leaving users vulnerable to corruption or tampering

---

## Project Goals

### Provide Strong, Efficient File Encryption Using Modern AEAD Ciphers

- Utilize ChaCha20-Poly1305 to provide confidentiality and integrity for local files

### Simplify Secure Encryption Workflows in C++

- Deliver an easy-to-integrate and extensible C++ solution using `libsodium` with minimal dependencies

---

## Tools, Materials & Resources

### libsodium

- Cryptographic library that provides safe and modern APIs for AEAD, key management, and randomness

### C++ Source Files

- `encrypt.cpp`, `decrypt.cpp`, and `main.cpp` for testing end-to-end functionality

### [libsodium Documentation](https://download.libsodium.org/doc/)

---

## Design Decision

### Use Authenticated Encryption (AEAD)

- ChaCha20-Poly1305 ensures both confidentiality and integrity — encryption and tamper detection are unified

### Stream Files in Chunks

- Reading and encrypting/decrypting files in 4KB blocks ensures scalability for large files without excess memory usage

### Associated Data Binding

- Adds metadata (like file attributes or headers) as "associated data" to the encryption process, ensuring end-to-end authenticity

---

## Features

### File Encryption with AEAD

- Encrypts file content using `crypto_aead_chacha20poly1305_ietf_encrypt`, writing it securely to a `.bin` file

### File Decryption with Integrity Verification

- Decrypts and verifies encrypted files using the paired key and nonce, rejecting tampered data automatically

### Easy CLI Integration & Makefile Support

- Can be compiled into CLI tools (`encrypt`, `decrypt`) or integrated into larger C++ applications

---

## Block Diagram

```plaintext
                                     ┌────────────────────┐
                                     │   User-Supplied    │
                                     │   plaintext file   │
                                     └─────────┬──────────┘
                                               ↓
                                    ┌──────────▼──────────┐
                                    │  Chunked File Reader│
                                    └──────────┬──────────┘
                                               ↓
                                    ┌──────────▼──────────┐
                                    │ libsodium AEAD API  │
                                    │╔═══════════════════╗│
                                    │║ ChaCha20-Poly1305 ║│
                                    │╚═══════════════════╝│
                                    └──────────┬──────────┘
                                               ↓
                                     ┌─────────▼─────────┐
                                     │ Encrypted Output  │
                                     │     (binary)      │
                                     └───────────────────┘

```

---

## Functional Overview

- encrypt.cpp reads an input file in chunks, encrypts it using ChaCha20-Poly1305 with optional associated data, and writes to an output file
- decrypt.cpp performs the reverse: authenticates and decrypts the file using the same key/nonce pair and associated data
- The `main()` function showcases the full cycle: encryption followed by decryption of the same file

---

## Challenges & Solutions

### Secure Key and Nonce Management

- Used `libsodium`'s `randombytes_buf()` to securely generate keys and nonces, emphasizing not hardcoding them in production

### Integrity Validation on Decryption

- ChaCha20-Poly1305’s AEAD design automatically verifies ciphertext integrity using Poly1305 tags — invalid files fail decryption gracefully

---

## Lessons Learned

### Importance of Nonce Uniqueness

- Reusing a nonce with the same key can compromise security
- Each encrypted file must use a fresh, random nonce

### Simplicity with libsodium

- `libsodium` abstracts cryptographic complexity with secure defaults, making it ideal for embedding encryption into C++ projects without error-prone boilerplate

---

## Project Structure

```plaintext
root/
├── License/
│   ├── LICENSE.md
│   │
│   └── NOTICE.md
│
├── .gitattributes
│
├── .gitignore
│
└── README.md

```

---

## Future Enhancements

- Automatically prepend or append the nonce to the encrypted file to simplify decryption
- Support for deriving keys from passwords using `crypto_pwhash()` and salts
- Bind additional file metadata (timestamps, permissions) as AEAD associated data
- Add formal test suite to validate encryption/decryption consistency across platforms.
- Provide cross-platform builds via GitHub Actions or CMake
