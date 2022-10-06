---
hip: 6
title: Deterministic Did,DidDocument Generation/Verification using Web3-wallets (Metamask,keplr,etc)
author: Pratap Mridha [@pratap2018](https://github.com/Pratap2018)
status: Draft
type: Standards Track
category: SDK,Core
created: 2022-10-6
---

## Simple Summary
A proposal for deterministic generation of DIDs and DID Documents using wallets (Metamask, keplr, etc) and a standard for verification of DIDs and DID Documents.

## Abstract
This proposal is for deterministic generation of DIDs and DID Documents using wallets (Metamask, keplr, etc) and a standard for verification of DIDs and DID Documents. 
A user should be able to generate and verify a DID and DID Document using a wallet. The verification method attached to the DID Document should be able to verify the DID and DID Document by any other system that supports the verification method. Example- HID-Network (currenty supports `Ed25519VerificationKey2020`).  

## Motivation
The motivation behind this proposal is to make the DID and DID Document generation and verification process more user friendly. Right now Hypersign-Identity-Network only supports `Ed25519VerificationKey2020` verification method but web3 wallet such as keplr uses `secp256k1` algorithm for signing and verification. So its not possible to use the same wallet for Did related signing/verification and for blockchain of transactions. So right now our users have to use two different wallets ([HD-Ed25519](https://github.com/hypersign-protocol/hid-ssi-js-sdk/blob/ed25519-hd-poc/test/ssi/ed25519_hd_poc.js) for Identity Wallet, secp256k1 for blockchain) for signing/verifying DIDs and for signing/verifying blockchain transactions. This proposal will make it possible to use the same wallet for both DID and blockchain transactions. The main outcome of this propsal is smooth user on bording experience.

## Specification

#### Currently Supporting Specification:
- `Ed25519VerificationKey2020` for verification: [Link](https://github.com/digitalbazaar/ed25519-verification-key-2020)
- `Ed25519Signature2020` for signature: [Link](https://github.com/digitalbazaar/ed25519-signature-2020)
#### After this proposal is implemented we will be supporting the following specifications:
- `EcdsaSecp256k1VerificationKey2019` for verification: [Link](https://www.w3.org/TR/did-spec-registries/#ecdsasecp256k1verificationkey2019) 
- `EcdsaSecp256k1Signature2019` for signature: [Link1](https://w3c-ccg.github.io/lds-ecdsa-secp256k1-2019/) [Link2](https://github.com/w3c-ccg/lds-ecdsa-secp256k1-2019)
#### Other Links:
- Crypto Suties Registry [Link](https://w3c-ccg.github.io/ld-cryptosuite-registry/)
- ecdsa256k1 Packge [Link](https://www.npmjs.com/package/@bloomprotocol/ecdsa-secp256k1-verification-key-2019)

This is still in draft stage. I will update this section once the proposal is finalized.


## Rationale
The rationale behind this proposal.
- As our Hypersign-Identity-Network only supports `Ed25519VerificationKey2020` verification method, we had to come up with a way to generate Did and DID Document using `Ed25519` Deterministicaly. So if a user remembers the seed phrase, he/she can re-generate the same DIDs and DID Documents.So there is no need to store the Did docs on the blockchain. It will work as private Did. [POC](https://github.com/hypersign-protocol/hypersign-knowledge-hub/tree/poc-HD-did/kepler-hs-ssi-sdk) 
- Right now our Sdk can only be used by the wallet providers (such as if keplr/other-wallet uses hid-ssi-sdk, they can provide Identity features to their users). Now our sdk has potential to be used by the cosmos chain wallets. So the cosmos enabled wallets can provide Identity features to the cosmos chain users. If wandering how ? our sdk will be able to use the same mnemonic of the wallet(using hid-ssi-sdk) to generate Did and DidDocument deterministically (`HD-Ed25519`).
- After achieving the above step, Now we propose a approch to generate DIDs and DID Documents using the publicKey of `secp256k1` wallets (keplr,Metamask). This will make it possible to use the same wallet for both DID and blockchain transactions. The user dosent have to provide seed phrase/mnemonic to generate DIDs and DID Documents. The user can use existing web3 wallet so the Did related task and blockchain related task can be done using the same wallet (Web3-Wallet/keplr).By attaching the `EdcsaSecp256k1VerificationKey2019` verification method to the DID Document this scenario can be possible.(For that Hypersign-Identity-Network has to support `EdcsaSecp256k1VerificationKey2019` verification method). 


## Privacy Concerns
- The user has to provide the seed phrase/mnemonic to generate DIDs and DID Documents. So the user has to trust the daap/provider with the seed phrase/mnemonic.
- If any wallet provider such as keplr/Metamask uses our sdk, the user alreay trusts them as relalible wallet provider.

## Backwards Compatibility
This proposal is backward compatible. The current DID and DID Document generation and verification process will work even after the acceptance of this proposal.

## Reference Implementation

[POC : Hiarchical Deterministic (HD) Ed25519 Did and DidDoc generation and verification](https://github.com/hypersign-protocol/hypersign-knowledge-hub/tree/poc-HD-did/kepler-hs-ssi-sdk) 


