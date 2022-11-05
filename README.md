# Peacash - The Technical Paper

**Abstract.** A lightweight cryptocurrency that can handle thousands of tps (transactions per second) is required to meet future demands. Reducing transaction sizes increases the networks throughput. Reducing the size of the blockchain also lowers the barrier of entry for resource constrained devices.

## 1. Introduction

As of writing Bitcoin has a limit of 7 tps. There is consistently a demand to make more than 7 tps which results in full blocks which in turn leads to increased transaction fees.

## 2. Proof-of-Stake

To keep a distributed ledger over a peer-to-peer network a decentralized regulatory system is required. One problem that arises is to limit of blocks added to the blockchain over time. To solve this problem, cryptocurrencies like Bitcoin uses a proof-of-work model where massive amounts of electricity is burned in order to vote, "one-CPU-one-vote".

Peacash solves this problem through proof-of-stake which uses the currency itself. A validator can choose to stake some of their coins in order to be able to vote. If the validator misbehaves their stake is burned.

Peacash uses a queue based system to determine the next forger. The queue consists of validators which stake atleast 1 coin and will rotate a step each time a new block is forged. Since the queue is the "shape of a loop" this process will continue in an endless cycle. As soon as the validators stake exceeds the minimum amount, they are placed in the back of the queue. And as soon the validators stake is less than the minimum to enter they are immediately removed from the queue. Once a validator gets removed from the queue their position is lost.

In order to be granted "voteing permissions" a validator must stake atleast 1 coin, or at most 100 coins.

## 3. Network

The steps to run the network are as follows:

1) New transactions are broadcast to all validators.
2) Each validator keeps track of pending transactions.
3) The validator first in queue collects pending transactions into a block.
4) The validator cryptographically signs the block and broadcasts it to all validators.
5) Validators accept the block only if it was received within a time limit.
6) Validators express their acceptence of the block by using the hash of the accepted block as the previous hash when it's their turn to forge a new block.

Validators always consider the chain with the largest combined sum of stake at the moment to be the correct one.

If a validator disconnects from the network it will experience other validators fail to show up in time which results in burned stake. Once the validator reconnects it will compare chains and choose the correct one with the largest combined sum of stake at the moment.

## 4. Incentive

To motivate stakers, a reward is given out each time a new block is forged. The reward is exponentially proportional to the stake. Reward is derived from the stake amount using the following formula:

$$reward(stake)=2^{stake/10^{-9}}/100-1$$

## 5. Reclaiming Disk Space

Blocks can be discarded once buried deep in the chain to save disk space. Peacash keeps track of balances separately.

## 6. Rust

Reasons to choose Rust:

* Rust emphasizes performance.
* Rust's *borrow checker* is ideal for cryptocurrency development.
* It is a loved language which continues to grow.
* As many people as possible should be able to understand the source code of a cryptocurrency.

## 7. Blocks

Peacash blocks are atleast 152 bytes in size and includes following data:

```rust
previous_hash: [u8; 32]
timestamp: u64
public_key: [u8; 32]
signature: [u8; 64]
transactions: Vec<Transaction>
stakes: Vec<Stake>
```

## 8. Transactions

Peacash transactions are 152 bytes in size and includes following data:

```rust
public_key_input: [u8; 32]
public_key_output: [u8; 32]
amount: u64
fee: u64
timestamp: u64
signature: [u8; 64]
```

## 9. Stakes

Peacash stakes are 120 bytes in size and includes following data:

```rust
public_key: [u8; 32]
amount: u64
fee: u64
timestamp: u64
signature: [u8; 64]
```

## 10. Privacy

Peacash's main objective is to achieve efficiency and performance. Peacash does not implement any privacy enhancing features like [Monero (XMR)](https://www.getmonero.org/) does becuase it comes with the cost of performance.

The blockchain is transparent. All data stored on the blockchain including every transaction, can be viewed by anyone, at any time, anywhere in the world.

## 11. Wallet

Encryption is used in order to secure the private key of the wallet.

Encryption model:
Passphrase -> Argon2id -> ChaCha20-Poly1305 -> Private key.

## References

1) [Rust (programming language)](https://en.wikipedia.org/wiki/Rust_(programming_language))
2) [Bincode](https://github.com/bincode-org/bincode)
