All polkadot actors are identified by their account.
If you don´t have an account, you can make one using https://poc-2.polkadot.io/#/

## Nominators

You may have an account with DOTs and want to earn fresh DOTs. You could do so as validator, which requires a node running 24/7. If you do not have such node or do not want to bother, you can still earn DOTs by nominating another validator.

Doing so, you become a nominator for the validator of your choice. Pick your validator carefully, if they do not behave properly, they will get slashed and you will lose DOTs as well.

While your DOTs are staked by nominating a validator, they are 'locked'. You can receive new DOTs in your account but you cannot stake as validator or transfer DOTs away from your account. You can un-nominate at any time to unlock your funds. Keep in mind that the un-nomination is effective at the next Era. Currently that means you may need to wait up to ~1 hour before the change takes place. 

## Validators

These participants will play a crucial role in adding new blocks to the Relay Chain and, by extension to all parachains, such that parties can complete cross-chain transactions via the Relay Chain. Validators perform two functions. First, verifying the information contained in an assigned set of parachain blocks is valid (such as the identities of the transacting parties and the subject matter of the contract). Their second role is to participate in the consensus mechanism to produce the Relay Chain blocks based on validity statements from other validators. Any instances of non-compliance with the consensus algorithms result in punishment by removal of some or all of the validator’s staked DOTs, thereby discouraging bad actors. Good performance, however, will be rewarded, with validators receiving transaction fees in the form of DOTs in exchange for their activities.

## Collators

These participants will sit atop parachains and provide proofs to validators based on transactions from parachains. Collators maintain parachains by aggregating parachain transactions into parachain blocks and producing state transition proofs for validators based on those blocks. They also monitor the network and prove bad behaviour to validators. Collators maintain a “full-node” for a particular parachain; meaning they retain all necessary information to be able to author new blocks and execute transactions in much the same way as miners do on current PoW blockchains. Under normal circumstances, they will collate and execute transactions to create an unsealed block and provide it, together with a zero-knowledge proof, to one or more validators responsible for proposing a parachain block.

## Fishermen

These participants are not engaged in the process of validating transactions in the same way as validators or nominators, but rather they deter bad actors by monitoring activity across the platform to determine whether any other participants have acted in breach of the rules. Fisherman will be required to provide significantly fewer DOTs by way of a security bond but can receive proportionately larger DOT rewards (in comparison to rewards received by validators and nominators) in proportion to the size of their security bond.