---
hip: 11
title: EthereumEip712Signature2021
author: Pratap Mridha <https://github.com/Pratap2018>
status: Draft
type: Standards Track
category: EthereumEip712Signature2021, DID-AUTH, VC, VP
created: 2023-05-10
---

## Simple Summary
This HIP defines a cryptographic suite for the purpose of creating, verifying proofs for EIP712 signatures in conformance with the Ethereum Improvement Proposal [EIP712](https://eips.ethereum.org/EIPS/eip-712) as well as the Data Integrity [DATA-INTEGRITY](https://w3c.github.io/vc-data-integrity/).

## Abstract
In the world of SSI the issue is keyPair management for the naive users. This HIP avoids the problem by using a web3 ethereum Wallet(Metamask) for signing adn presentation of credentials. This HIP also defines a cryptographic suite for the purpose of creating, verifying proofs for EIP712 signatures in conformance with the Ethereum Improvement Proposal [EIP712](https://eips.ethereum.org/EIPS/eip-712) as well as the Data Integrity [DATA-INTEGRITY](https://w3c.github.io/vc-data-integrity/). 

## Motivation
The motivation for this HIP is to provide a simple and easy to use cryptographic suite for the purpose of creating, verifying proofs for EIP712 signatures in conformance with the Ethereum Improvement Proposal [EIP712](https://eips.ethereum.org/EIPS/eip-712) as well as the Data Integrity [DATA-INTEGRITY](https://w3c.github.io/vc-data-integrity/).

## Definitions
  Ethereum EIP712 Signature 2021 
    Suite Defination : https://w3c-ccg.github.io/ethereum-eip712-signature-2021-spec/#suite-definition

| Parameter                                                                       |          Value          |                                     Specification |
| ------------------------------------------------------------------------------- | :---------------------: | ------------------------------------------------: |
| canonicalization algorithm (if automatically-generated types object is present) |           JCS           |        [JCS](https://tools.ietf.org/html/rfc8785) |
| message digest algorithm                                                        | EIP712 uses Keccak-256  |  [EIP712](https://eips.ethereum.org/EIPS/eip-712) |
| signature algorithm                                                             | EIP712 uses ECDSA K-256 | [EIP712] (https://eips.ethereum.org/EIPS/eip-712) |

## Specification
This Suites defines the flow of the EIP712 Signature and the Data Integrity Signature. The flow is as follows:
    
1. Types Generation:  https://w3c-ccg.github.io/ethereum-eip712-signature-2021-spec/#types-generation. WARNING: The current algorithm definition does not support auto generating types for arrays of structs. We need to work on that. but the Implementation [EthereumEip712Signature2021Suite](https://github.com/hypersign-protocol/EthereumEip712Signature2021Suite) does support Array of struct ([L55](https://github.com/hypersign-protocol/EthereumEip712Signature2021Suite/blob/main/src/TypedData/Eip712Types.ts#L55)) but the condition is the struct has to be symmetric.
2. Supported Verification Method: This signature suite does not define a new verification method type. The following verification method types can be used with Ethereum EIP712 Signature 2021 ([Ref](https://w3c-ccg.github.io/ethereum-eip712-signature-2021-spec/#verification-method)): 
   - EcdsaSecp256k1VerificationKey2019
   - EcdsaSecp256k1RecoveryMethod2020
   - JsonWebKey2020
3. Data Canonicalization: Canonicalization is the process of converting data that has more than one possible representation into a "standard" canonical representation. This can be done to compare different representations for equivalence, to count the number of distinct data structures, to improve the efficiency of various algorithms by eliminating repeated calculations, or to make it possible to impose a meaningful sorting order .For this suite the data is converted to JSON Canonicalization Scheme.   [JCS](https://github.com/hypersign-protocol/JCS) [RFC 8785](https://www.rfc-editor.org/rfc/rfc8785)
4. Proof Representation: Explained Here ->[Link](https://w3c-ccg.github.io/ethereum-eip712-signature-2021-spec/#ethereum-eip712-signature-2021)   
Example 1
```json
 {
   "proof": {
     "type": "EthereumEip712Signature2021",
     "created": "2019-12-11T03:50:55Z",
     "proofPurpose": "assertionMethod",
     "proofValue": "0xc565d38982e1a5004efb5ee390fba0a08bb5e72b3f3e91094c66bc395c324f785425d58d5c1a601372d9c16164e380c63e89f1e0ea95fdefdf7b2854c4f938e81b",
     "verificationMethod": "did:example:aaaabbbb#issuerKey-1",
     "eip712": {
        "types": "https://example.com/schemas/v1",
        "primaryType": "VerifiableCredential"
     }
   }
}
```
Example 2
```json
{
  "created": "2021-08-30T13:28:02Z",
  "eip712": {
    "domain": {
      "name": "Test",
    },
    "primaryType": "Document",
    "types": {
      "Data": [
        {
          "name": "job",
          "type": "Job",
        },
        {
          "name": "name",
          "type": "Name",
        },
      ],
      "Document": [
        {
          "name": "@context",
          "type": "string[]",
        },
        {
          "name": "@type",
          "type": "string",
        },
        {
          "name": "data",
          "type": "Data",
        },
        {
          "name": "telephone",
          "type": "string",
        },
        {
          "name": "proof",
          "type": "Proof",
        },
      ],
      "Job": [
        {
          "name": "employer",
          "type": "string",
        },
        {
          "name": "jobTitle",
          "type": "string",
        },
      ],
      "Name": [
        {
          "name": "firstName",
          "type": "string",
        },
        {
          "name": "lastName",
          "type": "string",
        },
      ],
      "Proof": [
        {
          "name": "created",
          "type": "string",
        },
        {
          "name": "proofPurpose",
          "type": "string",
        },
        {
          "name": "type",
          "type": "string",
        },
        {
          "name": "verificationMethod",
          "type": "string",
        },
      ],
    },
  },
  "proofPurpose": "assertionMethod",
  "canonicalizationHash":"5c78f22ed4f5617ed2cfe8feec7a14ec9d23e7fa28ffc70291d141de169844f7",
  "proofValue": "0xcf5844be1f1a5c1a083565d492ab4bee93bd0e24a4573bd8ff47331ad225b9d11c4831aade8d071f4abb8c9e266aaaf30612c582c2bc8f082b8788448895fa4a1b",
  "type": "EthereumEip712Signature2021",
  "verificationMethod": "did:hid:testnet:0xAED7EA8035eEc47E657B34eF5D020c7005487443#blockchainAccountId#key-1",
}
```

## Privacy Concerns
User Should not share private key or mnemonic over the network.
## Goals
- [x] Support for EIP712 Signature
- [x] Support for Data Integrity Signature
- [x] Support for EIP712 Signature with VC
- [x] Support for EIP712 Signature with VP 
- [x] DidAuth 
- [x] Interoperability 
## Reference Implementation
- [EthereumEip712Signature2021Suite](https://github.com/hypersign-protocol/EthereumEip712Signature2021Suite)