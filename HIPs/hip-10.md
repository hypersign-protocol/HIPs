---
hip: 10
title: did:hid Method Specification
authors: Arnab Ghose <arnab@hypermine.in>, Vishwas Anand Bhushan <vishwas@hypermine.in>
status: Draft
type: Standards Track
category: Core
created: 2023-04-21
---

## `did:hid` Method Specification

The `did:hid` specification is intended to be a Standard for Decentralized Identifiers (DIDs) on the Hypersign Identity Network. 

The Hypersign Identity Network is a permissionless blockchain network to manage digital identity and access rights. It aims to empower humans to gain control of their data and access on the internet by providing scalable, interoperable and secure [verifiable data registry (VDR)](https://www.w3.org/TR/did-core/#dfn-verifiable-data-registry) to implement use cases on Self Sovereign Identity (SSI) principles. The Hypersign Identity Network is built using [Cosmos-SDK](https://tendermint.com/sdk/) and is fully compatible with [W3C DID specifications](https://www.w3.org/TR/did-core/).

## Use Cases

The `did:hid` Method Specification is being designed to address a wide variety of use cases related to self-sovereign identity (SSI). Some of them are as follows:

### Web3 Reputation

A reputation score can be assigned to blockchain wallet address(s) to indicate its credibility. The reputation may be based on activities these wallets do, assets they own, balances they have and so on.  To facilitate this, a user can register a DID Document, which can include multiple wallets they own (across different blockchains) in the verification method of the said document. An application intended to provide a reputation score can access this DID Document and review the linked wallet addresse(s) to determine the a score. The score may be given in form of a Verifiable Credential (VC) or Soul Bound Token (SBT). This score can then be consumed by any decentralised/centralised apps.

### On-chain KYC

Our vision is for Hypersign DIDs and credentials to be queryable and verifiable across various blockchains within the [Cosmos Ecosystem](https://cosmos.network/) and beyond, through the [Inter-Blockchain Communication Protocol (IBC) Protocol](https://ibcprotocol.org/). For instance, if a user wishes to access a blockchain that offers a DEX (Decentralised Exchange) service within the Cosmos Ecosystem, they may first provide zk-proof of their KYC credentials to the DEX. The DEX can query the Hypersign Blockchain for the user's DID Document by [querying through IBC module](https://github.com/hypersign-protocol/HIPs/blob/main/HIPs/hip-5.md) and verify the proof of KYC before allowing the user to trade. This will bring compliance to web3 ecosystem (without voilating web3 principles), making it much more safer place to be.


### Organizational Identity Access and Management

Decentralized identifiers (DIDs) can be registered not only for individuals but also for organizations. An employee who already has a registered DID Document can register a DID for the organization, with the employee's DID serving as the controller of the organization's DID Document. Furthermore, the employee, as the controller can, authorize their colleagues to also control the organization's DID.



## Method Syntax

The ABNF syntax of `did:hid` is as follows:

```
did:                           "did:hid" ":" [chain-namespace] ":" method-specific-id
chain-namespace:               [-a-zA-Z0-9]{1,10}
method-specific-id:            caip-10-blockchain-account-id / alphanumeric-rule
caip-10-blockchain-account-id: chain_id + ":" + account_address
chain_id:                      [-a-z0-9]{3,8}:[-_a-zA-Z0-9]{1,32}
account_address:               [-.%a-zA-Z0-9]{1,128}
alphanumeric-rule:             ALPHA / DIGIT / . / -
```

### Chain Namespace

The `chain-namespace` is an optional attribute that reflects the location of the Identifier's Registry. If the DID Document is registered on the Hypersign Mainnet Network, this attribute should be skipped. Otherwise, it must be mentioned. The name can be arbitrary, but it should follow the general rule mentioned in the  [Method Syntax](#method-syntax) 

Mainnet DID Document ID : `did:hid:z9ztgXU5YupF5ME1HV3AKBW94CfGc7qMjrhUoLbFnaLat`  
Testnet DID Document ID : `did:hid:testnet:z9ztgXU5YupF5ME1HV3AKBW94CfGc7qMjrhUoLbFnaLat`  
Private DID Document ID : `did:hid:localnet:z9ztgXU5YupF5ME1HV3AKBW94CfGc7qMjrhUoLbFnaLat`  

### Method Specific ID

The specification supports two formats for the `method-specific-id`.

**CAIP-10 Blockchain Account ID**

This format allows the linkage of a blockchain address with DID Document. Users can essentially use popular blockchain wallets such as [Keplr](https://www.keplr.app/) and [MetaMask](https://metamask.io/) to sign the DID Document. Here, the wallet will act as the sole Key Management System. One of the main reasons behind introducing this format was to avoid the need to create a mapping between a DID and a blockchain address, so that DID Documents can be retrieved directly using just the wallet address. One use-case that utilises this format is mentioned [here](#web3-reputation). To read more about CAIP-10 Blockchain Account ID, look [here](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-10.md).

Examples:
 - `did:hid:testnet:cosmos:jagrat:hid1f6r0x3pljpl7pe76zzv36l0ksztqmdlth7zdk5` (Hypersign Tesnet - Jagrat)
 - `did:hid:cosmos:osmo-1:osmo1f6r0x3pljpl7pe76zzv36l0ksztqmdltakhv4r` (Osmosis Mainnet)
 - `did:hid:eip155:1:0xF4eE129BEDE6ac5E870bCf972e74A117b4809df9` (Ethereum Mainnet)

**Alphanumeric IDs**

This format caters to scenarios where users may wish to create DID Documents using an identifier that is not a blockchain address. This may include, but is not limited to, identifiers such as UUIDs, Ethereum Name Service (ENS) domains, and so on. The only special characters allowed are the dot (.) and hyphen (-).

Examples:
 - `did:hid:1b55c1ec-39e3-4e49-9fa9-7dc6ce27a112`
 - `did:hid:somedomain.xyz`


## DID Document Structure

The DID Document, along with its metadata, is stored on the blockchain in the following manner:

```json
{
    "didDocument": {
        "id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
        "controller": [ "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv" ],
        "verificationMethod": [
            {
                "id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv#k1",
                "type": "Ed25519VerificationKey2020",
                "controller": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
                "publicKeyMultibase": "zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
            }
        ],
        "authentication": [ "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv#k1" ]
    },
    "didDocumentMetadata": {
        "created": "2023-04-19T02:16:00Z",
        "updated": "2023-04-19T02:16:00Z",
        "deactivated": false,
        "versionId": "5B8D61A575C81565E8D23A9A85FEED160FB004C6B3CEA815080AAEDA9D553C97"
    }
}
```

The DID Document structure is compliant with W3C's DID Core Specification. The supported fields are:

1. `id`: Identifies the DID Subject
2. `controller` : List of DID Ids that controls the CRUD operation of DID Document
3. `verificationMethod`: List of Verification Keys
4. `service`: List of service endpoints which links the DID Document with a Web Resource
5. `alsoKnownAs`: List of aliases for the DID Subject
6. `authentication`: List of Verification Methods IDs which can be used in Authentication tasks such as engaging in any sort of challenge-response protocol
7. `assertionMethod`: List of Verification Methods IDs which can be used to express claims, such as claiming a Verifiable Credential
8. `capabilityInvocation`: List of Verification Methods IDs which can be used by the DID Subject to invoke a cryptographic capability, such as the authorization to update the DID Document.
9. `capabilityDelegation`: List of Verification Methods IDs which can be used to specify a mechanism that might be used by the DID subject to delegate a cryptographic capability to another party, such as delegating the authority to access a specific HTTP API to a subordinate.
10. `keyAgreement`: List of Verification Methods IDs which can be used to specify how an entity can generate encryption material in order to transmit confidential information intended for the DID subject, such as for the purposes of establishing a secure communication channel with the recipient.

**`verificationMethod` Structure**

The following attributes are part of the `verificationMethod` structure:

1. `id`: Verification Method ID
2. `type`: Specifies the Algorithm of the Verification Key
3. `controller`: DID ID which owns the Verification Method
4. `publicKeyMultibase`: Verification Key in Multibase Base58BTC encoding
5. `blockchainAccountId`: CAIP-10 Blockchain Account ID

**Note**: At least one of `publicKeyMultibase` or `blockchainAccountId` must be provided. In situations where both are provided, the address in `blockchainAccountId` must be a valid derivative of the verification key provided in publicKeyMultibase.

**`service` Structure**

The following attributes are part of the `service` structure:

1. `id`: Service ID
2. `type`: Service Type
3. `serviceEndpoint`: URL linked towards a Web Resource

**DID Document Metadata** Structure:

1. `created`: Date and Time of DID Document registration. (RFC3339 Format)
2. `updated`: Date and Time of latest change being made to the DID Document. (RFC3339 Format)
3. `deactivated`: Indicates whether a DID Document is deactivated or not.
4. `versionId`: Version ID of the DID Document.


## Supported Key Algorithms

The Cryptographic Algorithms that the specification supports for Verification Keys are:

1. **Ed25519VerificationKey2020**

```json
{
    "id": "did:hid:z9jM9ZEr7jh2gugvfeLLHsvQzfMRQ5C6yyKE1dikTp4vL#k1",
    "type": "Ed25519VerificationKey2020",
    "controller": "did:hid:z9jM9ZEr7jh2gugvfeLLHsvQzfMRQ5C6yyKE1dikTp4vL",
    "publicKeyMultibase": "z9jM9ZEr7jh2gugvfeLLHsvQzfMRQ5C6yyKE1dikTp4vL"
}
```

2. **EcdsaSecp256k1VerificationKey2019**

```json
{
    "id": "did:hid:6d63c408-d874-480b-9195-b1cf1d20ef25#k1",
    "type": "EcdsaSecp256k1VerificationKey2019",
    "controller": "did:hid:6d63c408-d874-480b-9195-b1cf1d20ef25",
    "publicKeyMultibase": "zzkarqRtiPpH5oKdM5f2ro8bjVbe4sB2QvhbXmi4RH6FD"
}
```

3. **EcdsaSecp256k1RecoveryMethod2020**

```json
{
    "id": "did:hid:eip155:1:0x35A868a3e18514870407F722B243f0780d290A93#k1",
    "type": "EcdsaSecp256k1RecoveryMethod2020",
    "controller": "did:hid:eip155:1:0x35A868a3e18514870407F722B243f0780d290A93",
    "blockchainAccountId": "eip155:1:0x35A868a3e18514870407F722B243f0780d290A93"
}
```

### `ClientSpec` Overview

A simple signature verification algorithm involves three components: a message, public key, and signature. When verifying a signed DID Document, it's important to ensure that the document was signed as-is.

However, there may be situations where a DID Document is signed by a blockchain wallet. These wallets often don't sign the DID Document as-is, but instead encapsulate it with a standard format/structure before signing it. To verify the DID Document, we need to first encapsulate the provided DID Document within the same structure that the wallet had encapsulated before signing it, and then proceed with the verification.

Since different wallets have different approaches to signing a message, it's necessary to have a `ClientSpec` to label the origin of a signature. Currently, two supported ClientSpec methods are available: 

| ClientSpec | Supported Wallets |
| ---------- | ----------------- |
| [cosmos-ADR036](https://github.com/cosmos/cosmos-sdk/blob/main/docs/architecture/adr-036-arbitrary-signature.md) | [Keplr](https://www.keplr.app/) |
| [eth-personalSign](https://docs.metamask.io/wallet/how-to/sign-data/#use-personal_sign) | [MetaMask](https://metamask.io/) |
 
Because signed documents can originate from both wallets and non-wallet sources, this attribute is **optional**.

**Note**: ClientSpec mentioned here may be subject to change based on future research. For example, with the introduction of [EIP-712](https://eips.ethereum.org/EIPS/eip-712), [eth-personalSign](https://docs.metamask.io/wallet/how-to/sign-data/#use-personal_sign) may no longer be the preferred method for signing DID Documents. For the sake of simplicity, [eth-personalSign](https://docs.metamask.io/wallet/how-to/sign-data/#use-personal_sign) was introduced for the initial implementation of ClientSpec.

## DID Operations

### Create DID

To register a DID Document on the Hypersign Identity Network, an RPC call is made to the blockchain, which must include the following details:

- The DID Document itself.
- Signatures for verification of Verification Methods and Controllers.
- [Optional] `clientSpec` (Refer [here](#clientspec-overview))


A typical representation of a Create DID RPC call would look something like the following:

```js
CreateDidRPC({
    didDocument: {
        "id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
        "controller": [ "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S" ],
        "verificationMethod": [
            {
            "id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S#k1",
            "type": "EcdsaSecp256k1VerificationKey2019",
            "controller": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
            "publicKeyMultibase": "z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
            }
        ],
        "authentication": [ "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S#k1" ]
    },
    signatures: [{
        "verification_method_id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S#k1",
        "signature": "<signature string>",
        "clientSpec": {
            "type": "cosmos-ADR036",
            "adr036SignerAddress": "hid1f6r0x3pljpl7pe76zzv36l0ksztqmdlth7zdk5"
        }
    }],
    creator: "hid1f6r0x3pljpl7pe76zzv36l0ksztqmdlth7zdk5",
})
```

**General Steps**

1. Validate every field in the input DID Document.
2. If the `method-specific-id` of the DID Document corresponds to a CAIP-10 Blockchain Account ID, verify its ownership. Else, skip this step.
  2.1. Iterate through each verification Method and check if the CAIP-10 Blockchain Account ID in `method-specific-id` is present in any of them.  
  2.2. If it is not found, raise an error.
3. Check if the input DID Document already exists in the Registry. If it does, raise an error.
4. Collect all Verification Methods and controllers from the input DID Document, and associate them with input signatures from `signatures` field.  
  4.1. If there is no signature for any of the Verification Methods or controllers, raise an error.  
  4.2. If any of the Verification Methods or controllers' signatures are invalid, raise an error.
5. Register the DID Document in the Registry.

**Scenarios**

1. VALID: A DID Document with valid signatures of every controller and verification method.

Consider the following DID Document to be registered:

```json
{
    "id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
    "controller": [ "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S" ],
    "verificationMethod": [
        {
        "id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S#k1",
        "type": "EcdsaSecp256k1VerificationKey2019",
        "controller": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
        "publicKeyMultibase": "z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
        }
    ],
    "authentication": [ "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S#k1" ]
}
```

Signatures:

```json
[
    {
        "verification_method_id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S#k1",
        "signature": "<valid signature string>",
        "clientSpec": {
            "type": "cosmos-ADR036",
            "adr036SignerAddress": "hid1f6r0x3pljpl7pe76zzv36l0ksztqmdlth7zdk5"
        }
    }
]
```

The DID Document will be registered since a valid signature is provided for `did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S#k1`

2. INVALID: A DID Document with valid signatures only for the verification method and not the non-subject controller.

Consider the following DID Document to be registered:

```json
{
    "id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
    "controller": [ "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S", "did:hid:z8HFqxxEADNubzkEH5HK3fZDTMSoti9VvC7yB8rtn6gkE" ],
    "verificationMethod": [
        {
            "id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S#k1",
            "type": "EcdsaSecp256k1VerificationKey2019",
            "controller": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
            "publicKeyMultibase": "z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
        }
    ],
    "authentication": [ "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S#k1" ]
}
```

Signatures:

```json
[
    {
        "verification_method_id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S#k1",
        "signature": "<valid signature string>",
        "clientSpec": {
            "type": "cosmos-ADR036",
            "adr036SignerAddress": "hid1f6r0x3pljpl7pe76zzv36l0ksztqmdlth7zdk5"
        }
    }
]
```

The DID Document will not be registered since no signature is provided for the controller `did:hid:z8HFqxxEADNubzkEH5HK3fZDTMSoti9VvC7yB8rtn6gkE`.

3. INVALID: A DID Document which has a CAIP-10 Blockchain Account ID in its `method-specific-id`, but the corresponding verification method is absent.

Consider the following DID Document to be registered:

```json
{
    "id": "did:hid:eip155:1:0x35A868a3e18514870407F722B243f0780d290A93",
    "controller": [ "did:hid:eip155:1:0x35A868a3e18514870407F722B243f0780d290A93" ],
    "verificationMethod": [
        {
            "id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S#k1",
            "type": "EcdsaSecp256k1VerificationKey2019",
            "controller": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
            "publicKeyMultibase": "z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
        }
    ],
}
```

Signatures:

```json
[
    {
        "verification_method_id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S#k1",
        "signature": "<valid signature string>",
        "clientSpec": {
            "type": "cosmos-ADR036",
            "adr036SignerAddress": "hid1f6r0x3pljpl7pe76zzv36l0ksztqmdlth7zdk5"
        }
    }
]
```

The DID Document will not be registered, since there is no verification method present which corresponds to the CAIP-10 Blockchain Account ID `eip155:1:0x35A868a3e18514870407F722B243f0780d290A93`.

4. VALID: A DID Document which has a CAIP-10 Blockchain Account ID in its `method-specific-id` and the corresponding verification method is present.

Consider the following DID Document to be registered:

```json
{
    "id": "did:hid:eip155:1:0x35A868a3e18514870407F722B243f0780d290A93",
    "controller": [ "did:hid:eip155:1:0x35A868a3e18514870407F722B243f0780d290A93" ],
    "verificationMethod": [
        {
            "id": "did:hid:eip155:1:0x35A868a3e18514870407F722B243f0780d290A93#k1",
            "type": "EcdsaSecp256k1RecoveryMethod2020",
            "controller": "did:hid:eip155:1:0x35A868a3e18514870407F722B243f0780d290A93",
            "blockchainAccountId": "eip155:1:0x35A868a3e18514870407F722B243f0780d290A93",
        }
    ],
}
```

Signatures:

```json
[
    {
        "verification_method_id": "did:hid:eip155:1:0x35A868a3e18514870407F722B243f0780d290A93#k1",
        "signature": "<valid signature string>",
        "clientSpec": {
            "type": "eth-personalSign",
            "adr036SignerAddress": ""
        }
    }
]
```

The DID Document will be registered, since there is a verification method present which corresponds to the CAIP-10 Blockchain Account ID `eip155:1:0x35A868a3e18514870407F722B243f0780d290A93`.

### Query DID

DID Documents can be queried using DID ID either through a gRPC or API call. The API layer is glued to the gRPC layer through `grpc-gateway`. A typical representation of a Query DID Call would look something like the following:

```js
QueryDIDRPC({
    didId: "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv"
})
```

Output of the above call:

```json
{
    "didDocument": {
        "id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
        "controller": [ "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv" ],
        "verificationMethod": [
            {
                "id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv#k1",
                "type": "Ed25519VerificationKey2020",
                "controller": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
                "publicKeyMultibase": "zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
            }
        ],
        "authentication": [ "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv#k1" ]
    },
    "didDocumentMetadata": {
        "created": "2023-04-19T02:16:00Z",
        "updated": "2023-04-19T02:16:00Z",
        "deactivated": false,
        "versionId": "5B8D61A575C81565E8D23A9A85FEED160FB004C6B3CEA815080AAEDA9D553C97"
    }
}
```

Output, if the DID Document is not registered:

```json
{
  "code": 3,
  "message": "DID Document did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv not found: didDoc not found: invalid request",
  "details": [
  ]
}
```

It should be noted that querying DID Document directly from Blockchain through gRPC/API endpoint IS NOT equivalent to Resolution of a DID Document. The responsibility for DID Document Resolution lies with the DID Resolver which will usually sit on top of the Blockchain.

### Update DID

To update a DID Document on the Hypersign Identity Network, an RPC call is made to the blockchain, which must include the following details:

- Updated DID Document.
- `versionId` of the updated DID Document.
- Signatures for verification of Verification Methods and Controllers.
- [Optional] `clientSpec` (Refer [here](#clientspec-overview))

A typical representation of a Update DID RPC call would look something like the following:

```js
UpdateDidRPC({
    didDocument: {
        "id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
        "controller": [ "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S", "did:hid:z9LcZGypnwjDxfqtqiFXAiKdQy67A4tpSkCDH2RhzNg4g" ],
        "verificationMethod": [
            {
            "id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S#k1",
            "type": "EcdsaSecp256k1VerificationKey2019",
            "controller": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
            "publicKeyMultibase": "z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
            }
        ],
        "authentication": [ "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S#k1" ]
    },
    signatures: [
        {
            "verification_method_id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S#k1",
            "signature": "<signature string>",
            "clientSpec": {
                "type": "cosmos-ADR036",
                "adr036SignerAddress": "hid1f6r0x3pljpl7pe76zzv36l0ksztqmdlth7zdk5"
            }
        },
        {
            "verification_method_id": "did:hid:z9LcZGypnwjDxfqtqiFXAiKdQy67A4tpSkCDH2RhzNg4g#k2",
            "signature": "<signature string>",
            "clientSpec": {
                "type": "",
                "adr036SignerAddress": ""
            }
        }
    ],
    versionId: "5B8D61A575C81565E8D23A9A85FEED160FB004C6B3CEA815080AAEDA9D553C97",
    creator: "hid1f6r0x3pljpl7pe76zzv36l0ksztqmdlth7zdk5",
})
```

**General Steps**

1. Validate every field in the input DID Document.
2. Check if the input DID Document exists in the Registry. If it does not, raise an error.
3. Fetch the registered DID Document from the Registry.  
  3.1. If the input DID Document and the registered DID Document are exactly the same, raise an error.  
  3.2. If the input `versionId` and the `versionId` from the registered DID Document do not match, raise an error.  
  3.3. If the registered DID Document is already deactivated, raise an error.
4. Collect existing Verification Methods and Controllers in the `OptionalGroup`, and the new Verification Methods and Controllers in the `RequiredGroup`.  
  4.1. Associate every input signature from `signatures` with its corresponding Verification Method.
  4.2. At least one signature from the `OptionalGroup` must be valid. If not, raise an error.  
  4.3. Every signature from the `RequiredGroup` must be valid. If not, raise an error.  
5. Save the updated DID Document in the Registry.


**Scenarios**

1. VALID: Signature is being provided for both existing as well as new controllers of the DID Document.

Consider an already registered DID Document:

```json
{
    "id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
    "controller": [ "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S" ],
    "verificationMethod": [
        {
        "id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S#k1",
        "type": "EcdsaSecp256k1VerificationKey2019",
        "controller": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
        "publicKeyMultibase": "z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
        }
    ],
}
```

Updated version of the registered DID Document (`did:hid:z9LcZGypnwjDxfqtqiFXAiKdQy67A4tpSkCDH2RhzNg4g` is added as the new controller):

```json
{
    "id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
    "controller": [ "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S", "did:hid:z9LcZGypnwjDxfqtqiFXAiKdQy67A4tpSkCDH2RhzNg4g" ],
    "verificationMethod": [
        {
            "id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S#k1",
            "type": "EcdsaSecp256k1VerificationKey2019",
            "controller": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
            "publicKeyMultibase": "z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S",
        }
    ],
}
```

Signatures:

```json
[
    {
        "verification_method_id": "did:hid:z2Aiw8DpgLVKG9DHngEZs65RkAjg7rTPNxfgYN1TeQeC7S#k1",
        "signature": "<signature string>",
        "clientSpec": {
            "type": "cosmos-ADR036",
            "adr036SignerAddress": "hid1f6r0x3pljpl7pe76zzv36l0ksztqmdlth7zdk5"
        }
    },
    {
        "verification_method_id": "did:hid:z9LcZGypnwjDxfqtqiFXAiKdQy67A4tpSkCDH2RhzNg4g#k2",
        "signature": "<signature string>",
        "clientSpec": {
            "type": "",
            "adr036SignerAddress": ""
        }
    }
]
```

The DID Document will be registered as valid signatures for both existing and new controllers were provided.

2. INVALID: Only the new Controller is providing a valid singature

This is similar to previous scenario, except that, we provide signature only for the newly added controller `did:hid:z9LcZGypnwjDxfqtqiFXAiKdQy67A4tpSkCDH2RhzNg4g`. The DID Document Update will fails since at least one valid signature is required from existing controllers or verification methods. 

3. VALID: Removal of an existing controller 

Consider an already registered DID Document:

```json
{
    "id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
    "controller": [ "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv", "did:hid:z9LcZGypnwjDxfqtqiFXAiKdQy67A4tpSkCDH2RhzNg4g" ],
    "verificationMethod": [
        {
        "id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv#k1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
        "publicKeyMultibase": "zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
        }
    ],
}
```

Updated version of the already registered DID Document (Removal of `did:hid:z9LcZGypnwjDxfqtqiFXAiKdQy67A4tpSkCDH2RhzNg4g` from the Controller List):

```json
{
    "id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
    "controller": [ "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv" ],
    "verificationMethod": [
        {
        "id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv#k1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
        "publicKeyMultibase": "zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
        }
    ],
}
```

Signatures:

```json
[
    {
        "verification_method_id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv#k1",
        "signature": "<valid signature string>",
        "clientSpec": {
            "type": "",
            "adr036SignerAddress": ""
        }
    }
]
```

The DID Document Update will be succeed since atleast one valid signature is provided for the existing controller `did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv`

4. INVALID: Non-Controller Verification Method attempts to update DID Document

Consider the following registered DID Document:

```json
{
    "id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
    "controller": [ "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv" ],
    "verificationMethod": [
        {
            "id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv#k1",
            "type": "Ed25519VerificationKey2020",
            "controller": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
            "publicKeyMultibase": "zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
        },
        {
            "id": "did:hid:z9LcZGypnwjDxfqtqiFXAiKdQy67A4tpSkCDH2RhzNg4g#k1",
            "type": "Ed25519VerificationKey2020",
            "controller": "did:hid:z9LcZGypnwjDxfqtqiFXAiKdQy67A4tpSkCDH2RhzNg4g",
            "publicKeyMultibase": "z9LcZGypnwjDxfqtqiFXAiKdQy67A4tpSkCDH2RhzNg4g",
        }
    ],
}
```

Updated version of the registered DID Document:

```json
{
    "id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
    "alsoKnownAs": [  "someAlternateName" ],
    "controller": [ "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv" ],
    "verificationMethod": [
        {
            "id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv#k1",
            "type": "Ed25519VerificationKey2020",
            "controller": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
            "publicKeyMultibase": "zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
        },
        {
            "id": "did:hid:z9LcZGypnwjDxfqtqiFXAiKdQy67A4tpSkCDH2RhzNg4g#k1",
            "type": "Ed25519VerificationKey2020",
            "controller": "did:hid:z9LcZGypnwjDxfqtqiFXAiKdQy67A4tpSkCDH2RhzNg4g",
            "publicKeyMultibase": "z9LcZGypnwjDxfqtqiFXAiKdQy67A4tpSkCDH2RhzNg4g",
        }
    ],
}
```

Signatures:

```json
[
    {
        "verification_method_id": "did:hid:z9LcZGypnwjDxfqtqiFXAiKdQy67A4tpSkCDH2RhzNg4g#k1",
        "signature": "<valid signature string>",
        "clientSpec": {
            "type": "",
            "adr036SignerAddress": ""
        }
    }
]
```

The DID Document Update will fail because `did:hid:z9LcZGypnwjDxfqtqiFXAiKdQy67A4tpSkCDH2RhzNg4g` is not part of Subject DID's Controller Group.

### Deactivate DID

To deactivate a DID Document on the Hypersign Identity Network, an RPC call is made to the blockchain, which must include the following details:

- DID Document ID.
- `versionId` of the DID Document which is meant to be deactivated.
- Signatures for verification of Verification Methods and Controllers.
- [Optional] `clientSpec` (Refer [here](#clientspec-overview))

A typical representation of a Deactivate DID RPC call would look something like the following:

```js
DeactivateDidRPC({
    didId: "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
    signatures: [
        {
            "verification_method_id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv#k1",
            "signature": "<signature string>",
            "clientSpec": {
                "type": "cosmos-ADR036",
                "adr036SignerAddress": "hid1f6r0x3pljpl7pe76zzv36l0ksztqmdlth7zdk5"
            }
        },
        {
            "verification_method_id": "did:hid:z9LcZGypnwjDxfqtqiFXAiKdQy67A4tpSkCDH2RhzNg4g#k2",
            "signature": "<signature string>",
            "clientSpec": {
                "type": "",
                "adr036SignerAddress": ""
            }
        }
    ],
    versionId: "7848DD18900C4DCBD622EA2A0F54EA93B38A67E759FA7CAEE330F50873E83A10",
    creator: "hid1f6r0x3pljpl7pe76zzv36l0ksztqmdlth7zdk5",
})
```

**General Steps**

1. Check if the DID Document exists in the Registry. If it does not, raise an error.  
2. Fetch the registered DID Document from the Registry.  
  2.1. Raise an error if the input DID Document is already deactivated.  
  2.2. Raise an error if the versionId of the input DID Document does not match the versionId of the registered DID Document.  
3. Collect all Verification Methods and controllers from the input DID Document and associate them with input signatures from signatures.  
  3.1. Raise an error if there is no signature for at least one of the Verification Methods or controllers.  
  3.2. Validate that at least one of the Verification Method's or controller's signatures is valid. If none are valid, raise an error.  
4. Deactivate the DID Document in the Registry by changing the deactivated value from `false` to `true`.

**Scenarios**

1. VALID: A DID Document with valid signatures of every Verification Method

Consider the following DID Document to be deactivated:

```json
{
    "id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
    "controller": [ "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv" ],
    "verificationMethod": [
        {
        "id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv#k1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
        "publicKeyMultibase": "zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
        }
    ],
    "authentication": [ "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv#k1" ]
}
```

Signatures:

```json
[
    {
        "verification_method_id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv#k1",
        "signature": "<valid signature string>",
        "clientSpec": {
            "type": "",
            "adr036SignerAddress": ""
        }
    }
]
```

The DID Document will be deactivated since a valid signature is provided for `did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv#k1`

2. VALID: A DID Document with valid signatures only for the Verification Method and not for the non-Subject Controller

Consider the following DID Document to be registered:

```json
{
    "id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
    "controller": [ "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv", "did:hid:z8HFqxxEADNubzkEH5HK3fZDTMSoti9VvC7yB8rtn6gkE" ],
    "verificationMethod": [
        {
            "id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv#k1",
            "type": "Ed25519VerificationKey2020",
            "controller": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
            "publicKeyMultibase": "zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv",
        }
    ],
    "authentication": [ "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv#k1" ]
}
```

Signatures:

```json
[
    {
        "verification_method_id": "did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv#k1",
        "signature": "<valid signature string>",
        "clientSpec": {
            "type": "",
            "adr036SignerAddress": ""
        }
    }
]
```

The DID Document will be deactivated since a valid signature is provided for `did:hid:zF4yj4PgS33z8Z2FdrPgnhZWgmi249tmx8LcxA13UopPv#k1` which is an existing controller.

## Reference Implementation

Refer [here](https://github.com/hypersign-protocol/hid-node) for Hypersign Identity Network Implementation

## Privacy Considerations

The Alphanumeric `method-specific-id` format was implemented to accomodate identifiers such as UUIDs or ENS styled domains (arnab.hid). It should be also noted that this could leave room for strings to be included in DID Id which may not be intended to be stored in Public Domain.

## Security Considerations

The Alphanumeric `method-specific-id` format can allow wallet addresses to pass through, and since the ownership of the `method-specific-id` cannot be proven, unlike CAIP-10 Blockchain Account ID rule, the DID Document will be registered regardless. This is also a reason why CAIP-10 Blockchain Account ID was introduced as rule, since it will have to go through the ownership check. Hence, it is advised to use the CAIP-10 Blockchain Account ID, if the intention is to link a wallet address with a DID Document.
