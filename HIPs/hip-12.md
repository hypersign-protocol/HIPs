---
hip: 12
title: Privacy Preserving cross-chain KYC using Zero Knowledge Proof and Verifiable credentials
authors: Pratap <prataphypermine.in>, Vishwas Anand Bhushan <vishwas@hypermine.in>
status: Draft
type: Standards Track
category: Core
created: 2023-05-23
---

## Introduction 

One of the reason people have always been skeptical to use Web3 or Blockchain technology (including cyrpto currencies), is that lack of regulations. Partly because, cryptocurrency usecase is very tightly coupled with this tech stack. There has always been a push back from regulatory bodies citing issues like money laundering and terror financing. Lately, many government bodies have understood that banning this usecase or the tech itself, is not the right solution to solve those problems. Instead, working on proper law and regulations which not only solve the above state problems but solve them in web3 way - with atmost privacy, data security and data ownership. Markets in Crypto-Assets (MiCA) being one such regulatory body of European Union (EU) has voted for the [law](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3725395) that allows providers of digital wallets and other crypto services to sell their products across the EU, if they register with national authorities. Which paves the way for usecases like [regulated token launches](https://news.law.fordham.edu/jcfl/2023/02/06/an-overview-of-the-eu-crypto-asset-regulatory-framework-mica/), identity based voting in DAOs (Vitalik's blog -  [ Moving beyon coin voting governance](https://vitalik.ca/general/2021/08/16/voting3.html)), [NFT Identity](https://delphilabs.notion.site/NFT-Identity-5a6c1afdce6646d6b21f1184502ae039) etc. 

## The Problem

Technically, token is nothing but a smart contract deployed on the blockchain network. The problem is, how can a smart contract verify that the wallet has KYCed before (or if the user belongs to allowed list of countries?) executing the transfer of tokens, as KYC data are private in nature and it should not be sent to a smart contract. Again KYC being one example of private data, in general we are talking about leveraging "argubly" any private data in the web3 on-chain world.  

## The Solution

**Zero Knowledge Proof and Self Sovereign Identity**

Advance cryptographic technology like zero knowledge proof (zkp) can help solve this problem by producing complex mathematical computational proof which can not only be sent to (and verified by) smart contract but also does not reveal any private information about the user. Marring zkp with Self sovereign identity (tech like decentralised identifier (DID) and verifiable credentials (VP)) brings "identity" to the zk stack making the entire system trust worthy and zk-based digital interaction more secure and seamless. 

A verifiable credential (VC with KYC data) may be issued to a user (controlling a DID) by a trusted issuer (controlling a DID). The user may then generate Verifiable Presentation (VP) and then use that VP to generate zk-proof which can be sent to a smart contract for verification. This proof does not reveal any private data but only the fact like "user is over 18 yo", "user is a millionair", "user blongs to whitelisted set of contries or has done KYC or not". The smart contract can verifies the proofs of data which was issued only by the trusted issuer easily.  

**Not just on-chain and multichain, cross-chain is the need**

For this system to scale, it has to be _"cross-chain"_. Cosmos, being a network of blockchains, which support application specific chains and has features like Interblockchain communication (IBC) for communication between these appchains, needs a system to verify these zk-proofs over IBC so that other app chain do not have to implement zero knowledge proofs contracts or have to deal with DID/VC infrastrcuture.  This offloads a lot of work, realted to zk and ssi, from app chains which help them focus on their business case. All they need to do is, enable the IBC communication on their chain.

## Implementation

At high level, there are 2 steps: 

-  1) **Credential Issuance**: Upon user's request, issuer issues a verifiable credential to the user. 
-  2) **zk-Proof generation from verifiable presentation**: When requested (by whitelist pool smart contract, deployed on service provier chain), the user generates the zk-proof locally in his brower using verifiable presentation and submits to the contract.
-  3) **zk-Proof verification over IBC from Hypersign chain**: The whitelist pool smart contract then ask zk-verifier contract, deployed on Hypersign chain, to verify the zk-proof over IBC. Upon succesful verification, the user wallet/DID gets whitelisted in whitelist pool smart contract

Refer to the basic architecture of wallet/DID whitelisting below for better visualization:

![high-level](/images/hip-12/241242630-9870a6c7-7c9f-4a0e-bbeb-88314b31372e.png)

> Note: For this HIP we will focus only on zk-KYC (point 1 and 2) and not one cross-chain aspects or IBC side of things. For cross-chain workflow in IBC, kindly checkout [HIP-13](https://github.com/hypersign-protocol/HIPs/blob/main/HIPs/hip-13.md).

## Key generation

For efficient zero knowledge proof we will use Baby Jubjub Elliptic Curve for key generation. We use this key generation algorithm for both issuer as well as user. 

Sample keys:

```js
{
  privateKeyHex: '80fbc4764a8b9dbebe9c595a58bc23341f3e2b433518b2a3d5542e1c0634df41',
  publicKeyXY: [
    3601875987071859785164473094754208325989693439543482614308943278702727342213n,
    4030833583793708930859668079380361125693267759142046773103484041304184075580n
  ]
}
```

Now, lets use BJJ keys to form our DID documents for issuers and users. 

Sample DID document with BJJ verification key (`BabyJubJubVerificationKey2021`)

```json
{
  "@context": "https://www.w3.org/ns/did/v1",
  "id": "did:hid:testnet:z543717GD36C5VSajKzLALZzcTakhmme2LgC1ywW1YwTM",
  "controller": "did:hid:testnet:z543717GD36C5VSajKzLALZzcTakhmme2LgC1ywW1YwTM",
  "verificationMethod": [
    {
      "id": "did:hid:testnet:z543717GD36C5VSajKzLALZzcTakhmme2LgC1ywW1YwTM#keys-1",
      "type": "BabyJubJubVerificationKey2021",
      "controller": "did:hid:testnet:z543717GD36C5VSajKzLALZzcTakhmme2LgC1ywW1YwTM",
      "publicKeyMultibase": "z543717GD36C5VSajKzLALZzcTakhmme2LgC1ywW1YwTM",
      "publicKey": [
        "3601875987071859785164473094754208325989693439543482614308943278702727342213",
        "4030833583793708930859668079380361125693267759142046773103484041304184075580"
      ]
    }
  ]
}
```
## Credential Issuance


Let's us say, user submits the following data to the issuer.

```json
{
  "@context": "http://schema.org/",
  "@type": "Person",
  "age": 25,
  "country": "USA",
  "name": "Alice"
}
```

The process for credential issuance is as follows: 

- The issuer "somehow" (out of scope of this demo) verifies the above data.
- Creates Sparse Merkle root (`rootHash`) of the `credentialSubject`.
- Then signs the `rootHash`  using [`EdDSA-BabyJubJub`](https://iden3-docs.readthedocs.io/en/latest/_downloads/a04267077fb3fdbf2b608e014706e004/Ed-DSA.pdf) signatureAlgorithm. The `EdDSA-BabyJubJub` uses [POSEIDON](https://eprint.iacr.org/2019/458.pdf) digestAlgoithm.

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://www.w3.org/2018/credentials/examples/v1"
  ],
  "credentialSubject": {
    "age": "25",
    "country": "0x557361",
    "id": "0x6bcc19d6dc56257574f8b347824bef8f58b38605a76313271e67b0ff4405391c",
    "name": "0x507261746170204d7269646861"
  },
  "rootHash": "f807b5f05b8b25556649bfe69d49429a8907c609d7b94452c42647f1558d6350",
  "id": "http://example.edu/credentials/3732",
  "issuanceDate": 1685346063440,
  "issuer": "did:hid:testnet:z543717GD36C5VSajKzLALZzcTakhmme2LgC1ywW1YwTM",
  "type": [
    "VerifiableCredential",
    "KYC"
  ],
  "proof": {
    "type": "BabyJubJubSignature2021",
    "credentialHash": "8804781371345579570033420413972109558958830340519668977613039209476413357007",
    "created": 1685346064774,
    "verificationMethod": "did:hid:testnet:z543717GD36C5VSajKzLALZzcTakhmme2LgC1ywW1YwTM#key-1",
    "proofValue": {
      "R8x": "683841881763791128752872915676462173916887923648823414160160391436784188064",
      "R8y": "17173979429575543432010350417565880360926574124383324745680518501585575713613",
      "S": "1742062914709679048193182798105485403319056250146959691115025294992925267680"
    },
    "proofPurpose": "assertionMethod"
  }
}
```

## zk-Proof generation from Verifiable Presentation

### Verifiable Presentation

User generates a Verifiable Presentation by signing a `challenge` given by the verifier using the same digital signature alorithm i.e [`EdDSA-BabyJubJub`](https://iden3-docs.readthedocs.io/en/latest/_downloads/a04267077fb3fdbf2b608e014706e004/Ed-DSA.pdf).

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://www.w3.org/2018/credentials/examples/v1"
  ],
  "type": [ "VerifiablePresentation" ],
  "verifiableCredential": [
    {
      "@context": [
        "https://www.w3.org/2018/credentials/v1",
        "https://www.w3.org/2018/credentials/examples/v1"
      ],
      "credentialSubject": {
        "age": "25",
        "country": "0x557361",
        "id": "0x6bcc19d6dc56257574f8b347824bef8f58b38605a76313271e67b0ff4405391c",
        "name": "0x507261746170204d7269646861"
      },
      "rootHash": "f807b5f05b8b25556649bfe69d49429a8907c609d7b94452c42647f1558d6350",
      "id": "http://example.edu/credentials/3732",
      "issuanceDate": 1685346063440,
      "issuer": "did:hid:testnet:z543717GD36C5VSajKzLALZzcTakhmme2LgC1ywW1YwTM",
      "type": [
        "VerifiableCredential",
        "KYC"
      ],
      "proof": {
        "type": "BabyJubJubSignature2021",
        "credentialHash": "8804781371345579570033420413972109558958830340519668977613039209476413357007",
        "created": 1685346064774,
        "verificationMethod": "did:hid:testnet:z543717GD36C5VSajKzLALZzcTakhmme2LgC1ywW1YwTM#key-1",
        "proofValue": {
          "R8x": "683841881763791128752872915676462173916887923648823414160160391436784188064",
          "R8y": "17173979429575543432010350417565880360926574124383324745680518501585575713613",
          "S": "1742062914709679048193182798105485403319056250146959691115025294992925267680"
        },
        "proofPurpose": "assertionMethod"
      }
    }
  ],
  "proof": {
    "type": "BabyJubJubSignature2021",
    "created": 1685346067490,
    "proofPurpose": "authentication",
    "verificationMethod": "did:hid:testnet:z8Fo8daHrZrQ4NtDZ9byYgrkEKqK43dkBNxorxpAEm3rj#key-1",
    "challenge": "123456",
    "proofValue": {
      "R8x": "2561850963774026906369122268554600740848407830063412592471185456413796082293",
      "R8y": "2102349270547407034807811296621127114422616298236933684247877651948314268415",
      "S": "2182387849842233664426242497656858314792124699911044877720633460859100800189"
    }
  }
}
```

### zk-Proof generation

The Verifiable presentation along with list of countries where exclusion (user should not belong to this list) has to proved and other private inputs are sent to the ZK-SNARK [circuit](https://github.com/hypersign-protocol/delphi-hackathon/blob/main/zkp/Hypersign_circuits/vp.circom) for proof generation.

![zk-kyc-attribute-membership-circuit](/images/hip-12/241677934-429f17c7-7dcd-4703-be67-284554d7238d.png)


The zk-proof generation  goes through bunch of checks like, `data integrity check`, `issuer signature checks`, `user's signature check`,`membership check` etc and produces `zk-proof` and `public signals`. The public signal contains either `0` or `1`. `0` if this users is not part the exculded country list and `1` if otherwise. 


### zk-Proof verification

Once `proof` and `public signal` is generated, it will be sent to zk verifier smart contract.

## Reference Implementation

Refer [here](https://github.com/hypersign-protocol/zk-ibc-experiment) for zero knowledge proof based KYC implementation

## Privacy Considerations

// TODO

## Security Considerations

// TODO

## References

- [BJJSignature2021 by iden3](https://iden3.github.io/BJJSignature2021/#abstract)
- [eip-2494: Baby Jubjub Elliptic Curve](https://eips.ethereum.org/EIPS/eip-2494)