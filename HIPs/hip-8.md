---
hip: 8
title: Did Comm / Connection Spec
author: Pratap Mridha <https://github.com/Pratap2018>
status: Draft
type: Standards Track
category: Universal Wallet Conection , DID Communication
created: 2023-04-04
---

## Simple Summary
Did Communication is a protocol for connecting two agents and exchanging secure information over a channel ,i.e. VC,VP . This protocol is independent of any specific transport for messages such as HTTP, WebSockets, Bluetooth, NFC, or any other communication channel. This protocol is agnostic to the message format, and it is possible to use different formats for different purposes. This protocol is also agnostic to the message types and it is possible to define new types.

## Abstract

Universal wallets are required to share the credentials with the verifiers and the verifiers are required to verify the credentials. The communication between the universal wallet and the verifier is done using the DID communication protocol.


## Motivation
 DIDComm Messaging defines how messages compose into the larger primitive of application-level protocols and workflows, while seamlessly retaining trust. “Built atop … DIDs” emphasizes DIDComm’s connection to the larger decentralized identity movement, with its many attendent virtues.

Of course, robust mechanisms for secure communication already exist. However, most rely on key registries, identity providers, certificate authorities, browser or app vendors, or similar centralizations. Many are for unstructured rich chat only — or enable value-add behaviors through proprietary extensions. Many also assume a single transport, making it difficult to use the same solution for human and machine conversations, online and offline, simplex and duplex, across a broad set of modalities. And because these limitations constantly matter, composability is limited — every pairing of human and machine for new purposes requires a new endpoint, a new API, and new trust. Workflows that span these boundaries are rare and difficult.

## Specification

KeyAgreement :  

- X25519KeyAgreementKey2020 is used to facilated the encryption and decryption of the didComm message 
- JsonWebKey2020 can be used for signing and verification of the didComm message
- Ed25519VerificationKey2018 can be used for signing and verification of the didComm message


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
Secret Keys should be stored in secure storage and should be used only when required.(decrypting the message and signing the message), user Should own and control the keys.

## Goals
Primary goals that can be achived by didcomm
1. Non-repudiation of the sender and Encryption of the message content (sender canot deny sending the message)
2. Repudiation of the sender but Authenticated encryption of the message content (the sender can deny sending the message)
3. Repudiation of the sender and UnAuthenticated encryption of the message content (the sender can deny sending the message)
4. Signed Unencrypted message
5. Plaintext message

Result : 
=================== NON REPUDIABLE ENCRYPTION ===================

Encryption metadata is

 ```js
  {
  messaging_service: null,
  from_kid: 'did:hid:testnet:zGY2t3arVXjsn7mi24xowiLiwHQCErzKve8YzHXo2ER9f#z6LScqAc6RHPvxN2WabKDGfhy7f4qaNK3unCxZFKdRbzrdpX',
  sign_by_kid: 'did:hid:testnet:zGY2t3arVXjsn7mi24xowiLiwHQCErzKve8YzHXo2ER9f#key-1',
  to_kids: [
    'did:hid:testnet:zB23yfoioAkThgKxumcGSeWazCXvgJWxT6fK3B9nHweg8#z6LSoGabWQXPgu85wr9C5n8PrqAWvUTiYRX9RU28YksHow1U'
  ]
}

```
Sending message
```
 {"protected":"eyJ0eXAiOiJhcHBsaWNhdGlvbi9kaWRjb21tLWVuY3J5cHRlZCtqc29uIiwiYWxnIjoiRUNESC1FUytBMjU2S1ciLCJlbmMiOiJYQzIwUCIsImFwdiI6IjJSbmx4SlhUX3g1REtFSk1QQnU2akE5clNSUVJ2SmhsWVF1N2d1aDd0UVEiLCJlcGsiOnsiY3J2IjoiWDI1NTE5Iiwia3R5IjoiT0tQIiwieCI6IkR5UHo5MUdMVEJvb2drN3pfM0FfR2syM182ZnFiR3RkQWtfYTFOcFNlSDAifX0","recipients":[{"header":{"kid":"did:hid:testnet:zB23yfoioAkThgKxumcGSeWazCXvgJWxT6fK3B9nHweg8#z6LSoGabWQXPgu85wr9C5n8PrqAWvUTiYRX9RU28YksHow1U"},"encrypted_key":"oEsO2N7VdQPJsPO7ib1FkaEAPptNTqbmxyFgw3HEKnB9wQd5HjSl9A"}],"iv":"7DRBtlsLq56UPn9JrVQQllNKKb44FwbG","ciphertext":"9oFT8k-gy5QxHls_GpDV-VKyDxIvDlq4cD9-jo0cuOwCan6JLvDKz1pVzgWzqi1IaCP9G7e33EHHrazWVcI_yNsoCkCgKcYmgPUKtwA2FauX_3ztpkX8u48YhrQWX3NR-Z7Gug_IvY-7xBkiRGgc5iDNTUbopzMkIiD1fR-kmtTmwDdBP8e9xhBUgYA7Z5DQNbIgKWuMhiG3vEloORIT1lHMWIY4Owby9VDAbErMYqpg5Zys44--9g7GyqUOMQG0MJQUR3F-tp7cbHc2r-TFwOBvqWrT9xeQor9JzyfSCwXTTfDOx2Tjd8i7o1_SMPHiZKkduORhXLA1P-j2EkQgxtXRMks5MqhvCPyfJ4Ea1k0qkmx_OAd6YULc6PIEWVLIdh-4YhogVBDb5EEM1AcSQ-Baf1ACWmXIEsX8heh7J3zL7Cl4aiST0ENitrfKwQmCd7LVVb4f-adUS60orOPA-UtQdDyDyWMVH98TDSwPyY3aL7jJ-82CbZI6veKjH6vloKd-1S3rZ6tItR3C9cz55FNY6cRCNcEMAHddfNhIllS7hKoI6k-VCqEH1P_CU6dueSwcvtOhw9BTL5DasxGis5rRlBXM19FUer7WE4cXOy7_Tf0FL_QLsxgYWcAIUr0KSWeVX7hNg44imAugPrW6_Y8B2nYgpBCI3LFqMWKdAUXTON-QT1k1Zo8wS2oy2g8Jvb9qktdNIgFnGHy4K7x8HFKlDHz9sAMgmsg3uiqdUBFyPi7VSIZQxzU13yD5kOEvG--Pi_u4kI8IzDIZv2YsnSonHKuT_sGHBqu7YC4q9TsX8KuNLAa6B93wNIHdg23HJdSXG3yGyNJRvZ5G5YtmFSXmmSxa9JdC9_ApJad5yUMQAuLMOb5Pj-tnNWFsEgApULijqSvnUdkd-n_Ldh1rHBqwGFC47ORGAFvWHx0xZms22fTr7_9V3EGtVWJ4YB1l0iLfDl1AVYh_oS--BVa00KO1rg8V-hAd2yfZsx7jb1AK5lTVXppSrn9mfioLvII_viI21cmU5euVpe2jJ9-aN40UAH-4xVqME0rVlFOhtMRqqKj0dFll__Oqqaje-5ZL2PA-4k-mmXWoo6zwi0QwlakFvAjJvJYdh9bbBHlCs7XUwdrDYmxAI_Vcrv5SNjUFSlDoJQ3yJjRp4Y3wTZvduy1UDqqvv5M7HVxLkeS4CCpw_gAccTjfcxCvY2A4XNW8jc5jZl7KI3DQ8uT4al6IEw_XQOkW97AxSE3RVpio3yOlVuAVFCRyTHp98Bu9CH5pwP6E2RWbcYUJVNK6_ql58-l5TnEcegTDsSN_Ge3mRpr3KygisBeaNudHgnb3O4UEw0PqD1D30KMSikNoJ87XIIOcV_-Y6vELVj9lNCMH7nE_1hv7u5w6WIq6zVGxiRxInpZpDUn46fvZhxbUJgW8AE944tlpsfY-3uzJ-7jCY_zZ1xH1T4EEIdyFjTMWf0kX42Q_N94lLLZhtVHbY3eIF2My-NIewTnRB5JcsS0_gkKunnHvsnRZ0Pt_2mu8z91UqdX3nFdBxQMWSI8mNHvNy61vya8nPZYB1dqOgXVEfg9jl2rAJUWPF2PlMZwqNeWuayPu84bGy-Lk5MJLbExbJFx0FdoKymyKbGddf9W_teq_W0i-4gS8sAbljwXFlOlzza6y4RceM5s6UM5ZWRKXKxxANuXSqK_Mk77gZRi7l6QTWucwYu7NNbaDZjxBypWl4lrG8GA3Sy7uG6nLzUiH-Dtee24n3Lh9x60vvdfX4DKiqgN4NNvZnxHVZNUcEjjpNLvqBwnXqj75QhpwWEIZ8oiLThHMH71jdGyoyjAvrVUkqE-zXO3UMSSkNxs-Dot05qMwkI_sp1bWlSvZQj0cD71Jn7NIsTQH7gT7e-7IQ1m69fGccPgaGJGqmTGI4p4mQQevdn33NxR_2KfRS_cbCSFjMPDhJYru7r4PxnFGG8OQ9wSqxcyxCmAEJK85YUYi3TYoGGTVEIBz6Clfn0FDvhuFiR919MaLQ_uKm5YGjYsUz6AbpDUaiyrS94IvTQBbbFWh4vpgPsgQCSN0wKpGrPX_lxkZKDpK1t91iTFCFbu5-FgKeB7dDt-v65p5Ik94hMTikDQH4d3KpQG5Zm8PVQa6BshZhQsKf3ksrN2edJXbwX7kVqUhpvWvW-dUBsR9F3kVlOuYBEJ2mwpvUYnMMPXDjHNEWYlbjbswxFpC0OojMS92zxgC1kC5gng61cUljIbx5ORj8BUe9GWV-SV16QeyFP5FJEkU-ocGvmBjkcHRVooKgNFRPlQoHJe2O7B6UVJkhvBBZwRHsOBNG86WoGlOC8d_CEm87jSxe-LhqJAmM-uO3bnyv-uVsk7KDY_7G0U0sSPMnJTEqhspA4hDp4evF2aJtE-jdeB50em_mqTJMRHuEzQaodfVbaaBtbyRh5HLyo7OVpJhX36nYJa-Xs_5Za2RGGiJMPb8TsOt9mwgmyz9tPewfY2WfyaDdk9O4Jw_ylnWuEjcYknpe5bmjs9KPGErDrdhnlgBo037OxgA8zImZ5FD44dW_6GJRf_NLqZIqK56yBajay6RSckj_qNQKTb1PTlxJzWA5MlTBpjBj8-11NqZcqzy26SBQ-3UE2cDneF1pZ4K6hVwpfNfMQ4jOdA6tlSTeqVOeU1vJAyvhoKBCmAAEAqHP4VUkXTK86_nQUoxH6fvP7CYcXw5RoItQb_in6mA2JksWq35OzZYYQ_IHAdWv6sr7EDtF-I7cI1j82upcYZ1IAIXTqEh-cYz3OA5d_nJ1hsJcxK98lN8sZOoR-qmbYLh1XTh6rofG_N9qH2RfJ2We_6-X3NSFtQcc82YG8o4U9tbPFPakrtAtBQvctLDHGy_6MvnzkzyhbMg4t9g2TNhQpVKcrFMhKdn2rrI_pIftcgoS6dXceAtPysLvAicPTQttnN68ss1O9EPbF8Kzyh8PJ1pJu6ZT1xtJD42BCD82-CfReSsFZTlsWPbGaKUIMXlyNqLNN-Vf1Yj42_M7W2dwBWYrBuN5Edz1JNE4iGf71U5OyDK8Dz1Zh0fXafqumtCSpEAZvfdWzInEalOq5NQTUTFJyxcesrvcZ_db5o","tag":"iv9RpxiIMv8lt4tAzELpFg"}
 ```
Reveived message is
```js
 {
  id: '1234567890',
  typ: 'application/didcomm-plain+json',
  type: 'http://example.com/protocols/lets_do_lunch/1.0/proposal',
  body: { data: 'data' },
  from: 'did:hid:testnet:zGY2t3arVXjsn7mi24xowiLiwHQCErzKve8YzHXo2ER9f',
  to: [ 'did:hid:testnet:zB23yfoioAkThgKxumcGSeWazCXvgJWxT6fK3B9nHweg8' ],
  created_time: 1680090068557,
  expires_time: 1680694868557,
  attachments: [
    {
      data: [Object],
      id: '123',
      description: 'example',
      media_type: 'application/didcomm-encrypted+json'
    }
  ]
}
```
Reveived message unpack metadata is
```js
 {
  encrypted: true,
  authenticated: true,
  non_repudiation: true,
  anonymous_sender: true,
  re_wrapped_in_forward: false,
  encrypted_from_kid: 'did:hid:testnet:zGY2t3arVXjsn7mi24xowiLiwHQCErzKve8YzHXo2ER9f#z6LScqAc6RHPvxN2WabKDGfhy7f4qaNK3unCxZFKdRbzrdpX',
  encrypted_to_kids: [
    'did:hid:testnet:zB23yfoioAkThgKxumcGSeWazCXvgJWxT6fK3B9nHweg8#z6LSoGabWQXPgu85wr9C5n8PrqAWvUTiYRX9RU28YksHow1U'
  ],
  sign_from: 'did:hid:testnet:zGY2t3arVXjsn7mi24xowiLiwHQCErzKve8YzHXo2ER9f#key-1',
  from_prior_issuer_kid: null,
  enc_alg_auth: 'A256cbcHs512Ecdh1puA256kw',
  enc_alg_anon: 'Xc20pEcdhEsA256kw',
  sign_alg: 'EdDSA',
  signed_message: '{"signatures":[{"header":{"kid":"did:hid:testnet:zGY2t3arVXjsn7mi24xowiLiwHQCErzKve8YzHXo2ER9f#key-1"},"protected":"eyJ0eXAiOiJhcHBsaWNhdGlvbi9kaWRjb21tLXNpZ25lZCtqc29uIiwiYWxnIjoiRWREU0EifQ","signature":"8K1qyel-Bp8b4jLDkxc8xHlmMWR0sXMKngwLDnF8EVsdQysOxrr0Tv3O7CmMhgXWfP67Eh2XnrnD-Fp-ZGOvAA"}],"payload":"eyJpZCI6IjEyMzQ1Njc4OTAiLCJ0eXAiOiJhcHBsaWNhdGlvbi9kaWRjb21tLXBsYWluK2pzb24iLCJ0eXBlIjoiaHR0cDovL2V4YW1wbGUuY29tL3Byb3RvY29scy9sZXRzX2RvX2x1bmNoLzEuMC9wcm9wb3NhbCIsImJvZHkiOnsiZGF0YSI6ImRhdGEifSwiZnJvbSI6ImRpZDpoaWQ6dGVzdG5ldDp6R1kydDNhclZYanNuN21pMjR4b3dpTGl3SFFDRXJ6S3ZlOFl6SFhvMkVSOWYiLCJ0byI6WyJkaWQ6aGlkOnRlc3RuZXQ6ekIyM3lmb2lvQWtUaGdLeHVtY0dTZVdhekNYdmdKV3hUNmZLM0I5bkh3ZWc4Il0sImNyZWF0ZWRfdGltZSI6MTY4MDA5MDA2ODU1NywiZXhwaXJlc190aW1lIjoxNjgwNjk0ODY4NTU3LCJhdHRhY2htZW50cyI6W3siZGF0YSI6eyJqc29uIjp7ImZvbyI6ImJhciJ9fSwiaWQiOiIxMjMiLCJkZXNjcmlwdGlvbiI6ImV4YW1wbGUiLCJtZWRpYV90eXBlIjoiYXBwbGljYXRpb24vZGlkY29tbS1lbmNyeXB0ZWQranNvbiJ9XX0"}',
  from_prior: null
}
```
=================== REPUDIABLE AUTHENTICATED ENCRYPTION ===================

Encryption metadata is
```js
 {
  messaging_service: null,
  from_kid: 'did:hid:testnet:zGY2t3arVXjsn7mi24xowiLiwHQCErzKve8YzHXo2ER9f#z6LScqAc6RHPvxN2WabKDGfhy7f4qaNK3unCxZFKdRbzrdpX',
  sign_by_kid: null,
  to_kids: [
    'did:hid:testnet:zB23yfoioAkThgKxumcGSeWazCXvgJWxT6fK3B9nHweg8#z6LSoGabWQXPgu85wr9C5n8PrqAWvUTiYRX9RU28YksHow1U'
  ]
}
```
Sending message
```js
 {"protected":"eyJ0eXAiOiJhcHBsaWNhdGlvbi9kaWRjb21tLWVuY3J5cHRlZCtqc29uIiwiYWxnIjoiRUNESC1FUytBMjU2S1ciLCJlbmMiOiJYQzIwUCIsImFwdiI6IjJSbmx4SlhUX3g1REtFSk1QQnU2akE5clNSUVJ2SmhsWVF1N2d1aDd0UVEiLCJlcGsiOnsiY3J2IjoiWDI1NTE5Iiwia3R5IjoiT0tQIiwieCI6InFvWEpCOUpkZGRaWlhnazg2cTVaRDdIRWRaNllTbnhtaGhFbzlMOHlpUzgifX0","recipients":[{"header":{"kid":"did:hid:testnet:zB23yfoioAkThgKxumcGSeWazCXvgJWxT6fK3B9nHweg8#z6LSoGabWQXPgu85wr9C5n8PrqAWvUTiYRX9RU28YksHow1U"},"encrypted_key":"tzsB4bfSV8g26_Ze4zDJ9y4CTiX2yriOQGjAf7NZ65gpnA0asIHcQQ"}],"iv":"Q2s821RxxgEu248Hqo_5lu9r9so6wg6s","ciphertext":"dRoCcbOdL_uqghhSQI27S-iAFAAXg9NhzmDuKwJ4pN7PvOuVigu3d8PYJCEa3yh2BA0Rmyhl7ZJTyUPU74pTQb6ws91x50fXteYTC1T_FHiqjjjRDXtIg2H8t1t_z-VkQ8cYa3iz-U4OAoJAOa-T9ue-XbZOIQCBRu-0ehBxFmKFcI8-XZXLNC3qQflWuUJy-hmu8L2k96C9kYm-H4rPC8TtpkuzE4bpRAhLJGfux9s9ZUu67GuyOP9uNVzTS6S8l9DtSeBB5ywYoB6ofn_ge40IOdcJ_VjEkHTsd8BsufUXN8NySsK5kUUVQjH2PIYu0grM5Khf4oYbMQfESZM_DTpjuusWCzkPH7rQyZ5Dj4vgBBJTmSrGtV4M0kJZKcaCDBF2V4FzON7sxVc0e-XR578Ems3vgaWBZu2g3pAZVdzX1nA-TJW2NITcvhGgC-w555Xd6NEJmPBjy6TPippWpBrYscTG-gowp-jM97VAgjyVRTWiwrJSTZFHgzeshfyzLTgfxx0Xad5AjlSASwfV883LLqfumQdjAjlqANwddt4kSpVmr04q12EVFT1v3Dutd_Wo7pf30mL6PkF7t-3xROZq6RB1xYfXTWiT4pO5HInbtzBdN2kO3uABNnI7f9lgZvZf3-3iD6RNU-rSCUXBBZ_jbh51JnslUnulAYqQl7U2nY94BX5nfLA58pA6M9Mv9bDSAbz_BcOoMDwuIWWuH-CwJVTpWQBiaBlNv6_mo0S-Cpg8j1zbZUAZMwYxQLnDaa2fH44ItiBf10CgvhHZYVHmgDPRKWMuLwE8RZUSDbnNjrIQ3AH24Y3mUww-URNOFTZhrcUFZuOOHUyR5FU-kVhTaEiqtvBNcuP0feKFjzJURgsj2osldSLmNrM0mOZkqXRmuyDmgDu8xm7woXJkSw3VGcLZOU1Io0qa3hE5OJ3pDxd2skSqRG1SedVF9XZXHGoqA1_9q8Z9Lm--3FPkceNvUFIj26zYnriP6BdV-DtijjBnZdObQ4VQQQi1-ncvQShLb9hbFwnfYT-CaqURF-cv0tAo1bNnCy2V0U0JpmwgIRJP8MbZeidxINg2LYryPPKYb0d8fVPQTy7r5CeQFYeCOmfpniwvVYC8GI3SW3-YBwnGSaozFWeG_8tsgABr6ZjpbMqvL_xADPBO19IrWvMxe0Z4wjwydXsv1r5UUCyY5NKKHWrixXtWLnQ0Is6qDUZRD70IuRPVF4ZZkl1F_LY3G4Pe_ev67HjD48sNPTmZaPs864plePm1I1ZOyg6z03ppflA2YxSmfJnCD5y3oXUgSEwj7I1ISclI716BdBQZZIIWvUrTmLhuQ8fuxFdfZTgGSnSfSCP0otfcAsGvTkaAcUd2hV2IJjjqX8vcfn4qE2tesqkhkpk0ZDZM9wweaa24ndqyf0Y42AaDNFECRU5iahFw3e6jSscTL_49Gk3HgSxRDPCKyWoZaFy5loS0MlJRIF3k_Y8C39-AG2mIXjkbi83DSlixpX5HaoGcb5197Z1kvFtyTcKV-9KDre_9Tl4zl1TwRJc1S48buw6UAJ36Mrdycl5_88GDT5-wEq0jxINN_RUuPRfuco6Qj1rSCGBgmdmAtsj6gsy6I-_jBLSaewKkLMOOluB40pHYjV5GKDXJeyE3k3TGy4KtMZv7oWsQXlpvGc0eU8hvl2UbqJug8_llaDblqg7RJoIrNBr984YiHjvLeroaNHqLOKuJAFUwR3YGZWV-rs5U0yt5mBAzM18Kzs2PXtJteWeDv5S6K3sop-Qx01HBiqvMHnbI6bvzqv5s2endpoA0ElgfchsQUuHRcIpLl7OXKap6wAhYaNxYvOvI1GEjdTFfZk1Be40ZOlYKTwxABqW_BjGNWtqi9o0r-i3-QBcQLaYqfYRy6sKQhbwbq4T6mLO-lPTdthvLNNvgWxe_dHGwTqdMNe_YZOyo3_DgmGobn7OUtCjjGrJzrD2YdVJ5UIRkIXl5jwDskFgeOe_0ll5fnWE1xtO41jwqFZYZ6m6ixsCd1M7Mq0FcZ9RXwBmLA5ihf5mVPzO6MSgkjg5iPR1QBwWthJ3xKS5gneHTyKV-FM3O6Y-IdkREq9b0DprJgMUApp2B2GZeX9_KABu2a1fTWOSyjlAXUKP5vTd4KiD_MCQUuKMulPYY2zp-nwsfTmFrF1oEnn4gbLvHs7eyYHFlE5tY1SXA7RjK4B8J9HDO_mbzSn89-SGp1nqQz8Za5jJ3plQudFLxoAIuww","tag":"G2C5ykDzKUEVGx9aYHiI3w"}
 ```
Reveived message is
```js
 {
  id: '1234567890',
  typ: 'application/didcomm-plain+json',
  type: 'http://example.com/protocols/lets_do_lunch/1.0/proposal',
  body: { data: 'data' },
  from: 'did:hid:testnet:zGY2t3arVXjsn7mi24xowiLiwHQCErzKve8YzHXo2ER9f',
  to: [ 'did:hid:testnet:zB23yfoioAkThgKxumcGSeWazCXvgJWxT6fK3B9nHweg8' ],
  created_time: 1680090068819,
  expires_time: 1680694868819,
  attachments: [
    {
      data: [Object],
      id: '123',
      description: 'example',
      media_type: 'application/didcomm-encrypted+json'
    }
  ]
}
```
Reveived message unpack metadata is
```js
 {
  encrypted: true,
  authenticated: true,
  non_repudiation: false,
  anonymous_sender: true,
  re_wrapped_in_forward: false,
  encrypted_from_kid: 'did:hid:testnet:zGY2t3arVXjsn7mi24xowiLiwHQCErzKve8YzHXo2ER9f#z6LScqAc6RHPvxN2WabKDGfhy7f4qaNK3unCxZFKdRbzrdpX',
  encrypted_to_kids: [
    'did:hid:testnet:zB23yfoioAkThgKxumcGSeWazCXvgJWxT6fK3B9nHweg8#z6LSoGabWQXPgu85wr9C5n8PrqAWvUTiYRX9RU28YksHow1U'
  ],
  sign_from: null,
  from_prior_issuer_kid: null,
  enc_alg_auth: 'A256cbcHs512Ecdh1puA256kw',
  enc_alg_anon: 'Xc20pEcdhEsA256kw',
  sign_alg: null,
  signed_message: null,
  from_prior: null
}
```
=================== REPUDIABLE NON AUTHENTICATED ENCRYPTION ===================

Encryption metadata is
```js
 {
  messaging_service: null,
  from_kid: null,
  sign_by_kid: null,
  to_kids: [
    'did:hid:testnet:zB23yfoioAkThgKxumcGSeWazCXvgJWxT6fK3B9nHweg8#z6LSoGabWQXPgu85wr9C5n8PrqAWvUTiYRX9RU28YksHow1U'
  ]
}
```
Sending message
```js
 {"protected":"eyJ0eXAiOiJhcHBsaWNhdGlvbi9kaWRjb21tLWVuY3J5cHRlZCtqc29uIiwiYWxnIjoiRUNESC1FUytBMjU2S1ciLCJlbmMiOiJYQzIwUCIsImFwdiI6IjJSbmx4SlhUX3g1REtFSk1QQnU2akE5clNSUVJ2SmhsWVF1N2d1aDd0UVEiLCJlcGsiOnsiY3J2IjoiWDI1NTE5Iiwia3R5IjoiT0tQIiwieCI6InRhUm43WkVFUkJaRTc3dWV0amVmSHJRaFFPMTI5dDhqcndhVXhSb3RWMHcifX0","recipients":[{"header":{"kid":"did:hid:testnet:zB23yfoioAkThgKxumcGSeWazCXvgJWxT6fK3B9nHweg8#z6LSoGabWQXPgu85wr9C5n8PrqAWvUTiYRX9RU28YksHow1U"},"encrypted_key":"VWBayxtxXH2bVk7bLA9J46ORM3WHtVaA8ZUoaaeFC2MNt4nsEtN8pQ"}],"iv":"EPr0SGjWOiRpqNooudK97hD22CmEUC65","ciphertext":"2QHxTdBNT6Nr2kDgx2VCXoYQ_khz-qC_FTDvjWGYvd8KPOIxvG7o3loLXm6nnjTQTkS5HUAo-5Q_AenCmMc8jsNv4V6YIPuUX1RHn2H_IW9JPoWX5HOAGxVwiU86ZyBeQyET3GA8QUEav62WM6oWYw5t5H8GfgnU_BZXXB6KXZ5HHCt6iMUQux3jTqp-kvHqhJn9w3MopFzBd6GxHb3I_I_ibZTJVhZyPSM2mTLHridOE3KxtmWOkRrnVWNapnKvycE5VEDUxVsDqYCUil20NJMxgjOJuYbUCvjPeIX-jpEsPVQjHQUrR6qCvXjXSLbyenPaFHjMNBbFiD9Pihc9K5StNkR7XEGXaw-wfmVm10VKcjVUV6R86KaguVLvZml--99MaeT8ALJfxHE2pcXPOv1V7mnexg_YKczlVuqmMbwoV4UIZDbqMkWT289wP7gakpVhNU5bcxji4aGCUl-IN_0Vd2lTwszSmU0gxE6Q5ePVuQUvbXcKaXKJesLy9mE0V11GLTR42XaJAYK4UH1pG94gUv-tzGdjK4WVo5vjL8wFDgZvLCYxeS6g1MX94v958f0aqisvRWYmDBzFFF_Y5mbJAGPNiDgsP67dEnp-pOSPyJEu6PPzP6IcvaoXSq4","tag":"TgWQUTEujkufqPk8UmTz6g"}
 ```
Reveived message is
```js
 {
  id: '1234567890',
  typ: 'application/didcomm-plain+json',
  type: 'http://example.com/protocols/lets_do_lunch/1.0/proposal',
  body: { data: 'data' },
  from: 'did:hid:testnet:zGY2t3arVXjsn7mi24xowiLiwHQCErzKve8YzHXo2ER9f',
  to: [ 'did:hid:testnet:zB23yfoioAkThgKxumcGSeWazCXvgJWxT6fK3B9nHweg8' ],
  created_time: 1680090069194,
  expires_time: 1680694869194,
  attachments: [
    {
      data: [Object],
      id: '123',
      description: 'example',
      media_type: 'application/didcomm-encrypted+json'
    }
  ]

}
```
Reveived message unpack metadata is
```js
 {
  encrypted: true,
  authenticated: false,
  non_repudiation: false,
  anonymous_sender: true,
  re_wrapped_in_forward: false,
  encrypted_from_kid: null,
  encrypted_to_kids: [
    'did:hid:testnet:zB23yfoioAkThgKxumcGSeWazCXvgJWxT6fK3B9nHweg8#z6LSoGabWQXPgu85wr9C5n8PrqAWvUTiYRX9RU28YksHow1U'
  ],
  sign_from: null,
  from_prior_issuer_kid: null,
  enc_alg_auth: null,
  enc_alg_anon: 'Xc20pEcdhEsA256kw',
  sign_alg: null,
  signed_message: null,
  from_prior: null
}
```
=================== SIGNED UNENCRYPTED ===================

Encryption metadata is
```js
 {
  sign_by_kid: 'did:hid:testnet:zGY2t3arVXjsn7mi24xowiLiwHQCErzKve8YzHXo2ER9f#key-1'
}
```
Sending message
```js
 {"signatures":[{"header":{"kid":"did:hid:testnet:zGY2t3arVXjsn7mi24xowiLiwHQCErzKve8YzHXo2ER9f#key-1"},"protected":"eyJ0eXAiOiJhcHBsaWNhdGlvbi9kaWRjb21tLXNpZ25lZCtqc29uIiwiYWxnIjoiRWREU0EifQ","signature":"dbB3ZQWWon-yw73S8EBfXxB1UPsIn0t4wKq12SDvq-7ojiUTGxHmG_k6tP51tbRX1mU3moiprTxDPQujlkRODA"}],"payload":"eyJpZCI6IjEyMzQ1Njc4OTAiLCJ0eXAiOiJhcHBsaWNhdGlvbi9kaWRjb21tLXBsYWluK2pzb24iLCJ0eXBlIjoiaHR0cDovL2V4YW1wbGUuY29tL3Byb3RvY29scy9sZXRzX2RvX2x1bmNoLzEuMC9wcm9wb3NhbCIsImJvZHkiOnsiZGF0YSI6ImRhdGEifSwiZnJvbSI6ImRpZDpoaWQ6dGVzdG5ldDp6R1kydDNhclZYanNuN21pMjR4b3dpTGl3SFFDRXJ6S3ZlOFl6SFhvMkVSOWYiLCJ0byI6WyJkaWQ6aGlkOnRlc3RuZXQ6ekIyM3lmb2lvQWtUaGdLeHVtY0dTZVdhekNYdmdKV3hUNmZLM0I5bkh3ZWc4Il0sImNyZWF0ZWRfdGltZSI6MTY4MDA5MDA2OTUwNiwiZXhwaXJlc190aW1lIjoxNjgwNjk0ODY5NTA2LCJhdHRhY2htZW50cyI6W3siZGF0YSI6eyJqc29uIjp7ImZvbyI6ImJhciJ9fSwiaWQiOiIxMjMiLCJkZXNjcmlwdGlvbiI6ImV4YW1wbGUiLCJtZWRpYV90eXBlIjoiYXBwbGljYXRpb24vZGlkY29tbS1lbmNyeXB0ZWQranNvbiJ9XX0"}
 ```
Reveived message is
```js
 {
  id: '1234567890',
  typ: 'application/didcomm-plain+json',
  type: 'http://example.com/protocols/lets_do_lunch/1.0/proposal',
  body: { data: 'data' },
  from: 'did:hid:testnet:zGY2t3arVXjsn7mi24xowiLiwHQCErzKve8YzHXo2ER9f',
  to: [ 'did:hid:testnet:zB23yfoioAkThgKxumcGSeWazCXvgJWxT6fK3B9nHweg8' ],
  created_time: 1680090069506,
  expires_time: 1680694869506,
  attachments: [
    {
      data: [Object],
      id: '123',
      description: 'example',
      media_type: 'application/didcomm-encrypted+json'
    }
  ]
}
```
Reveived message unpack metadata is
 ```js
 {
  encrypted: false,
  authenticated: true,
  non_repudiation: true,
  anonymous_sender: false,
  re_wrapped_in_forward: false,
  encrypted_from_kid: null,
  encrypted_to_kids: null,
  sign_from: 'did:hid:testnet:zGY2t3arVXjsn7mi24xowiLiwHQCErzKve8YzHXo2ER9f#key-1',
  from_prior_issuer_kid: null,
  enc_alg_auth: null,
  enc_alg_anon: null,
  sign_alg: 'EdDSA',
  signed_message: '{"signatures":[{"header":{"kid":"did:hid:testnet:zGY2t3arVXjsn7mi24xowiLiwHQCErzKve8YzHXo2ER9f#key-1"},"protected":"eyJ0eXAiOiJhcHBsaWNhdGlvbi9kaWRjb21tLXNpZ25lZCtqc29uIiwiYWxnIjoiRWREU0EifQ","signature":"dbB3ZQWWon-yw73S8EBfXxB1UPsIn0t4wKq12SDvq-7ojiUTGxHmG_k6tP51tbRX1mU3moiprTxDPQujlkRODA"}],"payload":"eyJpZCI6IjEyMzQ1Njc4OTAiLCJ0eXAiOiJhcHBsaWNhdGlvbi9kaWRjb21tLXBsYWluK2pzb24iLCJ0eXBlIjoiaHR0cDovL2V4YW1wbGUuY29tL3Byb3RvY29scy9sZXRzX2RvX2x1bmNoLzEuMC9wcm9wb3NhbCIsImJvZHkiOnsiZGF0YSI6ImRhdGEifSwiZnJvbSI6ImRpZDpoaWQ6dGVzdG5ldDp6R1kydDNhclZYanNuN21pMjR4b3dpTGl3SFFDRXJ6S3ZlOFl6SFhvMkVSOWYiLCJ0byI6WyJkaWQ6aGlkOnRlc3RuZXQ6ekIyM3lmb2lvQWtUaGdLeHVtY0dTZVdhekNYdmdKV3hUNmZLM0I5bkh3ZWc4Il0sImNyZWF0ZWRfdGltZSI6MTY4MDA5MDA2OTUwNiwiZXhwaXJlc190aW1lIjoxNjgwNjk0ODY5NTA2LCJhdHRhY2htZW50cyI6W3siZGF0YSI6eyJqc29uIjp7ImZvbyI6ImJhciJ9fSwiaWQiOiIxMjMiLCJkZXNjcmlwdGlvbiI6ImV4YW1wbGUiLCJtZWRpYV90eXBlIjoiYXBwbGljYXRpb24vZGlkY29tbS1lbmNyeXB0ZWQranNvbiJ9XX0"}',
  from_prior: null
}
```
=================== PLAINTEXT ===================

Sending message
```js
 {"id":"1234567890","typ":"application/didcomm-plain+json","type":"http://example.com/protocols/lets_do_lunch/1.0/proposal","body":{"data":"data"},"from":"did:hid:testnet:zGY2t3arVXjsn7mi24xowiLiwHQCErzKve8YzHXo2ER9f","to":["did:hid:testnet:zB23yfoioAkThgKxumcGSeWazCXvgJWxT6fK3B9nHweg8"],"created_time":1680090069862,"expires_time":1680694869862,"attachments":[{"data":{"json":{"foo":"bar"}},"id":"123","description":"example","media_type":"application/didcomm-encrypted+json"}]}
 ```
Reveived message is
```js
 {
  id: '1234567890',
  typ: 'application/didcomm-plain+json',
  type: 'http://example.com/protocols/lets_do_lunch/1.0/proposal',
  body: { data: 'data' },
  from: 'did:hid:testnet:zGY2t3arVXjsn7mi24xowiLiwHQCErzKve8YzHXo2ER9f',
  to: [ 'did:hid:testnet:zB23yfoioAkThgKxumcGSeWazCXvgJWxT6fK3B9nHweg8' ],
  created_time: 1680090069862,
  expires_time: 1680694869862,
  attachments: [
    {
      data: [Object],
      id: '123',
      description: 'example',
      media_type: 'application/didcomm-encrypted+json'
    }
  ]
}
```
Reveived message unpack metadata is
```js
 {
  encrypted: false,
  authenticated: false,
  non_repudiation: false,
  anonymous_sender: false,
  re_wrapped_in_forward: false,
  encrypted_from_kid: null,
  encrypted_to_kids: null,
  sign_from: null,
  from_prior_issuer_kid: null,
  enc_alg_auth: null,
  enc_alg_anon: null,
  sign_alg: null,
  signed_message: null,
  from_prior: null
}

```

## Reference Implementation
- [Did Comm Lattest Draft](https://identity.foundation/didcomm-messaging/spec/)
- [Did Comm v1](https://github.com/hyperledger/aries-rfcs/tree/main/concepts/0005-didcomm)
- [Did Comm v2](https://didcomm.org/book/v2/)
- [Aries RFC 005](https://github.com/hyperledger/aries-rfcs/tree/main/concepts/0005-didcomm)
- [Did Comm 2.0 implementation](https://github.com/decentralized-identity/didcomm-messaging)
- [IDF DID Comm](https://identity.foundation/working-groups/did-comm.html)


## POC

- DIDComm LIB: https://github.com/sicpa-dlab/didcomm-rust/tree/main/wasm
- Run POC : git clone this above library and build using wasm and use as a dependency in the poc 
- POC Link : https://github.com/hypersign-protocol/hypersign-knowledge-hub/tree/didComm/didComm-demo

## Backlog
Figure out a transport protocol  to build end to end didcomm flow 
transport protocol (udp/tcp)
May be some higher level protocols like (webrtc)

Actually webrtc could be more secure as it doesnot require server.
Webrtc supports p2p realtime audio video text communication over udp if didcomm enlist audio and video in the spec later webrtc could be a potential Higher level transport protocol for didcomm.


