---
hip: 8
title: Did Comm / Connection Spec
author: Pratap Mridha (https://github.com/Pratap2018)
status: Draft
type: Standards Track
category: Universal Wallet Conection , DID Communication
created: 2022-10-20
---

## Simple Summary
Did Communication is a protocol for connecting two agents and exchanging secure information over a channel ,i.e. VC,VP . This protocol is independent of any specific transport for messages such as HTTP, WebSockets, Bluetooth, NFC, or any other communication channel. This protocol is agnostic to the message format, and it is possible to use different formats for different purposes. This protocol is also agnostic to the message types and it is possible to define new types.

## Abstract

Universal wallets are required to share the credentials with the verifiers and the verifiers are required to verify the credentials. The communication between the universal wallet and the verifier is done using the DID communication protocol.


## Motivation
 DIDComm Messaging defines how messages compose into the larger primitive of application-level protocols and workflows, while seamlessly retaining trust. “Built atop … DIDs” emphasizes DIDComm’s connection to the larger decentralized identity movement, with its many attendent virtues.

Of course, robust mechanisms for secure communication already exist. However, most rely on key registries, identity providers, certificate authorities, browser or app vendors, or similar centralizations. Many are for unstructured rich chat only — or enable value-add behaviors through proprietary extensions. Many also assume a single transport, making it difficult to use the same solution for human and machine conversations, online and offline, simplex and duplex, across a broad set of modalities. And because these limitations constantly matter, composability is limited — every pairing of human and machine for new purposes requires a new endpoint, a new API, and new trust. Workflows that span these boundaries are rare and difficult.

## Specification
#### Specific Requirements

[Specification From Dif](https://identity.foundation/didcomm-messaging/spec/#specific-requirements)

Message Formats: 
- [Plaintext](https://identity.foundation/didcomm-messaging/spec/#plaintext-message-structure)
- [Signed Message](https://identity.foundation/didcomm-messaging/spec/#c2-didcomm-signed-messages)
- [Encrypted Message](https://identity.foundation/didcomm-messaging/spec/#c3-didcomm-encrypted-messages) 

All this three message formats can be correctly understood as generic JWMs (ref to DID-Comm spec, DIDComm Plaintext) [Read Here](https://identity.foundation/didcomm-messaging/spec/#iana-media-types)

- [DIF Spec](https://identity.foundation/didcomm-messaging/spec/#plaintext-message-structure)
- [Available Libraries](https://didcomm.org/book/v2/hellolibstools)

## Rationale
will be added soon

## Privacy Concerns
will be added soon
## Backwards Compatibility
will be added soon

## Reference Implementation
[Did Comm Lattest Draft](https://identity.foundation/didcomm-messaging/spec/)

[Did Comm v1](https://github.com/hyperledger/aries-rfcs/tree/main/concepts/0005-didcomm)

[Did Comm v2](https://didcomm.org/book/v2/)

[Aries RFC 005](https://github.com/hyperledger/aries-rfcs/tree/main/concepts/0005-didcomm)

[Did Comm 2.0 implementation](https://github.com/decentralized-identity/didcomm-messaging)

[IDF DID Comm](https://identity.foundation/working-groups/did-comm.html)