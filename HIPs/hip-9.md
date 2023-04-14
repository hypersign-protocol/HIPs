---
hip: 9
title: Regulation of `x/ssi` transaction fees through Governance Proposals
author: Arnab Ghose <arnab@hypermine.in>
status: Draft
type: Standards Track
category: Core
created: 2023-04-14
---

## Abstract

The transaction fees of a blockchain are typically tied to the current market price of the underlying token and the gas units required to execute a transaction. The volatility in transaction fees poses a significant challenge for businesses and individuals that rely on blockchain technology for their operations. For instance, during periods of high token prices, the cost of executing transactions can increase dramatically, making it prohibitively expensive for users. This can lead to reduced transaction volumes, hampering the adoption and growth of blockchain networks. Therefore, for SSI-based transactions specifically, it is crucial to establish regulations that ensure fair and consistent fees for users. This proposal aims to introduce a mechanism which can do that job said in the previous sentence. This proposal aims to introduce a mechanism that addresses this issue by providing a framework for setting and adjusting fees based on market conditions.

## Motivation

Consider a business seeking to establish an Identity Management and Credential Issuance/Verification system using the Hypersign Identity Network. To ensure the viability of their system, they must first determine the cost of each SSI transaction. This information will enable them to calculate a margin and offer their services to non-blockchain users in fiat currency.

For example, let's assume the cost of performing a CreateDID transaction is 1000000uhid or 1 HID, and the market price of 1 HID is $0.015. Therefore, the effective price would be $0.015 per transaction (1 HID * $0.015/HID).

However, HID's market price may fluctuate significantly within weeks or months, as seen by its hypothetical rise to $1 within two months, resulting in an effective fee increase to $1 per transaction. Such volatility creates uncertainty for consumers, which can significantly impact the revenue of businesses that set their prices based on the initial market price of 1 HID = $0.015. Establishing a stable fee structure is therefore critical for ensuring the viability of the business and promoting the adoption of the Hypersign Identity Network.

## Proposed Solution

A consensus-based fee mechanism can be established for each SSI transaction, indicating a **fixed fee price**. This way, SSI transactions can be treated separately in terms of transaction fee deduction

#### Governance Proposals on Hypersign

An example of a consensus parameter in Tendermint-based chains that is present by default is `SlashFractionDowntime`, which is a parameter of the `x/slashing` module. Its initial value is set during the creation of the genesis file and cannot be changed by editing the file after the blockchain network is started. A governance proposal is needed to make any changes to the parameter value. We provide a JSON file that includes the information for the parameter change, as follows:

```json
{
  "title": "Change in Validator Node Downtime Penalty",
  "description": "This proposal brings about change in the validator node downtime penalty to 0.365",
  "changes": [
    {
      "subspace": "slashing",
      "key": "SlashFractionDowntime",
      "value": "0.365000000000000000"
    }
  ],
  "deposit": "50000000uhid"
}
```

The `subspace` attribute indicates the module to which the parameter belongs. The `key` attribute specifies the name of the parameter. The `value` attribute represents the new value to which the parameter will be updated.

After a proposal is raised, a voting period of three days is observed during which network stakeholders (validators and their delegators) cast their votes. If the proposal gains at least 50% approval, it is implemented in the network and the value of `SlashFractionDowntime` fraction is updated to `0.365000000000000000`.

#### `CreateDidFee` Fixed-Fee Paramter

While it would be ideal to talk about every `x/ssi` transaction, for the sake of simplicity, let's consider `CreateDidFee` as an example. This parameter represents the fixed fee (in `uhid`) that users must pay for creating a DID transaction. It could be part of a separate module, such as `x/identityfee`, and its placement in the genesis file could be structured as follows:

`genesis.json`
```
.
.

"identityfee": {
  "create_did_fee": {
    "denom": "uhid",
    "amount": "10000"
  }
}
.
.
```

For instance, let's say we set the initial price for a Create DID transaction in the genesis block at `10000uhid`. But later on, if the market conditions change, we might need to lower the price to `8000uhid` to make it more affordable for users.
The JSON document for submiting the governance proposal will be as follows:

```json
{
  "title": "Decrement of CreateDID transaction fee from 10000uhid to 8000uhid ",
  "description": "This proposal brings about change in the CreateDID transaction fee from 10000uhid to 8000uhid",
  "changes": [
    {
      "subspace": "identityfee",
      "key": "CreateDidFee",
      "value": {"denom": "uhid","amount": "8000"}
    }
  ],
  "deposit": "50000000uhid"
}
```

## Privacy Concerns

This proposal has been designed with stringent privacy measures, and as such, there are no concerns regarding privacy implications.

## Backwards Compatibility

In light of this proposal, it is imperative to note that the fundamental structure of SSI transactions will remain unchanged. Therefore, the proposal is deemed backwards compatible.
