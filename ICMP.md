This page describes Polkadot's Inter-Chain Message Passing technology.

## Description

ICMP is a subset of the Polkadot protocol. It defines the means by which messages can be passed with *no additional trust* between parachains and/or the Relay chain. It relies heavily on Polkadot's Relay chain infrastructure, and is not relevant outside of that context. In particular, ICMP is *not* a message or format standard.

ICMP covers, in terms of consensus, the mechanisms for queueing, relaying and ordering messages. In conjunction with the rest of the Polkadot Relay chain and in particular Grandpa finalisation, it covers data availability. In conjunction with the parachain validation function, message input and output are also covered.

Low-level networking is not covered. Message semantics are not covered.

## Overview

### Validation and Communication

Polkadot "version 1.0" has two key features relating to scalability and sharding. These are namely the ability for many isolated transaction-based state-progression systems (known as parachains) to be crypto-economically secured through a single set of validators assumed only to be mostly rational; and secondly to let these otherwise isolated systems send asynchronous messages between each other with absolute guarantees and in a secure and trust-free manner.

For the present purposes, we define the term "message" in much the same way as "transaction". Both are data coming from outside that chain, and both imply *and require* that the chain act on the data in accordance with its own internal logic. Allowing for some level of delays typical in real-world systems, the chain has *no ability to reject or confound the implications of the data*. This property means that a faulty or malicious miner in Bitcoin cannot redirect funds and is the foundation of good crypto-economic consensus systems. The key difference between a transaction and a message is that whereas a transaction contains a signature to prove the provenance of the data (and thus authority of the instructions), with a message the provenance is proven merely by virtue of Polkadot's internal Byzantine-resistant crypto-economic validation infrastructure, in much the same way as Ethereum's inter-contract message passing. No signatures are involved, and it is for this reason that ICMP is largely devoid of utility outside of Polkadot's very particular environment.

In short, ICMP is not and is unlikely ever to form a "standard" in the typical sense of the word. It is an area of technology that is reliant upon, and proprietary to, Polkadot.

The two features of *shared security* and *trust-free message-passing* are inextricably linked; more specifically, the second directly implies the first. Other systems that attempt to allow one without the other are generally unfit for purpose since, except in the most trivial of circumstances, causality is transitive and thus linking chains with different security guarantees implies that all chains in the graph will suffer the security implications of the weakest.

There are workarounds, for example by avoiding general peer-to-peer connectivity and instead nominating and appointing a central chain with both sufficient logic to comprehend all messages and sufficient state to be able to evaluate their validity. However these requirements result in a system that is inflexible (all message's semantics must be on the hub), unscalable (all global data allowing validation of messages must be on the hub) and doesn't address the fact that by *not* sharing the security resources (i.e. slashable capital) such a system is essentially *dividing-and-conquering* itself!

### Validation

To understand ICMP it is first important to understand the guarantees that Polkadot gives. Polkadot is comprised of one single Relay-chain and a number (we hope around 100) *parachains*. (Parachains may be time-sliced into *parathreads*, but that is out of scope at present.) Parachains are an absolutely simple single state-transition system. All are defined by a pure function:

```
(head_data', messages_out) = V(head_data, header, extrinsics, witness, messages_in)
```

`V` is the validation function; a piece of WebAssembly that checks that the parachain's state-transition is actually valid. It relates a new state of the chain (`head_data'`) and a set of `messages_out` to a digest of the previous state of the chain (`head_data`; this is usually a hash of the parachain's header and maybe a few other important fields), the block data (`header` and `extrinsics` or transactions), and a set of `messages_in` which have been faithfully routed from other parachains or the Relay-chain. `witness` data (such the preimages of trie nodes) is also provided to ensure that verification/validation remains stateless.

The Relay-chain's cryptographic security apparatus (i.e. a subset of the validators, allowing for escalation to all validators in the case of a misbehaviour claim) not only ensures that all finalised transitions of parachains satisfy `V`, but also, crucially, that `messages_in` is a faithful representation of the messages that were sent from other chains in the system, in order.

```
messages_in: Vec<EndPoint, Vec<Vec<u8>>>
messages_out: Vec<EndPoint, Vec<Vec<u8>>>
EndPoint: enum { Para(uint32), Relay }
```

The actual data formats that go into and out of the validation function are trivial: messages are merely a set of pairs of endpoints and blob-vectors. Parachains are identified by a 32-bit integer given to them on registration. Messages destined for the Relay-chain need no further identifier.

### Message relaying

When a parachain has a block validated and its state transitioned ready for finalisation, details of the transition are deposited on the relay-chain. To avoid the relay-chain becoming a bottleneck for both validation and data throughput, specifics relating to the transition itself are kept minimal, with correctness relying on crypto-economic games and cryptographic digests.

Of the three possible endpoint combinations, namely *upward* (parachain to relay-chain), *downward* (relay-chain to parachain) and *lateral* (parachain-to-parachain), only for upward messages does the message payload actually get stored/queued on the relay-chain, taking up state. These messages are processed in a simple round-robin fashion and there are safeguards both on the number and the size of the messages allowed. They are interpreted in almost exactly the same way as the "call data" of transactions, with their origin being either an account identifier representing the parachain on the relay-chain or a special `Parachain` origin, at the choice of the sender (both may be assumed to be absolute indications of the messages origin).

Downward messages (initiated by the relay-chain) are little more than an internal API to allow the relay-chain to inject a message into a parachain's queue directly and are not expected to be used much outside of system-level parachains.

With lateral messages, the relay-chain never touches the payload data, dealing only in terms of hashes of the data. Parachain validators, in their reporting to the relay-chain, attest to a set of hashes of the message output queue, one for each lateral endpoint. If these hashes do not faithfully represent the message data that actually was output from the parachain it can be detected and punished.

In this way, a single hash suffices for each batch of messages sent between each pair of blockchains for each block validated. Thus the relay-chain manages a hash-matrix of order `N**2` entries. In the case of 100 parachains, this matrix could be up to 9,900 hashes, though realistically it will be very sparse and there will be no need for the relay chain to actually store quite so many entries.

This matrix allows future parachain validators to verify that a given `messages_in` queue is valid and by doing so allows Polkadot's shared security to make trust-free guarantees on message delivery.

### Data availability

There is, however, an issue: the availability of the message data. In the above model, the relay-chain only is only able to provide an indication that a later block has a valid `messages_in` field. On its own, it cannot help collators actually construct such a field, since the relay chain doesn't explicitly store the messages. Because of this, if a malicious set of parachain validators attested to a `messages_out` hash that did not actually represent any payload, then a victim parachain's collators would never be able to construct a `messages_in` hash that fulfilled the pre-image requirements. This is an example of the data availability problem.

In Polkadot's ICMP, strict data availability is addressed through requiring the same group of validators that *finalise* the relay-chain blocks to provide guarantees on the availability of all extrinsic and transient data that is not stored on-chain. This includes lateral message payloads, downward message payloads and parachain blocks. Data is stored across the network in an efficient erasure coding format tolerant to 33% of the nodes being offline, and thus providing the a similar relief of failure as the finality algorithm itself, the argument being that if finalisation occurs then it's highly likely that a sufficient number of honest and active nodes exist to reconstruct any data needed for creation of valid future blocks.

It is expected that this form of availability will be needed only as a security safe-guard; honest Polkadot validators and collators will have the data ready for distribution and in normal operation it will be gossiped freely.

More information on this can be found on the [Web3 Foundation's research team pages](research.web3.foundation).

## APIs

The APIs for message passing revolve merely on making the inputs and outputs of the parachain block validation function available to the parachain logic. There are four points of abstraction that these APIs may take place:

- Raw WebAssembly validation function;
- Substrate-based chain using the basic runtime APIs;
- Substrate-based chain using the default message dispatching module;
- Spree module APIs.

The first is useful for those who are interested in building their own parachains from scratch with without reference to Substrate. In this case other languages (AssemblyScript, C++ or anything that can be targeted to WebAssembly) may be used. It takes the form of an entry point in WebAssembly with offsets and sizes relating to encoded items of the block including the messages in. Further, solid information will be published here once ready.

The second is useful for those who wish to build a parachain on Substrate but who wish to interpret the byte messages coming from parachains directly. This allows arbitrary formats of messages to be supported, but does mean that any actions to be done on receipt of messages must be coded manually.

Though still under development, it will likely take the form of a single type provided as a generic argument to an element of the SRML/Cumulus API responsible for interpreting blocks (quite possibly referenced from the executive module).

The trait will look something like:

```
trait HandleIncoming {
  fn handle_incoming(sender: EndPoint, messages: &[Vec<u8>]);
}
enum EndPoint { Relay, Para(ParaId) }
```

Other options include one-call-per-message and one-call-per-block models, or perhaps having the option of all three with default implementations. This would be called at block initialisation.

As an alternative to this trait-based system, the API may make it possible to simply inspected the incoming messages at any point during block execution. In this case, the parachain's SRML runtime would likely implement a trait:

```
trait WithIncoming {
  fn with_incoming(sender: EndPoint, f: impl FnOnce(&[Vec<u8>]));
}
```

Once Cumulus is finalised, this will be updated to the actual API.

The third API abstraction level is easiest and requires little work on the part of the chain. It piggy-backs on Substrate's `Origin` API and existing transaction-processing system in order to interpret in much the same way as transactions/extrinsics. Messages are subject to the same fee payment, weight and and validity checks as transactions and are dispatched in the same way. The main difference is that they are not (or may not TBD) be dispatched with a `Signed` origin, but rather with a `Para` or `Relay` origin, allowing the dispatchable functions of all modules of the parachain to easily verify their provenance and/or remain forwards compatible.

Finally, Spree, an innovation allowing trustless forward-guarantees on the semantics of messages between parachains, will allow for interoperation of the parachains through symmetrical APIs. These APIs have attached logic, protected from the rest of the parachain's operation, which is able to send and receive messages. It has its own logical endpoint, distinct that of the rest of the parachain and other Spree modules. In this way, it opaquely governs the actual message content sent between chains. Spree modules are an asset shared across the Relay-chain, and may only be upgraded all-at-once. Because Spree modules are guaranteed to only ever communication with other Spree modules of the same type and version, we can avoiding the need for any fixed communications standards and can make absolute guarantees over the implications of the messages sent, regardless of changes and upgrades to the parachain itself.