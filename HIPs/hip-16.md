---
hip: 16
title: Interchain Web3 Reputation
author: Arnab Ghose <arnab@hypermine.in>
status: Draft
type: Standards Track
category: Core
created: 2024-03-04
---

## Abstract

In this HIP, we intend to present the idea of Interchain Web3 Reputation as a way of scoring wallet addresses belonging to different blockchains. This involves assessing on-chain activities, which include but are not limited to, holding a certain amount of native tokens or participating in a liquidity pool on a decentralized exchange (DEX), etc.

## Motivation

Wallet addresses serve as entry points into the blockchain world, enabling various activities such as token transfers, buying and selling NFTs, and holding Verifiable Credentials. The concept of assigning a score to a wallet address arises from the recognition that DApps may need to verify the authenticity of a given address. For instance, a decentralized exchange (DEX) might only want to permit trading for wallet addresses with substantial activity in the Web3 space.

This concept dovetails seamlessly with Decentralized Identifiers (DIDs). A DID document includes a `verificationMethod` attribute, comprising a list of public keys utilizing different algorithms. Within the `verificationMethod` document, there's a specific attribute named `blockchainAccountId` that stores the corresponding wallet address for the public key. Since a user can possess multiple wallet addresses, each designated for distinct activities, the objective is to score a DID ID. This DID ID would host the various wallet addresses a user wishes to include, and an aggregated score would be associated with each wallet within that DID. This scoring mechanism enhances the trustworthiness of wallet addresses, aligning with the principles of decentralized identity verification in the blockchain ecosystem.

## Architecture

The reputation system is a CosmWasm based Smart Contract solution. It primarily involves three major contracts: `Activity Contract`, `Activity Manager Contract` and `Reputation Engine Contract`

### Activity Contract

The Activity Contract defines the fundamental logic for verifying an activity, requiring the implementation of both query and execute-based contract methods for validity. Query-based methods primarily serve to retrieve information about the contract, such as its Name, Description, Score, and whether a User has completed the activity. The execute-based method is where the verification logic is housed. The contract's state has a Map between DID Id and a bool value to indicate if a DID Id has perfomed the activity. To implement this methods, an `activity` package [here](https://github.com/hypersign-protocol/interchain-web3-reputation/tree/develop/activities/packages/activity) written where developers can import traits to define the methods in their Activity contract.

There are two variations of the Activity Contract:

1. Single Contract:

This involves deploying a singular Activity Contract on the Hypersign Chain, designed for intra-chain activities. It is particularly useful for tasks like checking the total $HID holdings on a Hypersign wallet address.

2. Paired with Proxy Contract over IBC:

The overarching objective is to extend the Reputation solution to different chains. While querying the state of the blockchain on which the contract is deployed is straightforward, directly querying the state of other blockchains poses challenges. Based on our research, deploying a contract on the target chain (termed Proxy Contract) and connecting it with an Activity Contract on the source chain over the Inter-Blockchain Communication (IBC) protocol facilitates the successful cross-chain transfer of data. This approach enhances the interoperability of the Reputation solution across various blockchain networks.

Let's look at some examples of Acitivty Contracts with Proxy Contract Pairing architecture

**Example: Liquidity Position in Osmosis**

In a Decentralised Exechange, when a user tend to deposit tokens in a Liquidity Pool in exchange for rewards, they tend to take a "position" in the pool. A user can take as many positions as they want in a Liquidity Pool. Since, LP position information is managed by `concentratedliquidity` module and there aren't any dedicated Wasm Bindings to fetch this info, the Proxy contract utilises Stargate Query to accomplish this.

Check out the implementation of this Activity [here](https://github.com/hypersign-protocol/interchain-web3-reputation/tree/develop/activities/contracts/osmosis-lp-position), where we essentially check if a user has taken *any* position in a specific Liquidity Pool or not


**Example: Ownership of Stargaze NFT**

This activity is to check if a user owns NFT of particular collection on Stargaze chain. Since, NFTs are available as CosmWasm Contracts, the proxy contract simply performs intra-contract calls to get ownership info from the NFT contracts.

Check out the implementation of this Activity [here](https://github.com/hypersign-protocol/interchain-web3-reputation/tree/develop/activities/contracts/stargaze-nft-ownership)

**Example: Ownership of Omniflix NFT**

This activity is to check if a user owns NFT of particular collection on Omniflix chain. Similar to Osmosis example, we utilise Stargate queries to fetch the information because all NFT related work is done by a Cosmos module instead of a CosmWasm contract.

Check out the implementation of this Activity [here](https://github.com/hypersign-protocol/interchain-web3-reputation/tree/develop/activities/contracts/omniflix-nft-ownership)


### Activity Package Specification

We have a created a Rust package named `activity` which aids developers in building their own smart contracts. It consists of the necessary basic Enums and Traits that developers can simply import and extend. Check out the package [here](https://github.com/hypersign-protocol/interchain-web3-reputation/tree/develop/activities/packages/activity)

**Enums**

Following are the enums which the developers must implement:

`ActivityExecuteMsg` - These are execution based enums. It has a generic `T` which lets developers to extend the `activity_params` attribute by including fields which are based on the use-case of the Activity contract

```rs
pub enum ActivityExecuteMsg<T>
{
    PerformActivity {
        did_id: String,
        activity_params: T
    }
}
```

`ActivityQueryMsg` - These are query based enums. There are basic information which a contract should have such as Contract name, contract score and conract description, along with the ability to check where a `did_id` has completed the activity or not.

```rs
pub enum ActivityQueryMsg {
    // Returns Activity Name
    Name {},

    // Returns Activity Score
    Score {},

    // Returns Activity Description
    Description {},

    // Check if Activity executed by a DID Id is completed or not
    CheckActivityStatus{
        did_id: String
    },
}
```

**Traits**

Execute and Query based traits enforce function definitions for developers to extend and write their own logic within these functions

- `ActivityExecute`

```rs
pub trait ActivityExecute<T>
{
    type Err: ToString;

    fn perform_activity(
        &self,
        deps: DepsMut,
        env: Env,
        did_id: String,
        activity_params: T
    ) -> Result<Response, Self::Err>;
}
```

- `ActivityQuery`

```rs
pub trait ActivityQuery {
    fn name(
        &self,
        deps: Deps
    ) -> StdResult<NameResponse>;

    fn score(
        &self,
        deps: Deps
    ) -> StdResult<ScoreResponse>;

    fn description(
        &self,
        deps: Deps
    ) -> StdResult<DescriptionResponse>;

    fn check_activity_status(
        &self,
        deps: Deps,
        did_id: String,
    ) -> StdResult<CheckActivityStatusResponse>;
}
```

**Implementation Example**

Let's consider [Osmosis LP Activity](https://github.com/hypersign-protocol/interchain-web3-reputation/tree/develop/activities/contracts/osmosis-lp-position) contract as an example. We will have a surface level look at relevant part of the contract only.

In `Cargo.toml` of the project, add the following line to add the package

```rs
activity = { git = "https://github.com/hypersign-protocol/interchain-web3-reputation.git", branch = "develop" }
```

We have defined a struct called `OsmosisActivityContract` which will implement the activity package traits

```rs
// state.rs
pub struct OsmosisActivityContract<'a> 
{
    pub activity_map: Map<'a, String, bool>,
}

impl<'a> Default for OsmosisActivityContract<'static>
{
    fn default() -> Self {
        Self { 
            activity_map: Map::new("activity_map"),
        }
    }
}

impl<'a> OsmosisActivityContract<'a>
{
    pub fn new(
        activity_map_key: &'a str,
    ) -> Self {
        Self { 
            activity_map: Map::new(activity_map_key)
        }
    }
}
```

We now implement the Execute and Query traits for this struct like following:

```rs
// state.rs
use activity::{ActivityExecute, ActivityQuery};

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
pub struct ActivityParams {
    pub ibc_channel: String,
}

impl<'a> ActivityExecute<ActivityParams> for OsmosisActivityContract<'a>
{   
    type Err = ContractError;

    fn perform_activity(
        &self,
        deps: DepsMut,
        env: Env,
        did_id: String,
        activity_params: ActivityParams
    ) -> Result<Response, ContractError> {
        // .. Implementation of perform activity -- //
    } 
}

impl<'a> ActivityQuery for OsmosisActivityContract<'a>
{
    fn name(
        &self,
        deps: Deps
    ) -> StdResult<activity::NameResponse> {
        // -- Implementation -- //
    }

    fn score(
        &self,
        deps: Deps
    ) -> StdResult<activity::ScoreResponse> {
        // -- Implementation -- //
    }

    fn description(
            &self,
            deps: Deps
        ) -> StdResult<activity::DescriptionResponse> {
        // -- Implementation -- //
    }

    fn check_activity_status(
        &self,
        deps: Deps,
        did_id: String,
    ) -> StdResult<activity::CheckActivityStatusResponse> {
        // -- Implementation -- //
    }
}
```

Now, can associate these struct methods with activity package Enums as follows:

```rs
// query.rs
use activity::ActivityQueryMsg;

impl<'a> OsmosisActivityContract<'a> {
    pub fn query(
        &self,
        deps: Deps,
        _env: Env,
        msg: ActivityQueryMsg
    ) -> StdResult<Binary> {
        match msg {
            ActivityQueryMsg::Name {  } => to_json_binary(&self.name(deps)?),
            ActivityQueryMsg::Score {  } => to_json_binary(&self.score(deps)?),
            ActivityQueryMsg::Description {  } => to_json_binary(&self.description(deps)?),
            ActivityQueryMsg::CheckActivityStatus { did_id } => to_json_binary(&self.check_activity_status(deps, did_id)?)
        }
    }
}
```

```rs
// execute.rs
use activity::ActivityExecuteMsg;

impl<'a> OsmosisActivityContract<'a> {
    pub fn execute(
        &self,
        deps: DepsMut,
        env: Env,
        _info: MessageInfo,
        msg: ActivityExecuteMsg<ActivityParams>
    ) -> Result<Response, ContractError> {
        match msg {
            ActivityExecuteMsg::PerformActivity { 
                did_id, 
                activity_params 
            } => self.perform_activity(deps, env, did_id, activity_params)
        }
    }
}
```

Please note that this is just one way of implementation, and its completely upon the developer as to how they wish to structure their activity contract project

### Activity Manager Contract

Activity Manager is a contract, deployed on Hypersign chain, which is a registrar for activity contracts. An Activity Contract is only registered if it has the expected base query methods, which we talked about in the earlier section. 

This contract has one Execute based method to store information about the Activity Contract in its state. This is done by taking Activity Contract's address as input, performing intra-chain contract call to the input Activity contract and executing its Query based methods to gather information such as Name, Score, Description. If any expected Query based method is not found in the Activity Contract, the registration fails.

The contract's state has a map between DID Id and a list of Activities it has successfully done. This information is crucial as we will talk about Reputation Engine Contract in the following section

Implementation of this contract can be found [here](https://github.com/hypersign-protocol/interchain-web3-reputation/tree/develop/reputation-dashboard/contracts/activity-manager-contract)

### Reputation Engine Contract

Reputation Engine is where the logic to compute the reputation score of a DID Id resides. The instantiation of this contract requires passing the address of the Activity Manager Contract that it wants to get the information about the list of activities that is associated with a DID Id. It should be noted that its operation is independent of the Activity Manager Contract.

The contract has a query based method which takes DID Id as input and returns a score and its breakdown

Implementation of this contract can be found [here](https://github.com/hypersign-protocol/interchain-web3-reputation/tree/develop/reputation-dashboard/contracts/reputation-engine-contract)

### Schematic Diagram

The following diagram showcases an overall architecture of Deployed Contracts involved in Interchain Web3 Reputation System:

![Schematic Diagram](./images/interchainReputationContractArch.png)

### Lifecycle

1. Once a developer has written an Activity Contract, the controller or admin of the Activity Manager Contract performs a `RegisterActivity` call by entering the contract address of the Activity Contract.

2. The Activity Manager calls the Activity contract by executing the query methods: `Name`, `Score`, `Description` to store information related to the Activity Contract in its state. If any of these three methods are not implemented, the registration fails.

3. Independently, the reputation engine contract can call the Activity Manager contract's query method, `ActivitiesByDidId`, by passing the DID Id of the user. Internally, this method checks each and every registered Activity Contract by calling their `CheckActivity` query method, which informs whether the activity is done or not. Only completed activities are returned and the score is calculated according to the logic written in Reputation Engine Contract

## Future Scope

Currently, this system caters to CosmWasm enabled Cosmos chains only. The future scope of work involves researching and developing solutions for verifying activities of non-CosmWasm based Cosmos chains.
