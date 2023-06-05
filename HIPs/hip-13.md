---
hip: 13
title: Cross-chain CosmWasm Contract Interaction through IBC
author: Arnab Ghose <arnab@hypermine.in>
status: Draft
type: Standards Track
category: Core
created: 2023-06-01
---

## Abstract

[CosmWasm](https://cosmwasm.com/) is a Cosmos SDK module that offers a platform for deployment of Smart Contracts. This HIP aims to explain how the Inter-Blockchain Protocol (IBC) can be utilized to connect Smart Contracts deployed on separate chains.

## Explanation

In order to illustrate the concept of cross-chain smart contract interaction, let's consider a scenario where we have two contracts: `controller` and `host`. These contracts are deployed on separate chains.

The `host` contract maintains a state variable called `count` which is of type `u64`. Its purpose is to increment the value of the `count` variable when it receives an IBC packet from the `controller` contract. Upon successfully incrementing the `count` variable, an acknowledgment is sent back to the `controller` contract, after which, a map named `address_map` is created to store the relationship between the address of the user who initiated the increment request from the `controller` contract and the number of times they have requested to increment the `count` variable in the `host` contract. Essentially, the `controller` contract acts as a reference point for users to initiate cross-chain contract interactions.

The following sequence diagram illustrates the flow of cross-chain contract interactions. The subsequent sections will cover the details of the methods mentioned in the diagram.

![cross-chain-cosmwasm-interaction](https://github.com/hypersign-protocol/HIPs/assets/32813269/d066a496-a4a1-474f-99ad-215e96a9d81f)

### Enabling IBC for Contracts

CosmWasm contracts are not IBC enabled by default. The following are the methods that need to be implemented in Smart Contracts intended for use with IBC:

```rust
pub fn ibc_channel_open(
    _deps: DepsMut,
    _env: Env,
    _msg: IbcChannelOpenMsg,
) -> Result<IbcChannelOpenResponse, ContractError> {
    // ...
}

pub fn ibc_channel_connect(
    _deps: DepsMut,
    _env: Env,
    _msg: IbcChannelConnectMsg,
) -> Result<IbcBasicResponse, ContractError> { 
    // ...
}

pub fn ibc_channel_close(
    _deps: DepsMut,
    _env: Env,
    _msg: IbcChannelCloseMsg,
) -> Result<IbcBasicResponse, ContractError> {
    // ...
}

pub fn ibc_packet_receive(
    _deps: DepsMut,
    _env: Env,
    _msg: IbcPacketReceiveMsg,
) -> Result<IbcReceiveResponse, Never> {
    // ...
}

pub fn ibc_packet_ack(
    _deps: DepsMut,
    _env: Env,
    _msg: IbcPacketAckMsg,
) -> Result<IbcBasicResponse, ContractError> {
    // ...
}

pub fn ibc_packet_timeout(
    _deps: DepsMut,
    _env: Env,
    _msg: IbcPacketTimeoutMsg,
) -> Result<IbcBasicResponse, ContractError> {
    // ...
}
```

For an in-depth understanding of these methods, refer [here](https://github.com/CosmWasm/cosmwasm/blob/main/IBC.md)

If the above methods are implemented correctly in a smart contract, the contract will be assigned an IBC port in the format `wasm.<contract-address>`. Here's an example:

```
$ hid-noded q wasm contract hid14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9skm6af7

address: hid14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9skm6af7
contract_info:
  admin: ""
  code_id: "1"
  created:
    block_height: "35"
    tx_index: "0"
  creator: hid1vjjqryxhcvd88w0vafn38mvdsp32xm2wm306jp
  extension: null
  ibc_port_id: wasm.hid14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9skm6af7
  label: somecontract1
```

The contract `hid14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9skm6af7` is IBC enabled and has been assigned the IBC port `wasm.hid14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9skm6af7`.

### IBC Packet Data

The structure of the data parsed as binary within the IBC packet is as follows: 

```rust
#[cw_serde]
pub enum IbcExecuteMsg {
    IncrementCount {
        tx_author: String
    },
}
```

The `IncrementCount` structure includes an attribute called `tx_author`, which is used to pass the address of the blockchain transaction author. It's important to note that the `tx_author` attribute is not directly utilized by the `host` contract. Instead, it is used by the `controller` contract upon receiving a successful acknowledgment.

### Host Contract

The `host` contract maintains a `count` variable in its state. When the `host` contract receives an IBC packet of type `IbcExecuteMsg::IncrementCount` (as discussed [here](#ibc-packet-data)) from any counterparty chain, it increments the `count` variable. If the received packet is of a different type, the state remains unaffected. The logic for handling IBC packet reception and state change is implemented in the `ibc_packet_receive` method of the `host` contract as shown below:

```rust
// host/src/ibc.rs

#[cfg_attr(not(feature = "library"), entry_point)]
pub fn ibc_packet_receive(
    deps: DepsMut,
    env: Env,
    msg: IbcPacketReceiveMsg,
) -> Result<IbcReceiveResponse, Never> {
    match do_ibc_packet_receive(deps, env, msg) {
        Ok(response) => Ok(response),
        Err(error) => Ok(IbcReceiveResponse::new()
            .add_attribute("method", "ibc_packet_receive")
            .add_attribute("error", error.to_string())
            .set_ack(make_ack_fail(error.to_string()))),
    }
}

pub fn do_ibc_packet_receive(
    deps: DepsMut,
    _env: Env,
    msg: IbcPacketReceiveMsg,
) -> Result<IbcReceiveResponse, ContractError> {
    let msg: IbcExecuteMsg = from_binary(&msg.packet.data)?;

    match msg {
        IbcExecuteMsg::IncrementCount { tx_author } => execute_ibc_method(deps, tx_author)
    }
}

fn execute_ibc_method(deps: DepsMut, tx_author: String) -> Result<IbcReceiveResponse, ContractError> {
    let mut current_count: u32 = COUNT.load(deps.storage)?;
    current_count += 1;
    COUNT.save(deps.storage, &current_count)?;
    Ok(IbcReceiveResponse::new()
        .add_attribute("method", "execute_ibc_method")
        .set_ack(make_ack_success(tx_author)))
}
```

The IBC packet is deserialized, and then it is checked whether it is of type `IbcExecuteMsg::IncrementCount`. If it is, the `execute_ibc_method` is called, which increments the `count` variable by 1. Subsequently, a success acknowledgement is sent to the `controller` contract. On the other hand, if the packet is not of type `IbcExecuteMsg::IncrementCount`, a failed acknowledgement is sent back to the `controller` contract.

### Controller Contract

The `controller` contract is responsible for sending a signal through IBC to the `host` contract, instructing it to increment the `count` variable.

In the `controller` contract, there is an execute method called `Increment`, which prepares an IBC packet to be sent to the `host` contract. Here's a reference implementation of the method:

```rust
// controller/src/contract.rs

ExecuteMsg::Increment { channel } => Ok(Response::new()
    .add_attribute("method", "execute_query")
    .add_attribute("channel", channel.clone())
    .add_message(IbcMsg::SendPacket { 
        channel_id: channel,
        data: to_binary(&IbcExecuteMsg::IncrementCount { tx_author: info.sender.into_string()  })?, 
        timeout: IbcTimeout::with_timestamp(env.block.time.plus_seconds(120)),
    })
)
```

The method accepts `channel` as an input, which specifies the channel ID of the `host` contract deployed on the counterparty chain. In `IbcMsg::SendPacket`, the information of the IBC packet is defined, including the counterparty chain's channel ID and the data. Here, the data is of type `IbcExecuteMsg::IncrementCount`, where we pass the transaction author's blockchain address as input. The `add_message()` method facilitates the transfer of this packet to the counterparty chain.

Upon receiving this IBC packet, the `host` contract performs the necessary actions as discussed [here](#host-contract).

If a success acknowledgement is received from the `host` contract, the `controller` contract stores a mapping between the blockchain transaction author's address and the number of times it has requested the `count` variable to be incremented. This mapping is stored in the `address_map` variable. The logic for this is implemented in the `ibc_packet_ack` method of the `controller` contract, as shown below:

```rust
// host/src/ibc.rs

#[cfg_attr(not(feature = "library"), entry_point)]
pub fn ibc_packet_ack(
    deps: DepsMut,
    _env: Env,
    msg: IbcPacketAckMsg,
) -> Result<IbcBasicResponse, ContractError> {
    let original_packet: IbcExecuteMsg = from_slice(&msg.original_packet.data)?;
    
    match original_packet {
        IbcExecuteMsg::IncrementCount { tx_author } => after_ibc_acknowledgement(deps, tx_author)
    }    
}

fn after_ibc_acknowledgement(deps: DepsMut, tx_author: String) -> Result<IbcBasicResponse, ContractError> {
    let mut req_count = ADDRESS_MAP.may_load(deps.storage, tx_author.clone())?.unwrap_or_default();
    req_count += 1;
    ADDRESS_MAP.save(deps.storage, tx_author.clone(), &req_count)?;
    Ok(IbcBasicResponse::new().add_attribute("method", "after_ibc_acknowledgement"))
}
```

This is similar to the `ibc_packet_receive` method implemented in the `host` contract, where we match the type of the IBC packet. If the packet is of type `IbcExecuteMsg::IncrementCount`, then the `after_ibc_acknowledgement` function is called. This function stores the mapping between the transaction author's address and the number of times they have requested to increment the `count` variable.

## Reference Implementation

A full fledged implemenatition of the `host` and `controller` contracts is provided [here](https://github.com/hypersign-protocol/ibc-cosmwasm-interaction).

## Privacy Concerns

There is no privacy concern associated with this proposal.

## Backwards Compatibility

There are no backwards compatibility issue with this proposal.

## References

- [cw-ibc-example](https://github.com/0xekez/cw-ibc-example)
- [cw-ibc-queries](https://github.com/JakeHartnell/cw-ibc-queries)
- [ics999](https://github.com/larry0x/ics999)
