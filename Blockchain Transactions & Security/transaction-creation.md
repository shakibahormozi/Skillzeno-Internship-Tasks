# How Bitcoin Transactions Are Created: A Practical Investigation

## 1. Introduction

As part of my Blockchain and Web3 Technologies Internship, I studied how Bitcoin transactions are created, structured, and processed by the blockchain network.

To connect the theoretical concepts with real blockchain activity, I conducted a practical investigation using Mempool.space, a public Bitcoin blockchain explorer.

During this exercise, I examined real Bitcoin transactions, identified their inputs and outputs, traced UTXOs across multiple transactions, inspected address balances, and manually calculated transaction fees. I used the transaction IDs and linked address pages to reconstruct how specific Bitcoin outputs were spent and replaced by new outputs.

This project focuses on understanding the structure and lifecycle of Bitcoin transactions, with particular attention to the Unspent Transaction Output (UTXO) model.

## 2. Learning Objectives

The main objectives of this practical exercise were to:

- Understand the basic structure of a Bitcoin transaction.

- Learn how wallets use existing UTXOs to construct new transactions.

- Understand the relationship between transaction inputs, outputs, and fees.

- Observe how a previously created output becomes an input in a subsequent transaction.

- Explore how transaction and address information can be examined using a blockchain explorer.

- Understand the difference between transaction creation, network validation, and block confirmation.


## 3. How a Bitcoin Transaction Is Created

Bitcoin uses the UTXO model rather than a conventional account-balance model. A transaction spends existing outputs and creates new outputs.

A wallet constructs the transaction using available UTXOs that satisfy the conditions required for spending.

The transaction creation lifecycle can be summarized in the following steps.

**Step 1: Selecting Inputs**

The wallet identifies one or more available UTXOs that can fund the transfer.

Each input references a specific output from a previous transaction. This reference includes the previous transaction ID and the index of the output being spent.

A transaction may consume multiple UTXOs if a single output does not provide enough value for the intended payment and fee.

**Step 2: Defining Outputs**

The sender specifies the destination and the amount to transfer.

Each output contains an amount denominated in satoshis and a locking script (scriptPubKey) that defines the conditions required to spend that output in the future.

A transaction can create multiple outputs. For example, a wallet may create one output for the recipient and another output returning the remaining funds to a change address controlled by the sender.

However, a transaction's output amounts alone are not sufficient to determine which output is a change output. Additional evidence may be required.

**Step 3: Calculating the Transaction Fee**

Bitcoin transaction fees are generally determined by the difference between the total input value and the total output value.

**Transaction fee = Total input value − Total output value**

For example, if a transaction consumes 1.00000000 BTC and creates outputs totaling 0.99950000 BTC, its fee is:

1.00000000 − 0.99950000 = 0.00050000 BTC

Since one BTC equals 100,000,000 satoshis, the fee in this example is 50,000 satoshis.

Wallets also consider the transaction's virtual size and prevailing network fee rates when selecting an appropriate fee.

**Step 4: Signing the Transaction**

After constructing the transaction, the wallet produces the required cryptographic signatures using the appropriate private keys.

The signatures provide authorization to spend the referenced outputs, subject to the conditions defined by their locking scripts.

Depending on the output type, Bitcoin transactions may use different signature mechanisms. For example, traditional and SegWit v0 spending commonly use ECDSA signatures, while Taproot supports Schnorr signatures.

Private keys are used to produce signatures and should never be shared when inspecting public blockchain transactions.

**Step 5: Serializing and Broadcasting**

The transaction is encoded into Bitcoin's serialized transaction format.

The wallet then broadcasts the signed transaction to connected Bitcoin nodes. Nodes may relay valid transactions to other peers, allowing them to propagate across the network.

At this stage, a transaction can remain unconfirmed while waiting to be included in a block.

**Step 6: Network Validation and Block Inclusion**

Bitcoin nodes independently check transactions against applicable transaction and consensus rules. These checks include input availability, spending conditions, signatures, and transaction value constraints.

Miners may include valid transactions in blocks. Once a transaction is included in a block accepted by the network, it receives its first confirmation. Additional blocks provide further confirmations.

These steps describe the general transaction lifecycle. In the practical investigation below, I examined transactions already recorded on the public blockchain rather than creating and broadcasting a new transaction myself.

## 4. Practical Investigation Using Mempool.space

**4.1 Tools and Methodology**

Tool used: [Mempool.space](https://mempool.space/) — Bitcoin Explorer

**I followed a practical, transaction-by-transaction approach:**

1. Opened individual transaction pages and examined the displayed input and output values.

2. Identified outputs sent to a particular address.

3. Followed the relevant transaction IDs to determine whether those outputs were later spent.

4. Inspected address pages to examine confirmed balances, total received amounts, and UTXO counts.

5. Manually calculated transaction fees and compared the results with the explorer's displayed values.

6. Recorded the transaction relationships and distinguished observed facts from assumptions about address ownership.

The investigation covered five transaction IDs.

**4.2 Transaction 1 — Identifying an Initial Output**

Transaction ID: [6ed1b20abed8f5f48c5c3466e8afdc672c065cbb332948e51c69e112509f836f](https://mempool.space/tx/6ed1b20abed8f5f48c5c3466e8afdc672c065cbb332948e51c69e112509f836f)

The explorer displayed the following transaction details:

## Bitcoin Transaction Analysis

| Component | Amount |
|:---|---:|
| Total Input | 114.07649756 BTC |
| Output to `bc1q2w54p8aqj7y8anphrn76rq9g7z7qk89xaayjc4` | 0.06059370 BTC |
| Output to `bc1qrfwdht448d0367jykecxjprglhz5u58kp76exl` | 114.01529756 BTC |
| **Total Outputs** | **114.07589126 BTC** |
| **Transaction Fee** | **0.00060630 BTC** |

Manual verification:

114.07649756 − 114.07589126 = 0.00060630 BTC

The difference equals 60,630 satoshis, matching the fee displayed by the explorer.

Observation: This transaction created an output of 0.06059370 BTC to the address bc1q2w54p8aqj7y8anphrn76rq9g7z7qk89xaayjc4. I later traced this output into another transaction.

**4.3 Transaction 2 — Identifying a Second Output to the Same Address**

Transaction ID: [c96f857ab4bc14e1d55352b7b504ffc26d97010368c5abb3f6b1bf10360547ff](https://mempool.space/tx/c96f857ab4bc14e1d55352b7b504ffc26d97010368c5abb3f6b1bf10360547ff)

This transaction had two inputs:

### Bitcoin Transaction Inputs

| Input | Amount |
|:---|---:|
| Input 1 | 0.00001000 BTC |
| Input 2 | 91.20529756 BTC |
| **Total Inputs** | **91.20530756 BTC** |

The transaction created an output of 91.20454646 BTC to the same address identified in Transaction 1.


### Bitcoin Transaction Summary

| Component | Amount |
|:---|---:|
| Total Inputs | 91.20530756 BTC |
| Total Outputs | 91.20454646 BTC |
| **Transaction Fee** | **0.00076110 BTC** |

Manual verification:

91.20530756 − 91.20454646 = 0.00076110 BTC

The result equals 76,110 satoshis, matching the explorer's displayed fee.

Observation: The address bc1q2w54p8aqj7y8anphrn76rq9g7z7qk89xaayjc4 received two independent outputs:

- 0.06059370 BTC from Transaction 1.

- 91.20454646 BTC from Transaction 2.

Although both outputs were sent to the same address, they were created by separate transactions and represented distinct UTXOs.

**4.4 Transaction 3 — Spending Multiple UTXOs in One Transaction**

Transaction ID: [8047131e1df98252bc076a51fda9239d2fed56dba390fd0847f791dd59ade251](https://mempool.space/tx/8047131e1df98252bc076a51fda9239d2fed56dba390fd0847f791dd59ade251)

This transaction consumed the two outputs identified in the previous investigation steps.

### Bitcoin Transaction Inputs

| Input | Amount |
|:---|---:|
| Output originating from Transaction 1 | 0.06059370 BTC |
| Output originating from Transaction 2 | 91.20454646 BTC |
| **Total Inputs** | **91.26514016 BTC** |

The transaction created two new outputs:

### Bitcoin Transaction Outputs

| Output | Amount |
|:---|---:|
| Output to `bc1qrqtczuh8a8p9uwxjl0ca9h4wjlckqjgvc5a0yu` | 0.06899147 BTC |
| Output to `bc1q6qf6xe4uthx0y8g7lglwv4244gxuy8mq7qyly2` | 91.19552169 BTC |
| **Total Outputs** | **91.26451316 BTC** |

**Manual verification:**

91.26514016 − 91.26451316 = 0.00062700 BTC

The calculated fee is 62,700 satoshis, matching the explorer's displayed fee.

Key observation: This transaction demonstrated that multiple existing UTXOs can be consumed together as inputs to create new outputs. The two inputs were separate outputs, even though they had previously been sent to the same address.

## 4.5 Transaction 4 — Following an Output Into a Subsequent Transaction

Transaction ID: 7db06a66e81f97df16cd294ddf30801217de09e06c1fa9f17a43d608e08aabf8

This transaction spent the 91.19552169 BTC output created by Transaction 3.

### Bitcoin Transaction Summary

| Component | Amount |
|:---|---:|
| Total Input | 91.19552169 BTC |
| Output 1 | 0.06899147 BTC |
| Output 2 | 91.12610722 BTC |
| **Total Outputs** | **91.19509869 BTC** |
| **Transaction Fee** | **0.00042300 BTC** |

**Manual verification:**

91.19552169 − 91.19509869 = 0.00042300 BTC

The calculated fee equals 42,300 satoshis, matching the explorer.

I also inspected the related address pages:

[bc1qs8s7gymf6canrthwa9whtx9a6mfjq9q93ugwfq](https://mempool.space/address/bc1qs8s7gymf6canrthwa9whtx9a6mfjq9q93ugwfq) displayed a confirmed balance of 0.06899147 BTC and one confirmed UTXO.

[bc1q08j86yjjechk2d6svdz6s6qyav0spz5hz60z80](https://mempool.space/address/bc1q08j86yjjechk2d6svdz6s6qyav0spz5hz60z80) later displayed a zero confirmed balance and zero confirmed UTXOs after its received output was spent in Transaction 5.

These address observations helped me distinguish an output that remained unspent from one that had subsequently been consumed by another transaction.

## 4.6 Transaction 5 — Following the Larger Output

Transaction ID: [4277ec3046f2a134bcb46bca9b8b4a4c9f7b564c5919f54c7aaf653f1d3c1c4d](https://mempool.space/tx/4277ec3046f2a134bcb46bca9b8b4a4c9f7b564c5919f54c7aaf653f1d3c1c4d)

This transaction consumed the 91.12610722 BTC output from Transaction 4.


### Bitcoin Transaction Summary

| Component | Amount |
|:---|---:|
| Total Input | 91.12610722 BTC |
| Output to `bc1qegqked6rtuqqy4va6lgp66c2nran78jtmugswp` | 1.00000000 BTC |
| Output to `bc1qnct2d0kwsmca2eesxgk04c9ek756xd56trr2jv` | 90.12568400 BTC |
| **Total Outputs** | **91.12568400 BTC** |
| **Transaction Fee** | **0.00042322 BTC** |


**Manual verification:**

91.12610722 − 91.12568400 = 0.00042322 BTC

The calculated fee equals 42,322 satoshis, matching the displayed value.

At the time I inspected the corresponding address pages, both addresses showed one confirmed UTXO and a confirmed balance equal to the amount received:

[Address receiving 1 BTC](https://mempool.space/address/bc1qegqked6rtuqqy4va6lgp66c2nran78jtmugswp)

[Address receiving 90.125684 BTC](https://mempool.space/address/bc1qnct2d0kwsmca2eesxgk04c9ek756xd56trr2jv)

These observations indicated that both outputs were still unspent at the time of inspection.

## 5. Reconstructed Transaction Flow

By following transaction IDs and their associated outputs, I reconstructed the following sequence:

Transaction 1: 6ed1b20a...
    |
    |-- 0.06059370 BTC
    v
Address: bc1q2w54...
    ^
    |-- 91.20454646 BTC
Transaction 2: c96f857a...
    |
    v
Transaction 3: 8047131e...
    Inputs:
      - 0.06059370 BTC
      - 91.20454646 BTC

    Outputs:
      - 0.06899147 BTC
      - 91.19552169 BTC
                    |
                    v
Transaction 4: 7db06a66...
    Input:
      - 91.19552169 BTC

    Outputs:
      - 0.06899147 BTC
      - 91.12610722 BTC
                    |
                    v
Transaction 5: 4277ec30...
    Input:
      - 91.12610722 BTC

    Outputs:
      - 1.00000000 BTC
      - 90.12568400 BTC

This reconstruction illustrates how an output from an earlier transaction can become an input to a later transaction, producing new outputs that may themselves be spent in subsequent transactions.

The explorer displayed the same timestamp for Transactions 2 and 3. This does not necessarily mean that the transactions were created or broadcast at exactly the same moment. Explorer timestamps generally reflect block-level timestamps, and multiple transactions in the same block can share a timestamp. The transaction references and output relationships provide the stronger evidence for the sequence described above.

6. Manual Transaction Fee Verification

I independently calculated the fees for all five transactions and compared the results with the values displayed by Mempool.space.

Transaction

Total inputs (BTC)

Total outputs (BTC)

Fee (satoshis)

6ed1b20a...

114.07649756

114.07589126

60,630

c96f857a...

91.20530756

91.20454646

76,110

8047131e...

91.26514016

91.26451316

62,700

7db06a66...

91.19552169

91.19509869

42,300

4277ec30...

91.12610722

91.12568400

42,322

For every transaction, the difference between the total input value and the total output value matched the displayed fee.

This exercise helped me understand that the transaction fee is the difference between inputs and outputs, rather than a conventional payment that necessarily appears as a separate output to a fee address.

7. What I Learned

Through this practical investigation, I developed a better understanding of the following concepts:

Transaction inputs and outputs: Inputs reference specific outputs from previous transactions, while outputs create new spending conditions for future transactions.

UTXO lifecycle: An output remains unspent until it is referenced and consumed by a subsequent transaction. Once spent, it is no longer part of the unspent transaction output set.

Multiple inputs: A single transaction can consume several independent UTXOs at once.

Transaction fees: The fee can be calculated manually by subtracting total outputs from total inputs.

Transaction tracing: Following transaction IDs and their referenced outputs makes it possible to reconstruct the movement of bitcoin between linked transactions.

Address inspection: Confirmed balances, total received values, and UTXO counts provide useful evidence about the state of an address at the time of inspection.

Evidence-based analysis: Blockchain data reveals transaction relationships, but it does not automatically reveal the real-world identity of an address owner or the intent behind a transfer.

8. Scope and Limitations

This exercise involved analyzing existing Bitcoin transactions available through a public blockchain explorer. I did not create, sign, or broadcast a new transaction as part of this particular practical investigation.

The results demonstrate hands-on transaction tracing, UTXO analysis, and fee calculations. They do not constitute independent cryptographic validation of transaction signatures or a claim that I operated a full Bitcoin node.

Address ownership and the purpose of a transfer cannot be established from transaction amounts and address relationships alone. Similarly, identifying an output as a change output requires additional supporting evidence.

Address balances and unspent-output observations in this report reflect the information displayed during the investigation and may change as new transactions are confirmed.

9. Conclusion

This practical investigation helped me connect the theoretical structure of Bitcoin transactions with real blockchain data.

By examining five transactions and tracing the associated UTXOs, I observed how outputs created in earlier transactions can be spent as inputs in subsequent transactions. I also verified transaction fees using manual calculations and examined address-level information to determine whether specific outputs remained unspent at the time of inspection.

The exercise provided a practical foundation for further learning in blockchain transaction construction, hashing, digital signatures, transaction validation, and blockchain security.

10. References

Mempool.space — Bitcoin Explorer

Bitcoin Developer Guide — Transactions

Bitcoin Developer Reference — Transaction Format

Bitcoin.org — How Bitcoin Works
