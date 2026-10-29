# Hashing and Digital Signatures in Bitcoin



## 1. Introduction

As part of my Blockchain and Web3 Technologies Internship, I explored how cryptographic hashing and digital signatures are used in Bitcoin transactions.

For this practical exercise, I examined the raw transaction data of a real Bitcoin transaction using Mempool.space.

Transaction ID:
4277ec3046f2a134bcb46bca9b8b4a4c9f7b564c5919f54c7aaf653f1d3c1c4d

[View transaction on Mempool.space](https://mempool.space/tx/4277ec3046f2a134bcb46bca9b8b4a4c9f7b564c5919f54c7aaf653f1d3c1c4d)

## 2. Cryptographic Hashing

A cryptographic hash function transforms input data into a fixed-length output. Bitcoin uses double SHA-256 hashing to calculate the transaction ID (TXID) from a transaction's base serialization.

For SegWit transactions, the TXID is calculated without the witness data. The witness-inclusive serialization is used to calculate the separate witness transaction ID (WTXID).

Practical Observation

The raw transaction data I examined was 222 bytes long. After removing the SegWit marker, flag, and witness data to obtain the base serialization, the resulting data was 113 bytes long.

The TXID calculated from this base serialization matched the transaction ID displayed by Mempool.space.

This demonstrated the relationship between transaction serialization and transaction identification.

## 3. Digital Signatures

Digital signatures provide a cryptographic mechanism for authorizing the spending of Bitcoin outputs.

A private key is used to create a signature, while the corresponding public key is used to verify it. Bitcoin nodes evaluate signatures alongside the relevant scripts and transaction rules.

Practical Observation

The raw transaction contained two witness items for its input:

- A 71-byte signature item, including the signature hash type.

- A 33-byte compressed public key.

The signature item began with 3044, consistent with a DER-encoded ECDSA signature, and ended with 01, indicating the SIGHASH_ALL hash type for this signature.

These observations helped me identify where signature-related data appears in a serialized SegWit transaction.

The presence of signature data alone does not prove that the signature is valid. Independent cryptographic verification and the relevant Bitcoin script rules are necessary to establish validity.

## 4. Key Learnings

Through this exercise, I learned that:

- TXID is a transaction identifier derived from transaction data using double SHA-256.

- SegWit transactions distinguish between TXID and WTXID.

- Digital signatures and hashes serve different purposes.

- Public keys can be included in transaction witness data, depending on the spending method.

- Signature verification helps establish authorization to spend an output, but does not independently establish a person's real-world identity.

## 5. References

[Bitcoin Developer Guide](https://developer.bitcoin.org/devguide/transactions.html) — Transactions

[Bitcoin Developer Reference](https://developer.bitcoin.org/reference/) — Transaction Format

[Mempool.space](https://mempool.space/) — Bitcoin Explorer
