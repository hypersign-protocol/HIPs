---
hip: 7
title: Concept of Issuer nodes who only store DID data
author: Vishwas Anand (@Vishwas1)
status: Draft
type: Standards Track
category: Core
created: 2022-10-07
---


## Simple Summary
We propose a concept of subchain with verifiable snapshotting on main HID network.

## Abstract
Many companies or organizations would not want to put DIDs on public HID network as data on public network is shared across the world. We propose a concept of subchain with verifiable snapshotting on main HID network. All ssi (did and schema) realted data can kept in subchains or zones with fewer nodes and **periodic verifiable snapshots** can be sent to main HID network for proof of those subchain transactions. This way subchains can obtain sovereign privacy and governance and can obtain (optionally) security from main HID network. 

## Motivation
The motivation behind this feature is to incorporate centralised companies / orgs / judiciaries to use Hypersign SSI system without worrying about leaking informations to different juridiction.

## Problem Summary

Many companies or organizations would not want to put DIDs on public HID network as data on public network is shared across the world. They might feel uncoftable using public permissionless blockchain network where data can be shared across different juridictions. For example:
1. Govt. Of India might want to keep Indian citizen DIDs only in Mumbai Zones.
2. Company A might want to keep their employee data with Company A's network and would only share with Company B if or when required, say for example, if Company A's employee goes to work for Company B in federated setting.

In both of the above scenario's these companies or orgs need to have their own blockchain network and keep their data within their network. 

However, one possible downside with this approach is,  public permissionless blockchians usually have more node and hence they can provider better security than any proivate network with fewer nodes. 

## Proposed Solution

The current [hid-node](https://github.com/hypersign-protocol/hid-node) has SSI module which one can use to perform all SSI realted operations for DID and Schemas. Any company can fork the hid-node binary and create their own network (just like zones in cosmos) for manage digital identity. These zones can then send periodic snapshots to hid network via IBC - obtaining security from bigger network. These snapshots can be cryptographically verified any point of time and does not contain actual data.Similar concept can be found in Polygon's whitepaper (https://github.com/maticnetwork/whitepaper#consensus-con) where they talked about "Checkpointing" for obtaining security from Ethereum main network.

## Rationale

The public permission-less blockchain does not fits on this scenario however, the public chains are expected to be better secured than private chains since, public chains will have more number of nodes as compared to private ones (theoretically).

## Privacy Concerns

The privacy subchain controls his privacy  and governance on its own but for security it can rely on hid network. Furthermore, they can disable the checkpointing mechanism at all and can only run their soverign network. 

## References

* https://github.com/hypersign-protocol/whitepaper/issues/4
* https://github.com/cosmos/ibc/discussions/700 


