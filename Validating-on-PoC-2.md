This guide follows [Validating on PoC-1](https://github.com/paritytech/polkadot/wiki/Validating-on-PoC-1).
The following information has been updated to match version [0.2.1](https://github.com/paritytech/polkadot/releases/tag/v0.2.1), released August 31st 2018.

# Introduction

Being a validator on the Polkadot network is simple but there are a few caveats and you need to make it right to ensure your validator will behave itself and earn you [DOT](https://github.com/paritytech/polkadot/wiki/DOT)s.

# Motivation

First of all, validators are the heart of the network: they validate new blocks in a similar way Bitcoin miners create new blocks on the Bitcoin blockchain. Polkadot however is not based on Proof Of Work. Validators do not need high performance computers but they need to met a few conditions.

As a validator, you may earn testnet [DOT](https://github.com/paritytech/polkadot/wiki/DOT)s when following the rules and keeping your node up and running. For this reason, the choice of the computer running your node is important and you may prefer a hosted VM to your home computer.

# Risk

If you are elected validator and don´t behave properly (node not online/reachable, wrong software version, etc...), your node´s balance will be slashed: you will lose testnet DOTs.

# Setting a validator node in a nutshell

The next chapter goes into more details but here is the recipe.
It is advised to do the following in the right order to avoid issues:
- install an appropriate polkadot version
- start it without any flag until your node is synced
- create an account using the [Polkadot UI](https://github.com/paritytech/polkadot/wiki/Polkadot-UI), save your seed and take note of your account´s address
- get testnet [DOT](https://github.com/paritytech/polkadot/wiki/DOT)s in your account
- restart your polkadot client with `polkadot --validator --key <seed>`
- check that your authority key matches
- `Extrinsics/staking/stake()` your account
- check that you show up in the `Storage/staking/intentions()` list
- wait until you show up in the `Storage/session/validators()` list
- observe your balance going hopefully up!

# Validator Requirements

In order to be a validator you need the following:
- A node running a proper version of the polkadot client
- A node started with both `--validator` and `--key ...`. More on that below
- Some testnet [DOT](https://github.com/paritytech/polkadot/wiki/DOT)s
- Having staked

You can find more information regarding the installation of polkadot in the readme:
- install using cargo: https://github.com/paritytech/polkadot/blob/master/README.adoc#1-to-play
- using docker: https://github.com/paritytech/polkadot/blob/master/README.adoc#4-using-docker

## Proper version

At the time of writing the cutting edge latest version is *not* appropriate and should *not* be used by validators. Only version 0.2.1 works.

## --validator

That´s easy, just run polakdot with `--validator`.

## --key

That can be a little tricky due to [#474](https://github.com/paritytech/polkadot/issues/474).

First open the UI at https://poc-2.polkadot.io/#/accounts.

Do NOT use the generated seed, use a string, preferable between 32 and 60 chars.
Save this seed and take note of your account´s address.

You can now start your node using `polkadot --validator --key <your seed here>`.

When polkadot starts, it will confirm and display the address of the validator´s account.
Look for a log similar to `Using authority key 5CFch3.....` when polkadot starts. This MUST match the address of your validator´s account. If this is not the case, do not stake your account, you will get slashed.

## Some testnet DOTs

In order for your validator account to be granted the right to validate, you will need testnet [DOT](https://github.com/paritytech/polkadot/wiki/DOT)s in that account, enough in regards to the other validator candidates.

You may ask for testnet [DOT](https://github.com/paritytech/polkadot/wiki/DOT)s on Riot in #polkadot-watercooler:matrix.org).

## Staking

In order for your validator to effectively run, you will need to stake your account.
You can do so [here](https://poc-2.polkadot.io/#/extrinsics). Select your account, category `staking`, extrinsic `stake()` and send the transaction.

If you did it right, you should see your account´s address showing in the intensions list. You find the list [here](https://poc-2.polkadot.io/#/storage) under `staking/intensions()`.

## Free slot

You are now almost good to go. You may be elected validator if there are enough free slots and your stake (balance) is higher than the other validator candidates.

Currently, there are 5 slots available. The dev team is operating 4 validators with high stake. That leaves one slot free for community validators. The number of available slots will likely vary over time. To check the current value, use the UI at `Storage/staking/validatorCount()`.


