To become a staked validator on PoC-1, you'll need two things:

- Enough DOTs for staking (head to [the watercooler](https://riot.im/app/#/room/#polkadot-watercooler:matrix.org) and ask for some if you don't have any).
- A computer, running v0.1 of Polkadot that has a fast, stable internet connection and that is permanently on & online.

### First, set up your machine

In addition to any usual CLI parameters that you run `polkadot` with, you'll need to add an extra two:

- `--validator`: This tells your node to try to validate when it sees itself as a listed authority on the network;
- `--key <SEED>`: This provides your node with a secret key that lets it know whether it's a listed authority on the network, and, if it is, lets it sign statements to authorise the creation of new blocks.

The `<SEED>` that you pass is important; make sure it's randomly chosen and don't forget it; you'll need it in the next step.

Since `polkadot` will need to be running 24/7, if you are setting this up on a remote host, make sure that `polkadot` won't die when your connection to that host dies. The easiest way to do this is to run it in a `screen` session. You can find instructions on that on the internet.

### Second, apply to validate

New validators are chosen every 24 hours or so. Right now, there are 12 slots for validators and they are selected as the top 12 staked accounts. You'll need to create a new account (Accounts -> Create Account) in the polkadot UI (https://polkadot.js.org/ or https://poc-1.polkadot.io/ depending in whether you have a local node running) **using the same <SEED> as before**. This account represents the node you just set up. Call it something like "My Validator Node".

Next is to give it some DOTs. Right now, the least staked validator on PoC-1 has 9800 DOTs, so it'll need to have more than that to get chosen. (If you don't have that many, then ask around - maybe you can scrounge some off other people by convincing them you'll do a great job as validator.) Once your account has a decent amount of DOTs in, then you can register it to be staked. Go to Extrinsics, ensure you have "My Validator Node" selected at the top and then "staking" and "stake()" selected underneath it. Hit "Submit Extrinsic" and you're done.

### Third, wait

Every 24 hours or so ("or so" because PoC-1 has pretty ropey validators already which slows down block production), a new validator set is chosen by the system. To keep an eye on this there are two pieces of data on-chain that you can check via the UI; "intentions" and "validators". The latter is the current set of validators. The former is the accounts that are down to become validator next time.

To see one or both, head to "Storage" in the UI and select "staking" and "intentions()" and hit "+". For validators, select "session" and "validators()" before hitting "+".

You should check that your address is among them (note it won't appear as a `<SEED>` but rather a normal "SS58" address - it'll be the same series of letters and numbers that begins with 5 as you'll see next to/underneath "My Validator Node").


