This page details the message format for Polkadot-based message passing between chains. It describes the formal data format, any environmental data which may be additionally required and the corresponding meaning of the datagrams.

# Background

In Polkadot, three message passing systems use this format: XCMP, HRMP and VMP.

- **XCMP** *Cross-Chain Message Passing* highly scalable message passing between parachains.
- **HRMP** *Horizontal Relay-based Message Passing* message passing between parachains where messages are "manually" relayed by the relay-chain.
- **VMP** *Vertical Message Passing* message passing between the Relay-chain itself to a parachain; for the present purposes, we mean specifically **DMP**, *Downward Message Passing*, where the Relay-chain passes a message down to a parachain.

# Basic Top-level Format

- 4 bytes (magic): `0xff58434d`
- 4 bytes (version code).

Depending on the version code, the rest of the format may be different.

## Version 0

Datagrams of version 0 are such:

- 4 bytes (magic): `0xff58434d`.
- 4 bytes (version code, LE): `0x00000000`.
- 2 bytes (Message type).
- * bytes (Message parameters).

Message type must be one of:

- 0x0000: Native Account Credited [ID32 variant]

# Message Types

## Native Account Credited [ID32 variant]

An *Amount of Funds*, measured in the *Native Currency* of the *Origin Chain* have been credited to the *Sovereign Account* of the *Recipient Chain* and a 32-byte sub-account identifier is provided for the recipient chain to credit within its own context.

### Parameter(s)

- `amount: u128` The *Amount of Funds* that have been credited to the *Sovereign Account* of the *Recipient Chain* on the *Origin Chain*.
- `subaccount: [u8; 32]` A 32-byte account ID which identifies the subaccount to be credited within the context of the *Recipient Chain*.

## Foreign Account Transfer [ID32 variant]

An *Amount of Funds*, measured in the *Native Currency* of the *Recipient Chain* should be transfered from the *Sovereign Account* of the *Origin Chain* to the account controlled by a 32-byte account identifier within the context of the *Recipient Chain*.

### Parameter(s)

- `amount: u128` The *Amount of Funds* that should be transfered from the *Sovereign Account* of the *Origin Chain* on the *Recipient Chain*.
- `destination: [u8; 32]` A 32-byte account ID which identifies the account on the *Recipient Chain* to be credited.

# Definitions

- *Sovereign Account* An account controlled solely and irrevocably by a particular chain.
- *Origin Chain* The chain from which a given message has been delivered. This is always queryable by the receiving code using the message-passing protocol.
- *Recipient Chain* The chain to which a given message has been delivered.