This page describes Polkadot's Inter-Chain Message Passing technology.

## Description

ICMP is a subset of the Polkadot protocol. It defines the means by which messages can be passed with *no additional trust* between parachains and/or the Relay chain. It relies heavily on Polkadot's Relay chain infrastructure, and is not relevant outside of that context. In particular, ICMP is *not* a message or format standard.

ICMP covers, in terms of consensus, the mechanisms for queueing, relaying and ordering messages. In conjunction with the rest of the Polkadot Relay chain and in particular Grandpa finalisation, it covers data availability. In conjunction with the parachain validation function, message input and output are also covered.

Low-level networking is not covered. Message semantics are not covered.

## Overview

### Validation and Communication

Polkadot "version 1.0" has two key features relating to scalability and sharding. These are namely the ability for many isolated transaction-based state-progression systems (known as parachains) to be crypto-economically secured through a single set of validators assumed only to be mostly rational; and secondly to let these otherwise isolated systems send asynchronous messages between each other with absolute guarantees and in a secure and trust-free manner.

For the present purposes, we define the term "message" in much the same way as "transaction". Both are data coming from outside that chain, and both imply *and require* that the chain act on the data in accordance with its own internal logic. Allowing for some level of delays typical in real-world systems, the chain has *no ability to reject or confound the implications of the data*. This property means that a faulty or malicious miner in Bitcoin cannot redirect funds and is the foundation of good crypto-economic consensus systems. The key difference between a transaction and a message is that whereas a transaction contains a signature to prove the provenance of the data (and thus authority of the instructions), with a message the provenance is proven merely by virtue of Polkadot's internal Byzantine-resistant crypto-economic validation infrastructure, in much the same way as Ethereum's message. No signatures are involved, and it is for this reason that ICMP is largely devoid if utility outside of Polkadot's very particular environment.

In short, ICMP is not and is unlikely ever to form a "standard" in the typical sense of the word. It is an area of technology that is reliant upon, and proprietary to, Polkadot.

The two features of *shared security* and *trust-free message-passing* are inextricably linked; more specifically, the second directly implies the first. Other systems that attempt to allow one without the other are generally unfit for purpose since, except in the most trivial of circumstances, causally linking chains with different security guarantees generally implies that all chains in the graph will suffer the security implications of the weakest.

There are workarounds, for example by avoiding general peer-to-peer connectivity and instead nominating and appointing a central chain with both sufficient logic and security guarantees for the specific semantics required. However this is inflexible, unscalable and doesn't address the fact that by *not* sharing the security resources (i.e. slashable capital) such a system is essentially *dividing-and-conquering* its own resources.

### Validation

To understand ICMP it is first important to understand the guarantees that Polkadot gives. Polkadot is comprised of one single Relay-chain and a number (we hope around 100) *parachains*. (Parachains may be time-sliced into *parathreads*, but that is out of scope at present.) Parachains are an absolutely simple single state-transition system. All are defined by a pure function:

```
V(head_data', head_data, extrinsics, witness, messages_in, messages_out)
```

`V` is the validation function; a piece of WebAssembly that checks that the parachain's state-transition is actually valid. It accepts a digest of the previous state of the chain `head_data` (this is either the parachain's header or a hash of it), data from the external world `extrinsics` (these are typically just transactions), and the messages into and out of the chain that are to be routed to either other parachains or the Relay-chain.

The Relay-chain's cryptographic security apparatus (i.e. a subset of the validators, allowing for escalation to all validators in the case of a misbehaviour claim) ensures that `V` is always true for all finalised parachain blocks.

```
messages_in: Vec<EndPoint, Vec<Vec<u8>>>
messages_out: Vec<EndPoint, Vec<Vec<u8>>>
EndPoint: enum { Para(uint32), Relay }
```

The actual data formats that go into and out of the validation function are trivial: messages are merely a set of pairs of endpoints and blob-vectors. Parachains are identified by a 32-bit integer given to them on registration. Messages destined for the Relay-chain need no further identifier.

## APIs

Parachains
