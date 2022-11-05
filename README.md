# The Technical Paper

**Abstract.** A lightweight cryptocurrency that can handle thousands of tps (transactions per second) is required to meet future demands. Reducing transaction sizes increases the networks throughput. Reducing the size of the blockchain also lowers the barrier of entry for resource constrained devices.

## 1. Introduction

As of writing Bitcoin has a limit of 7 tps. There is consistently a demand to make more than 7 tps which results in full blocks which in turn leads to increased transaction fees.

Energy is a finite resource. Proof-of-work is an energy intensive task. A way to achieve consensus amongst a decentralized network without wasting as much electricity would improve upon the already existing proof-of-work model.

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

To motivate validators, a reward is given out each time a new block is forged. The reward is exponentially proportional to the stake. Reward is derived from the stake amount using the following formula:

$$reward(stake)=2^{stake/10^{-9}}/100-1$$

The reward also include fees collected from all the transactions and stakes in the block.

## 5. Node

Nodes are the upkeepers of the network.
They are computers that connect to each other in a peer-to-peer network and share the distributed ledger also known as the blockchain.
They relay new blocks and transactions to each other.
They run software that implements the Peacash protocol.
Nodes can join and leave the network at any time.
Every node keeps a local copy of the blockchain in `./peacash/db`.

## 6. Validator

Validators are nodes that have deposited a stake.
Validator nodes extend the blockchain on a set time interval.
They forge new blocks for a reward.
The reward consists of collected fees as well as a set amount that is proportional to how much the validator is currently staking.
Being a validator comes with the responsibility of showing up when it's time to forge a new block.
If a validator fails to show up when it's their turn to forge a block they are punished by having their stake burned.

## 7. Wallet

Peacash uses `Ed25519` to sign transactions.
To protect the `private key` of the wallet is encrypted using a passphrase configured by the user, a cryptographic `salt` and a `nonce` value.
The key derivation function (KDF) used to derive a key from the passphrase is `Argon2id`.
The symmetric-key algorithm used to encrypt the private key is `ChaCha20-Poly1305`.
Wallet files end with the extension `.pea` and are stored locally in `./peacash/wallets/`.

## 8. Privacy

Peacash's main objective is to achieve efficiency and performance. Peacash does not implement any privacy enhancing features like [Monero (XMR)](https://www.getmonero.org/) does becuase it comes with the cost of performance.

The blockchain is transparent. All data stored on the blockchain including every transaction, can be viewed by anyone, at any time, anywhere in the world.
