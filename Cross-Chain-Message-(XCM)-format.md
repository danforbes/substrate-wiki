This page details the message format for Polkadot-based message passing between chains. It describes the formal data format, any environmental data which may be additionally required and the corresponding meaning of the datagrams.

# Background

In Polkadot, three message passing systems use this format: XCMP, HRMP and VMP.

- **XCMP** *Cross-Chain Message Passing* highly scalable message passing between parachains with minimal work on the side of the Relay-chain.
- **VMP** *Vertical Message Passing* message passing between the Relay-chain itself and a parachain.
- **HRMP** *Horizontal Relay-based Message Passing* message passing, similar in effect to XCMP (messages between parachains) but implemented using VMP so messages are passed through the Relay-chain.

# Basic Top-level Format

All data is SCALE encoded.

- `magic: [u8; 2] = 0xff00`: Prefix identifier.
- `version: u16 = 0u16`: Version of XCM; only zero supported currently.
- `type: u16`: Message type.
- `payload`: Message parameters.

For version 0, message `type` must be one of:

- `0u16`: Native Account Credited
- `1u16`: Foreign Account Transfer

# Message Types

## Native Account Credited

An *Amount of Funds*, measured in the *Native Currency* of the *Origin Chain* have been credited to the *Sovereign Account* of the *Recipient Chain* and a sub-account identifier is provided for the recipient chain to credit within its own context.

### Parameter(s)

- `amount: Compact<u256>` The *Amount of Funds* that have been credited to the *Sovereign Account* of the *Recipient Chain* on the *Origin Chain*.
- `destination: DestId` A universal destination ID which identifies the sub-account to be credited within the context of the *Recipient Chain*.

## Foreign Account Transfer

An *Amount of Funds*, measured in the *Native Currency* of the *Recipient Chain* should be transfered from the *Sovereign Account* of the *Origin Chain* to the account controlled by a 32-byte account identifier within the context of the *Recipient Chain*.

### Parameter(s)

- `amount: Compact<u256>` The *Amount of Funds* that should be transfered from the *Sovereign Account* of the *Origin Chain* on the *Recipient Chain*.
- `destination: DestId` A universal destination identifier which identifies the account/owner/controller on the *Recipient Chain* to be credited.

# Definitions

- *Sovereign Account* An account controlled solely and irrevocably by a particular chain.
- *Origin Chain* The chain from which a given message has been delivered. This is always queryable by the receiving code using the message-passing protocol.
- *Recipient Chain* The chain to which a given message has been delivered.

# `DestId`: Universal Destination Identifiers

Destination identifiers are self-describing identifiers that can specify an owner into whose control some cryptographic asset be placed. It aims to be sufficiently abstract in meaning and general in nature that it works across a variety of chain types, including UTXO, account-based and privacy-preserving.

## Basic format

- `version: u8 = 0x00`: Version code, currently there's only the first version, zero.
- `type: Compact<u32>`: The type of destination.
- `payload`: The data payload, format determined by the `type`.

## Type 0: 32-byte Substrate Frame `MultiSigner` Account ID

This is a generic 32-byte multi-crypto Account ID, as used across Polkadot, Kusama, Westend and various other Substrate-based chains. It corresponds directly to the public key of either the S/R 25519 or the Ed25519 signature schemes, or the Blake2-256 hash of the 33 byte compact ECDSA public key. Depending on the exact nature of the chain, several other account mechanisms (see e.g. the `utility`, `multisig` or `proxy` pallets) may be in place for account authentication.

- `id: [u8; 32]` The 32 byte Account ID.

## Type 1: Variable-sized Substrate Frame Account Index

The Substrate Frame framework includes the `indices` pallet allowing accounts to be refered to through a short-form index. This refers to the account whose index is provided.

- `index: Compact<u64>` The 64-bit account index.