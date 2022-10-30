---
hip: 7
title: HIP 7 - Universal Wallet credential exchange specification
author: Pratap Mridha (https://github.com/Pratap2018)
status: Draft
type: Standards Track
category: Universal Wallet (Identity & Credentials)) 
created: 2022-10-18
---

## Simple Summary
Proposal for standardising the specifications for credentials and  credential exchange between two or many parties using Universal Wallet.

## Abstract
hypersign-identity-wallet is a universal wallet for storing credentials and exchanging credentials between two or many parties. This specification is a proposal for standardising the specifications for credentials and  credential exchange between two or many parties using Universal Wallet. 


## Motivationd
The motivation behind this proposal is to standardise the specifications for credentials and  credential exchange between two or many parties using Universal Wallet. This will help in interoperability between different wallets and also help in building a common framework for exchanging credentials between different parties.

## Specification
The specification for the  credential is divided into two parts. The first part is the specification for the credentials and the second part is the specification for the credential exchange between two or many parties.

### Credential Specification
The credential specification is divided into two parts. The first part is the specification for the credential and the second part is the specification for the credential exchange between two or many parties.

#### Credential
The credential is a JSON-LD object that contains the following fields accourding to w3c specification:

Three types of credential formats 
1. Verifiable Credential (w3c)
2. Blockcerts
3. JWT

[context VC](https://www.w3.org/2018/credentials/v1)

[context Blockcerts](https://w3id.org/blockcerts/v2)

[Read More](https://www.w3.org/TR/vc-data-model-2.0/#basic-concepts)

[Issue Credential Protocol 1.0 Aries](https://github.com/hyperledger/aries-rfcs/blob/main/features/0036-issue-credential/README.md) 

[Issue Credential Protocol 2.0 Aries Very Important](https://github.com/hyperledger/aries-rfcs/blob/main/features/0454-present-proof-v2/README.md)

[Credential issuence subject and holder relationship](https://www.w3.org/TR/vc-data-model/#subject-holder-relationships)


- `@id` - The unique context identifier for the credential .
- `@context` - The JSON-LD context that defines the terms used in the credential. 
- `id` - A unique identifier for the credential.
- `type` - A type for the credential.
- `issuer` - The entity that issued the credential.
- `issued` - The date the credential was issued.
- `issuanceDate` - The date the credential was issued.
- `expirationDate` - The date the credential expires.
- `credentialSubject` - The subject of the credential.
- `proof` - A cryptographic proof that the credential has been issued by the issuer.
- `credentialStatus` - The status of the credential.
- `holder` - The holder of the credential.
- `credentialSchema` - The schema that the credential conforms to.
- `evidence` - Evidence for the credential.
- `termsOfUse` - Terms of use for the credential.
- `refreshService` - A service that can refresh the credential.
- `validFrom` - The date the credential becomes valid.
- `validUntil` - The date the credential becomes invalid.
- `refreshService` - A service that can refresh the credential. [Read More](https://www.w3.org/TR/vc-data-model/#advanced-concepts)

![CredStruct](./images/credential%20struct.png)

#### Credential Exchange/ Presentation Exchange
Regardless of the format of a verifiable credential, interoperability still depends on
how that credential is exchanged. This involves any number of complex protocol
questions. For example:
- Should potential verifiers reach out to holders proactively, or should they wait
to challenge them when holders attempt to access protected resources?
- How do verifiers ask for claims that may be in different credentials?
- Can verifiers add filters or qualifications to their proof requests, such as only
accepting claims from specific issuers or only if a credential was issued before or
after a specific date?
- Can verifiers contact a party other than the holder to get credential data?

This is where Layer 3 has a direct dependency on Layer 2. For example, if you think of
credentials primarily as inert data and believe the best way to distribute identity data is
through hubs, then a natural way to exchange that data might be to call a web API and
ask that the data be served to you. A credential holder could leave the credential on
the hub, with a policy that tells the hub what criteria must be met before serving it to
anyone who asks. This is the general paradigm embodied in the approach taken by
credential-serving APIs being incubated for DIF identity hubs (https://identity.foundation/hub-sdk-js). ([Read More Page 106](https://www.oreilly.com/library/view/self-sovereign-identity/9781617296598/))   
([DIF](https://identity.foundation/credential-manifest/))

Another approach is to use DIDComm, a protocol for secure communication between parties, where a verifier can send a presentation request to a holder, and the holder can respond with a credential/presentation . This is the approach taken by the Hyperledger Aries project. [Presentation Exchange](https://identity.foundation/presentation-exchange) 

[Presentation Definition](https://identity.foundation/presentation-exchange/#presentation-definition)

[Presentation Submission](https://identity.foundation/presentation-exchange/#presentation-submission)

[Features](https://identity.foundation/presentation-exchange/#features) : Features enable Verifiers to express, and Holders to support, extended functionality (relative to the base objects) by defining one or more properties on one or more objects.





## Rationale
The rationale behind this proposal is to standardise the specifications for credentials and  credential exchange between two or many parties using Universal Wallet. This will help in interoperability between different wallets and also help in building a common framework for exchanging credentials between different parties.

## Privacy Concerns
Will update soon

## Backwards Compatibility
will update soon

## Reference Implementation
will update soon

## Security Considerations
will update soon


## Reference 



[Peer DID](https://identity.foundation/peer-did-method-spec)

[Credentials](https://www.w3.org/community/credentials/)

[Use case Credential move wallet to wallet](https://www.w3.org/TR/2019/NOTE-vc-use-cases-20190924/#store-move-claim)

[vc-implementaion reports](https://w3c.github.io/vc-test-suite/implementations/)

[vc-data-model](https://www.w3.org/TR/vc-data-model/)

[vp req spec 2.0](https://w3c-ccg.github.io/vp-request-spec)

[presentation exchange](https://identity.foundation/presentation-exchange/)

[universal wallet spce](https://w3c-ccg.github.io/universal-wallet-interop-spec/)

[DID Comm (v2) ](https://didcomm.org/book/v2/)

[DID Comm (v2) Premier](https://www.youtube.com/watch?v=TBxWgNmsnvU)


[universal wallet experimental ](https://github.com/transmute-industries/universal-wallet)





