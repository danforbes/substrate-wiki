SS58 is a simple address format designed for Substrate based chains. There's no problem with using other address formats for a chain, but this serves as a robust default. It is heavily based on Bitcoin's Base-58-check format with a few alterations.

The basic idea is a base-58 encoded value which can identify a specific account on the Substrate chain. Different chains have different means of identifying accounts. SS58 is designed to be extensible for this reason.

## Basic Format

The basic format conforms to:

```
base58encode ( concat ( <address-type>, <address>, <checksum> ) )
```

This is, the concatenated byte series of address type, address and checksum then passed into a base-58 encoder. The `base58encode` function is exactly as [defined](https://en.wikipedia.org/wiki/Base58) in Bitcoin and IPFS, using the same alphabet as both.

## Address Type

The `<address-type>` is one or more bytes that describe the precise format of the following bytes.

Currently, there exist six valid values:

- 00000000b (0) Polkadot Live (SS58 checksum preimage)
- 00000001b (1) Polkadot Live (AccountId checksum preimage)
- 00101010b (42) Polkadot PoC-1... testnet (SS58 checksum preimage)
- 00101011b (43) Polkadot PoC-1... testnet (AccountId checksum preimage)
- 01000100b (68) EW Live (SS58 checksum preimage)
- 01000101b (69) EW Live (AccountId checksum preimage)

## Address Formats for Polkadot

There are 8 different address formats, identified by the length (in bytes) of the total payload (i.e. including the checksum).

- 3 bytes: 1 byte account index, 1 byte checksum
- 4 bytes: 2 byte account index, 1 byte checksum
- 5 bytes: 2 byte account index, 2 byte checksum
- 6 bytes: 4 byte account index, 1 byte checksum
- 7 bytes: 4 byte account index, 2 byte checksum
- 10 bytes: 8 byte account index, 1 byte checksum
- 11 bytes: 8 byte account index, 2 byte checksum
- 34 bytes: 32 byte account id, 2 byte checksum

## Checksum types

Four potential checksum strategies exist within Polkadot, giving different length and longevity guarantees. There are two types of checksum preimage (known as SS58 and AccountID) and two different checksum lengths (1 and 2 bytes).

In all cases for Polkadot, the [Blake2-256](https://en.wikipedia.org/wiki/BLAKE_(hash_function)) hash function is used. The variants simply select the preimage used as the input to the hash function and the number of bytes taken from its output.

The bytes used are always either the left most byte ("1 byte checksum") or the two left most bytes ("2 byte checksum"). The input to be used is one of:

- **SS58**: The non-checksum portion of the SS58 byte series used as input to the base-58 function, i.e. `concat( <address-type>, <address> )`.
- **AccountID**: The actual 32-byte account identifier. For externally controlled accounts, this is the public key.
