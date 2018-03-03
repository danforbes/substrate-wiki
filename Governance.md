Polkadot uses a sophisticated governance mechanism that allows it to evolve gracefully over time at the ultimate behest of its stakeholders.

In order to do this, we bring together various novel mechanisms, including an amorphous state-transition function stored on-chain and defined in a platform-neutral intermediate language (i.e. WebAssembly) and several on-chain voting mechanisms such as referenda with adaptive super-majority thresholds and batch approval voting.

## Overview

The governance system of Polkadot is founded wholly around the idea of stakeholder voting. A key and unfailing rule is:

**All changes to the protocol must be agreed upon by stake-weighted referendum; 50% of stake will always command the network**

On the face of it, this may seem somewhat restrictive: forcing a stakeholder-based process to do something as little as, say, nudging the block time down by 5%, seems overkill. However, without this rule the network would likely be unstable. By placing the network outside of the hands of stakeholders, it would create a misalignment that would lead to inaction or worse.

Despite this, by altering the meaning of the abstention votes (arising from stakeholders that don't make an explicit vote) we can effect different outcomes depending on the circumstances, crafting a balance of power between active and passive stakeholders.

### Council

To represent passive stakeholders, we introduce the idea of a "council". The council is an on-chain entity comprising a number of actors (each represented by an account). It has a fixed number of seats and all members have a fixed term. Each of the members is elected through an approval vote.

There is no particular voting period for a seat but rather a continuous election process. All stakeholders are free to signal their approval (or not) of any of the registered candidates. As a seat becomes free, a tally is taken to find the most approved candidate, by total stake, and the seat becomes theirs. Members can be removed early only by a referendum.

After an election happens, all unsuccessful candidates except for a number of runners-up are removed along with their approvals. Runners-up get to stay on (along with their approvals) ready for the next election.

The council is called upon for one main task of governance: proposing sensible referenda. For a referendum to be proposed by the council, a strict majority of members must be in favour, with no member exercising a veto. Vetos may be exercised only once by a member for any single proposal; if, after a cool-down period, the proposal is resubmitted, they may not veto it a second time.

In the case that all members vote in favour, the vote is considered unanimous and is treated as uncontroversial.

### Referenda

Referenda are simple, inclusive, stake-based voting schemes. Each referendum has a specific proposal associated with it which takes the form of a privileged function call in the runtime (that includes the most power call: `set_code`, which is able to alter the entire code of the runtime). They are discrete events, have a fixed period where voting happens, and then are tallied and the function call made if the vote is approved.

Referenda can be started in multiple ways:

* Publicly submitted proposals;
* Proposals submitted by the council, either through a no-veto majority or unanimously;
* Proposals submitted as part of the enactment of a prior referendum.

Furthermore, abstention votes in referenda (i.e. the stake of stakeholders that did not vote) can be counted in multiple ways:

* Split in exactly the same ratio of the explicit votes;
* Split increasingly more favourably towards the "against" votes, the lower the turnout;
* Split increasingly more favourably towards the "approve" votes, the lower the turnout.

We assume that a unanimous council agreement signals a largely technocratic and uncontroversial protocol change. For this reason we assert that the "burden of proof" should fall on those against the motion and thus we bias any abstention votes in favour of the motion.

We assume that a majority council agreement, with no veto, signals a sensible, but not uncontroversial protocol change, perhaps an irregular state transition. For this, we make no presumptions over the abstainers and instead split them exactly as the explicit voters decide.

Publicly submitted referenda, being public, can include malevolent actions. Here the onus must be placed on the proponents and so we bias any abstention votes against the motion, in favour of the (assumed safe, since its functional enough to administer this vote) status quo.

In general:

* Proposals submitted through the enactment of a referendum may (of course) use any counting mechanism;
* Proposals submitted by unanimous council agreement need have more than 50% approval when the abstention votes are biased in their favour;
* Proposals submitted by simple majority council agreement must win only a simple majority of explicit votes;
* Publicly submitted proposals must have more than 50% even when the abstention votes are biased against their favour.

### Public Referendums

Proposals for referendums may be submitted by anyone, however they do not automatically get voted upon. When submitted, the proposer stakes a deposit (any size above a fixed minimum) and the proposal enters a queue. While a proposal is in the queue, anybody may "second" the deposit with a second deposit of the same size as the initial deposit.

At a fixed interval, the item in the queue with the most stake associated with it is removed, all deposits are returned and a referendum takes place on it with each depositor being registered as voting in approval.