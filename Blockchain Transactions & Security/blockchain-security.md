# Basic Blockchain Security: A Practical Bitcoin Investigation

## 1. Introduction

As part of my Blockchain and Web3 Technologies Internship, I studied fundamental blockchain security concepts and explored how they relate to real Bitcoin transactions.

For the practical part of this exercise, I used [Mempool.space](https://mempool.space/) to inspect Bitcoin transaction records, follow the movement of BTC between transaction outputs, examine address balances and UTXO information, and compare transaction inputs with outputs.

The investigation helped me understand the role of UTXOs in preventing double spending, the significance of transaction confirmations, the security properties of Bitcoin's blockchain structure, and the limitations of analyzing public blockchain data.

**Learning objectives:**
- Understand the concept of double spending and how Bitcoin prevents the same UTXO from being spent twice in the accepted chain.
- Learn how block confirmations contribute to transaction security.
- Understand how cryptographic hashes and proof of work help protect blockchain history.
- Identify the importance of private key protection.
- Explore blockchain transparency and the limitations of address-based analysis.

## 2. Double Spending and the UTXO Model

### 2.1 What Is Double Spending?

Double spending is an attempt to spend the same digital asset more than once.

Bitcoin addresses this problem through its transaction and UTXO model. Every transaction input references a specific output from a previous transaction. Once that output is spent in the accepted blockchain, it cannot be spent again by another transaction on the same accepted chain.

A conflicting transaction may attempt to reference the same previous output, but the network cannot accept both conflicting spends as valid transactions in the same chain.

The rules enforced by Bitcoin nodes, together with the network's consensus mechanism, help prevent the same output from being spent twice.

### 2.2 Practical Investigation: Tracing a Spent Output

I examined the following two transactions:

**Transaction A:**  
[View transaction `7db06a66...`](https://mempool.space/tx/7db06a66e81f97df16cd294ddf30801217de09e06c1fa9f17a43d608e08aabf8)

**Transaction B:**  
[View transaction `4277ec30...`](https://mempool.space/tx/4277ec3046f2a134bcb46bca9b8b4a4c9f7b564c5919f54c7aaf653f1d3c1c4d)

Transaction A created an output of `91.12610722 BTC` to the address:

[`bc1q08j86yjjechk2d6svdz6s6qyav0spz5hz60z80`](https://mempool.space/address/bc1q08j86yjjechk2d6svdz6s6qyav0spz5hz60z80)

Transaction B subsequently used that output as its input.

The input value was distributed into two new outputs:

| Output | Amount |
|---|---:|
| Output 1 | 1.00000000 BTC |
| Output 2 | 90.12568400 BTC |
| Total outputs | 91.12568400 BTC |
| Transaction fee | 0.00042322 BTC |

At the time I inspected the address page, it showed a confirmed balance of `0 BTC` and zero confirmed UTXOs.

**Practical finding:** The previously created output of `91.12610722 BTC` was consumed by the subsequent transaction. This illustrates the UTXO lifecycle: an output is created, remains available for spending until used, and ceases to be an unspent output after it is spent.

This observation demonstrates how UTXOs are tracked through transactions. It does not mean that I performed a live double-spending attack or independently tested conflicting transactions.

## 3. Transaction Confirmations and Security

### 3.1 What Are Confirmations?

A transaction may be broadcast to the Bitcoin network without immediately being included in a block. Such a transaction is commonly described as unconfirmed.

When a miner includes a transaction in a block that is accepted by the network, the transaction receives its first confirmation. Each subsequently accepted block built on top of that block adds another confirmation.

Confirmations increase confidence that a transaction will remain in the accepted blockchain history, although they do not provide an absolute guarantee against reorganizations.

| Transaction state | Security significance |
|---|---|
| Unconfirmed | The transaction has not yet been included in an accepted block. |
| 1 confirmation | The transaction has been included in a block. |
| Multiple confirmations | Additional blocks provide greater protection against a reversal of the transaction's place in the chain. |

The appropriate confirmation threshold depends on the payment amount, the potential consequences of a reversal, and the risk tolerance of the recipient.

### 3.2 Practical Connection

During my investigation, I followed transaction relationships using Mempool.space and inspected address pages to distinguish outputs that had been spent from those that remained unspent at the time of inspection.

This helped me understand that an address balance and a transaction's confirmation status represent different types of information:

- A confirmed balance indicates the value represented by currently unspent outputs associated with an address.
- A transaction confirmation indicates that the transaction has been included in a block.
- An unspent output can remain available for a future transaction even after its creating transaction has been confirmed.

**Important limitation:** Examining an explorer's confirmation count provides evidence about the displayed blockchain status. It is not equivalent to independently validating the transaction using a Bitcoin full node.

## 4. Blockchain Immutability and Proof of Work

### 4.1 How Is Blockchain History Protected?

Bitcoin's blockchain links blocks together using cryptographic hashes. Each block header contains the hash of the previous block, creating a chain of references.

If a transaction in an earlier block were modified, the transaction's identifier and the block's transaction commitment would no longer match the original data. Reconstructing the affected block would require producing a valid proof of work, and subsequent blocks would also need to be reconciled with the altered history.

Bitcoin nodes follow consensus rules and select the valid chain with the greatest accumulated proof of work, subject to those rules.

This makes rewriting sufficiently buried blockchain history computationally difficult. However, immutability should not be interpreted as an absolute guarantee that reorganizations can never occur.

### 4.2 Connection to My Investigation

I followed the output of `91.20454646 BTC` from one transaction into a later transaction and then traced a subsequent output through two additional transactions.

The observed relationship was:

1. Transaction `c96f857a...` created an output of `91.20454646 BTC`.
2. Transaction `8047131e...` spent that output along with another input.
3. Transaction `8047131e...` created an output of `91.19552169 BTC`.
4. Transaction `7db06a66...` spent that output and created an output of `91.12610722 BTC`.
5. Transaction `4277ec30...` spent the latter output and created two new outputs.

This sequence demonstrated how specific transaction outputs connect successive transactions. It also illustrated why transaction history and input-output relationships are important when analyzing blockchain activity.

The exercise did not involve modifying a block or testing a blockchain reorganization. It was an observational investigation of recorded transactions.

## 5. Private Key Security

A Bitcoin private key is a critical security credential. It is used to create digital signatures that authorize spending under the conditions defined by the relevant locking scripts.

If an attacker obtains a private key or a wallet's recovery phrase, the attacker may be able to authorize unauthorized transactions.

Important security practices include:

- Never share private keys or recovery phrases.
- Never include private keys or recovery phrases in GitHub repositories, screenshots, or publicly accessible files.
- Keep wallet software and the devices used to access it appropriately secured and updated.
- Maintain secure wallet backups and understand how recovery works.
- Consider suitable offline or hardware-based signing arrangements when protecting significant holdings.

During this investigation, I used publicly available transaction data. I did not need access to any wallet's private keys or recovery phrase.

**Security finding:** Public blockchain data can reveal transaction activity without exposing the private key required to authorize future spending.

## 6. Blockchain Transparency and Privacy

Bitcoin transactions are publicly recorded. Transaction IDs, output amounts, addresses, and transaction relationships can be examined through public blockchain explorers.

This transparency supports transaction tracing and independent examination of recorded activity. However, a Bitcoin address is not automatically equivalent to a known real-world identity.

For example, two transactions may be linked because an output from one transaction is used as an input in another. This establishes a technical relationship between the transactions, but it does not independently prove that their addresses belong to the same person or organization.

Similarly, the amount and structure of an output are not sufficient to prove that it represents change returned to a sender.

### Practical Observations

During the investigation, I observed that:

- Different outputs can be sent to the same address.
- A single transaction can consume multiple separate UTXOs.
- Outputs with identical amounts can still be distinct outputs created by different transactions.
- Address balances and UTXO counts can help identify whether outputs remain unspent at the time of inspection.
- Transaction relationships do not, by themselves, establish the identity of an address owner or the purpose of a transfer.

These observations are relevant to blockchain investigations because they help distinguish directly observable evidence from assumptions that require additional support.

## 7. Summary of Practical Findings

| Security concept | Practical evidence or learning |
|---|---|
| Double spending | Traced an output and observed its subsequent consumption as a transaction input. |
| UTXO security | Learned how spent outputs cease to be available as unspent outputs. |
| Confirmations | Studied the difference between a transaction being included in a block and the confidence provided by additional confirmations. |
| Immutability | Learned how hash-linked blocks and proof of work make historical modification difficult. |
| Private key security | Identified why signatures can be examined publicly without revealing the private key. |
| Privacy limitations | Distinguished transaction relationships from claims about real-world ownership or intent. |

## 8. Scope and Limitations

This exercise was conducted through public transaction and address information displayed by Mempool.space.

The practical work consisted of examining transaction details, tracing UTXOs, inspecting address balances, and interpreting the resulting relationships.

I did not conduct a double-spending attack, recover or inspect any private keys, run a Bitcoin full node, or independently verify every transaction signature and consensus rule.

The analysis therefore demonstrates practical blockchain exploration and foundational security concepts rather than a complete security audit or independent validation of the Bitcoin network.

Address balances and UTXO observations are time-sensitive and reflect the information displayed during the investigation. The current state may differ if further transactions have occurred.

## 9. Conclusion

Through this exercise, I developed a practical understanding of several fundamental Bitcoin security concepts.

By tracing outputs across multiple transactions, I learned how the UTXO model prevents the same output from being spent twice in the accepted chain. I also studied how confirmations increase confidence in transaction history, how proof of work helps protect previously accepted blocks, and why the security of private keys is essential.

Finally, the investigation demonstrated that publicly available blockchain data can reveal transaction relationships while remaining insufficient, on its own, to establish real-world ownership or the intent behind a transfer.

This work provides a foundation for further study of transaction validation, consensus rules, digital signatures, and blockchain investigation techniques.

## 10. References

- [Bitcoin Developer Guide — Transactions](https://developer.bitcoin.org/devguide/transactions.html)
- [Bitcoin Developer Guide — Block Chain](https://developer.bitcoin.org/devguide/block_chain.html)
- [Bitcoin.org — Securing Your Wallet](https://bitcoin.org/en/secure-your-wallet)
- [Bitcoin.org — Important Information About Bitcoin](https://bitcoin.org/en/you-need-to-know)
- [Mempool.space — Bitcoin Explorer](https://mempool.space/)

