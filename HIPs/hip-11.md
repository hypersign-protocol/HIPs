---
hip: 11
title: Setting a Governance-based Minimum Gas Price
author: Arnab Ghose <arnab@hypermine.in>
status: Draft
type: Standards Track
category: Core
created: 2023-05-02
---

## Abstract

A minimal bootstrapped blockchain built using Cosmos SDK allows validators to set a minimum fee for any transaction they accept by using the `minimum-gas-prices` parameter. The value of this parameter is an independent decision left to the validators. Since the fee value is tied to the token price, it increases as the token price rises. Furthermore, setting `minimum-gas-prices` to 0 could allow spam transactions to flood the network, leading to congestion.

To address these issues, this proposal suggests the introduction of a governance-based parameter for a minimum fee. This would enable the community to collectively decide on an appropriate minimum fee for transactions, thereby preventing spam attacks and ensuring network stability.

## Motivation

This proposal takes inspiration from [HIP-9](https://github.com/hypersign-protocol/HIPs/blob/main/HIPs/hip-9.md), which emphasized the need for a fixed-fee parameter in SSI-based blockchain transactions to regulate fee prices through governance proposals based on the token price.

However, a drawback of the fixed-fee approach is that it doesn't account for the Gas Units consumed during the transaction. Gas Units represent the amount of computation required for a given transaction. Larger transactions consume more Gas Units, resulting in higher fees. Introducing a fixed fee parameter meant that a DID Creation transaction with 100 Verification Methods and a DID Creation transaction with just 1 Verification Method are treated the same in terms of fee deduction, which is not ideal.

## Proposed Solution

A global consensus-based governance parameter, called `global-min-fees`, can be introduced to set a minimum fee agreed upon by every validator. Validators are allowed to charge a higher fee than the global minimum. This eliminates the possibility of validators setting fees to 0, which would prevent spam transactions.

For instance, let's assume `global-min-fees` is set to `0.1 HID`, and the user is using a validator's node that charges a minimum fee of `0.2 HID`. The transaction through this validator will go through since it meets the `global-min-fees` criteria. If the validator had set their minimum fee to, say, `0.01 HID`, and the user tried to pay the minimum fee, the transaction would have failed.

Introducing `global-min-fees` would result in the following scenarios:

1. Validators will compete with each other to include as many transactions as possible when they act as block proposers, by gradually decreasing the value of their min-gas-prices over time. Some may choose to set their value to match the `global-min-fees`, while others may choose to keep their min-gas-prices a bit higher, which we will discuss in the next scenario.

2. The majority of a validator's income usually comes from minted block rewards rather than transaction fees. A validator may choose to increase their fees higher than the `global-min-fees` to increase their earnings.

3. Validators may choose to raise a governance proposal to increase `global-min-fees` if they feel the global minimum fee is too low.

Considering these scenarios, it can be envisioned that over time, the overall fees of the network could reach an equilibrium, similar to [Nash Equilibrium](https://en.wikipedia.org/wiki/Nash_equilibrium).

## Reference Implementation

[Cosmos Hub](https://github.com/cosmos/gaia) have built a global fee module that implements the approach mentioned in this proposal. You can check the implementation [here](https://github.com/cosmos/gaia/tree/main/x/globalfee).

## Privacy Concerns

This proposal has been designed with stringent privacy measures, and as such, there are no concerns regarding privacy implications.

## Backwards Compatibility

In light of this proposal, it is imperative to note that the fundamental structure of SSI transactions will remain unchanged. Therefore, the proposal is deemed backwards compatible.
