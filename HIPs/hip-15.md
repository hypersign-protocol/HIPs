---
hip: 15
title: Interaction between CosmWasm Smart Contracts and `x/ssi` module
author: Arnab Ghose <arnab@hypermine.in>
status: Draft
type: Standards Track
category: Core
created: 2023-08-09
---

## Abstract

In the upcoming Hypersign Testnet release, the much-awaited support for the CosmWasm module will be introduced, opening up possibilities for the developers to explore Self-Sovereign Identity (SSI) based use-cases using CosmWasm Smart Contracts. In its default implementation, CosmWasm contracts have the capability to interact with other contracts on the same blockchain or different blockchains through IBC. However, they are unable to directly interact with any of the native Cosmos modules. This HIP proposal seeks to discuss "Wasm Bindings" as a means to facilitate interaction between CosmWasm Contracts and the `x/ssi` module.

## Wasm Bindings

### Overview

Wasm Bindings can be envisioned as a conduit for Smart Contracts to establish communication with the `x/ssi` module. Each of these bindings serves a distinct functionality, functioning in a manner akin to a CosmWasm Contract method. These bindings are categorized into Execute and Query types. In a broader sense, though not entirely precise, these bindings cause the `x/ssi` module to function similarly to a Smart Contract with predefined methods.

You can find a sample reference implementation [here](https://github.com/hypersign-protocol/hid-node/tree/tmp-hip-15-wasm-x-ssi), where the following bindings are supported:

**Query Based**

- `DidDocumentExists` - It takes a DID Document ID as input and verifies the existence of the requested DID Document in the state. The result is then returned as either `True` or `False`. 

```go
// wasmbinding/bindings/query.go

// SsiContractQuery contains custom queries for x/ssi chain
type SsiContractQuery struct {
    DidDocumentExists *QueryDidDocumentExistsRequest `json:"did_document_exists,omitempty"`
}

type QueryDidDocumentExistsRequest struct {
    DidId string `json:"did_id,omitempty"`
}

type QueryDidDocumentExistsResponse struct {
    Result bool `json:"result"`
}
```

**Execute Based**

- `SetBlockchainAccountId` - It accepts a Blockchain Account Id as input and assigns the value to the `blockchainAccountId` store. In the context of real-life use cases, this scenario lacks practical significance, thus should be regarded as a placeholder example. 

```go
// wasmbinding/bindings/msg.go

type SsiContractMsg struct {
    SetBlockchainAccountId *SetBlockchainAccountIdStruct `json:"set_blockchain_account_id,omitempty"`
}

type SetBlockchainAccountIdStruct struct {
    BlockchainAccountId string `json:"blockchain_account_id,omitempty"`
}
```

### CosmWasm Integration

Developers aiming to construct Smart Contracts capable of interacting with the x/ssi module need to define Enums representing the supported Query and Execute bindings within their contracts. To elaborate on this process, we will refer to the aforementioned Wasm Binding implementation.

**Query Based**

```rs
// msg.rs
use cosmwasm_std::{Binary, CustomQuery}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum SsiQueries {
    // The enum definition MUSTT match with Bindings defined in `wasmbinding/bindings/query.go` in hid-node
    DidDocumentExists {
        did_id: String,
    },
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub struct ChainResponse {
    pub ssi_query_result: Binary,
}

impl CustomQuery for SsiQueries {}
```

These enums can be used as illustrated below. For instance, consider a query method named `CheckIfDidExists` that accepts a string input named `input_did_id`:

```rs
// contract.rs

#[cfg_attr(not(feature = "library"), entry_point)]
pub fn query(deps: Deps<SsiQueries>, env: Env, msg: QueryMsg) -> StdResult<Binary> {
    match msg {
        QueryMsg::CheckIfDidExists { input_did_id } => to_binary(&query_with_payload(deps, env, input_did_id)?)
    }
}

fn query_with_payload(
    deps: Deps<SsiQueries>,
    _env: Env,
    input_did_id: String,
) -> StdResult<ChainResponse> {
    let payload = QueryRequest::Custom(SsiQueries::DidDocumentExists { did_id: input_did_id });

    let raw = to_vec(&payload).map_err(|serialize_err| {
        StdError::generic_err(format!("Serializing QueryRequest: {}", serialize_err))
    })?;

    match deps.querier.raw_query(&raw) {
        SystemResult::Err(system_err) => Err(StdError::generic_err(format!(
            "Querier system error: {}",
            system_err
        ))),
        SystemResult::Ok(ContractResult::Err(contract_err)) => Err(StdError::generic_err(format!(
            "Querier contract error: {}",
            contract_err
        ))),
        SystemResult::Ok(ContractResult::Ok(value)) => Ok(ChainResponse { ssi_query_result: value }),
    }
}

```

**Execute Based**

```rs
// msg.rs
use cosmwasm_std::{CustomMsg, CosmosMsg};

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum SsiMsgs {
    // The enum definition MUST match with Bindings defined in `wasmbinding/bindings/msg.go` in hid-node
    SetBlockchainAccountId {
        blockchain_account_id: String,
    }
}

impl From<SsiMsgs> for CosmosMsg<SsiMsgs> {
    fn from(msg: SsiMsgs) -> Self {
        CosmosMsg::Custom(msg)
    }
}

impl CustomMsg for SsiMsgs {}
```

These enums can be used as illustrated below. Suppose we have an Execute method called `PerformExecOperation` that requires a string input named `blockchain_account_id`:

```rs
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn execute(
    _deps: DepsMut,
    _env: Env,
    _info: MessageInfo,
    msg: ExecuteMsg,
) -> StdResult<Response<SsiMsgs>> {
    let msg: CosmosMsg<SsiMsgs> = match msg {
        ExecuteMsg::PerformExecOperation { blockchain_account_id } => {
            SsiMsgs::SetBlockchainAccountId { blockchain_account_id: blockchain_account_id }.into()
        },
    };

    Ok(Response::new().add_message(msg))
}
```

### Developer Exerience

Instead of manually composing these bindings, developers have the option to leverage a `Brokerage` contract. This contract, deployed by the Hypersign Dev team, will come pre-equipped with the supported Wasm Bindings and corresponding contract methods. Developers can conveniently invoke these methods directly from their contracts, accessing the intended functionality. Importantly, this capability isn't confined solely to contracts deployed on Hypersign, as it can also be extended to contracts deployed on other chains via the Inter-Blockchain Communication (IBC) protocol. (discussed in [HIP-13](https://github.com/hypersign-protocol/HIPs/blob/main/HIPs/hip-13.md))
