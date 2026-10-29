### Practical Transaction Verification: Bitcoin Transaction

**Transaction ID:**  
`4277ec3046f2a134bcb46bca9b8b4a4c9f7b564c5919f54c7aaf653f1d3c1c4d`

**Explorer:** [Mempool.space](https://mempool.space/tx/4277ec3046f2a134bcb46bca9b8b4a4c9f7b564c5919f54c7aaf653f1d3c1c4d)

During my practical investigation, I inspected the transaction's confirmation count, input and output values, witness data, sequence number, previous output script, and output script types.

The explorer showed 282 confirmations at the time of my observation.

#### Observed Transaction Details

- **Input value:** 91.12610722 BTC
- **Output 1:** 1.00000000 BTC
- **Output 2:** 90.12568400 BTC
- **Transaction fee:** 42,322 satoshis
- **Input type:** V0_P2WPKH
- **Output types:** V0_P2WPKH
- **Input sequence:** `0xfffffffd`

#### Manual Fee Verification

Total output value:

`1.00000000 + 90.12568400 = 91.12568400 BTC`

Transaction fee:

`91.12610722 - 91.12568400 = 0.00042322 BTC`

The calculated fee equals 42,322 satoshis, matching the value displayed by the explorer.

#### Verification Concepts

This exercise helped me understand the difference between a transaction's displayed confirmation status and independent transaction validation by a Bitcoin node.

The witness data contains a digital signature and a public key, while the previous output script defines the spending conditions that must be satisfied. However, inspecting these fields alone does not independently verify the signature or every applicable consensus rule.

The sequence value `0xfffffffd` also demonstrates how transaction fields can signal replaceability under BIP125-style rules while a transaction remains unconfirmed.

**Scope limitation:** I examined the transaction through a public blockchain explorer. I did not independently execute the full Bitcoin consensus validation process or verify the signature using a Bitcoin full node.

**Key learning:** Transaction verification involves more than checking input and output amounts. It also includes validating spending conditions and signatures, checking that inputs are available, enforcing transaction rules, and determining whether the transaction has been included in an accepted block.

