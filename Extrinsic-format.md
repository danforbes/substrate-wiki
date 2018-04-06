## Extrinsic format aka Transaction format

Extrinsics are a bare binary format. All numerics are in LE format. It is the concatenation of these fields:

- 4 bytes: 32-bit length of the extrinsic data, *not including* these 4 bytes.
- 32 bytes: 256-bit Ed25519 public key of the sender.
- 8 bytes: 64-bit transaction index of the sender.
- At least 2 bytes: Runtime call end-point descriptor. See the runtime for more information.
- 64 bytes: 512-bit Ed25519 signature over the previous *3* fields, signed by the sender. Note that the 4-byte length field is not included.

Example:

```
0x
..000000
d172a74cda4c865912c32ba0a80a57ae69abae410e5ccb59dee84e2f4432db4f
0000000000000000
0200
d7568e5f0a7eda67a82691ff379ac4bba4f9c9b859fe779b5d46363b61ad2db9
2a00000000000000
................................................................................................................................