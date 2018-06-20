![Polkadot UI](https://i.imgur.com/bEV7oGv.png)

Polkadot's PoC-1 Testnet already comes with an open-source, publicly accessible interface that makes it easy for anyone to start participating in the development of Polkadot.

## Getting started

If you are already running [your own node](https://github.com/paritytech/polkadot), simply point your (webkit-compatible) browser to: https://polkadot.js.org/apps/ 

If you do not want to set up your own node, just navigate to https://poc-1.polkadot.io/ to create your first Polkadot account.

#### Creating an account

To create a new account, navigate to the `accounts` tab and click the `Create account` button at the top of the page. Choose a name for your account, encrypt it with a strong password, then click `Save`.

#### Getting testnet DOTs

To start playing with your new Polkadot account, head over to our [Riot channel](https://riot.im/app/#/room/#polkadot-watercooler:matrix.org) and ask for some testnet DOTs. 

#### Sending a transaction

Navigate to the `Extrinsics` tab and then select your account with DOTs. Then, enter the address you want to send DOTs to in the `recipient: AccountId` field, enter the amount in `value: Balance`, click `Submit Transaction`, then enter your password and click `Sign and Submit`.

Then you will notice the balance of the recipient automatically increase after a few blocks.

## Navigating around the interface


### Explorer

The Polkadot block explorer lists the validated blocks as they are sealed. Each block listed includes its:

- block/height number

- hash

- parentHash (hash of previous block)

- extrinsics root (merkle root of the ordered mapping of extrinsics, from the index to each extrinsic in the block)

- state root (merkle root of all the storage)

### Extrinsics

![Polkadot Extrinsics](https://i.imgur.com/ShUq0P9.png)

Extrinsics are the bits of information added to the blockchain, i.e., the things 'extrinsic' from the system. These include transactions, staking, proposing, voting, and many other consensus-level actions.

Start by selecting the account you wish to act with, then choose one of the following extrinsics from the extrinsics section.

#### Transferring and staking

From here you can transfer, stake and unstake tokens. 

- **transfer(recipient,value) - Transfer** by entering the recipient's address in the 'recipient field', then entering the number of DOTs you would like to send.

- **stake() - Stake** the entirety of your selected account's balance to become a Validator. However, becoming a Validator requires more than just staking. To get more information on becoming a Validator, read the [requirements and tutorial here](https://github.com/paritytech/polkadot/wiki/Validating-on-PoC-1).

- **unstake() - Unstake** the entirety of your selected account's balance to withdraw your bid to be a Validator.

#### Other functions

As of PoC-1, other options are limited or simply not yet available for testing. This readme will be updated as these functions are made available.

### Storage

The `Storage` tab allows you to query the Polkadot PoC-1 blockchain for statistics and information.

Some useful queries include:
- `consensus.authorityCount()` : the number of current validators
- `session.validators()` : the current validators by address, useful for verifying your status as a validator
- `staking.intentions()` : accounts with the desire to stake and become validators 
- `consensus.code()` : the Wasm code of the current runtime, useful for monitoring changes in the state transition function 
- `system.accountIndexOf(who)` : the number of extrinsics a given account has submitted
- `staking.freeBalanceOfWho()` : the balance of a given account 

### Accounts

This page lists your current Polkadot accounts. You can view, edit or create an account. When creating accounts, it's always good practice to back up the private key under the `create from the following seed ...` field.

### Addresses

You can save addresses here for later use. To add a new address to your address book, click the `Add address` tab at the top

### Vanity

![vanity](https://i.imgur.com/tLwi5kp.png)

Polkadot UI comes with a vanity address generator in the browser. To create a vanity address, enter a string and start hashing. The longer the string, the longer it will take to compute. Note that some characters are not allowed due to the hashing algorithm used in Polkadot.

### Raw RPC

For hackers interested in submitting Raw RPC calls, you can do so from here. These calls will be updated as Polkadot evolves.

### Toolbox

Here you can hash data, verify signed messages, and sign messages from your accounts.