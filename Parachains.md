# Parachain Deployment and Querying

## View Parachains

https://poc-2.polkadot.io/#/storage > parachains > activeParachains()

## Create Parachain

Obtain WebAssembly code for Basic Adder Example by rphmeier: 

Download from https://github.com/paritytech/polkadot/blob/4af260fea194d807a48f63dbd4b4595d4be91744/polkadot/parachain/tests/res/adder.wasm (possibly outdated)

or look in the `polkadot/parachain/tests/res/` folder on master.

Run the adder-collator binary with arguments to connect it to validators in the network, which also runs the relay chain. It will help if you have validators' node IDs to connect to.

```rust
cargo run -p adder-collator -- ARGS_GO_HERE
```

## Deploy Parachain

### Start Referendum
Go to https://poc-2.polkadot.io/#/extrinsics

Select Account to deploy it

Either:

democracy > propose(proposal, value) > democracy > startReferendum(proposal, voteThreshold) > parachains > registerParachain(id, code, headerData), drag-and-drop / upload the compiled WASM binary from Rob or Adrian to Parachain Code, enter headData, Super majority approval, balance/deposit: 100,000

OR

democracy > propose(proposal, value) > parachains > registerParachain(id, code, headerData), drag-and-drop / upload the compiled WASM binary from Rob or Adrian to Parachain Code, enter headData, ...

Note: View Minimum Deposit that's required at https://poc-2.polkadot.io/#/storage > democracy > minimumDeposit() > click "+"

NOTE: Remember the REFERENDUM_ID

### Wait until REFERENDUM_ID is a Proposed Referendum (aka Proposed Referenda)
View Referendum Info / Count
https://poc-2.polkadot.io/#/storage > democracy > referendumInfoOf(referendum) > enter REFERENDUM_ID > click "+" (then just wait for it to appear)
https://poc-2.polkadot.io/#/storage > democracy > referendumCount() >click "+""

### Vote on the REFERENDUM_ID in the Proposed Referenda (before it expires)
View Voting Period at https://poc-2.polkadot.io/#/storage > democracy > votingPeriod() > click "+"

### Wait unit REFERENDUM_ID is an Active Referendum (aka Launched Referenda on the Table of Referenda)

### Vote on the REFERENDUM_ID in the Active Referenda (before it expires)
View Launch Period at https://poc-2.polkadot.io/#/storage > democracy > launchPeriod() > click "+"
Alternatively view the list of "proposedReferenda" or "activeReferenda" with Polka-UI https://github.com/paritytech/polka-ui or http://polkadash.io/

### Confirmation that Parachain Successfully Deployed
View Polkadot Node logs until it says Super majority approval
Send transaction and query the Parachain
Basic Adder (Test) Parachain is too simple for transactions to be executed against it, as it's a minimal test parachain for adding a number.
References:

Above summary has been built by @luke based on notes I've taken by following comments made by @rphmeier, @jaco, @adrianbrink in #polkadot-watercooler


You can read more about Parachains at https://github.com/w3f/Web3-wiki/wiki/Parachains

### TODO
* Update to incorporate comments in discussion between @jacogr and @miketang https://matrix.to/#/!fOOzymDEHiIIUtmlBE:matrix.org/$1537192171559673aeHoK:matrix.org

### Links
* Opportunities to build parachains - https://github.com/w3f/Web3-collaboration/issues/11