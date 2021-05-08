---
hip: 2
title: Self Signed Credential
author: Vishwas Anand (@Vishwas1)
status: Draft
type: Standards Track
category: Identity-Wallet
created: 2021-05-08
---

## Simple Summary
A user should be able to issue self signed credential to himself/herself.

## Abstract

Many websites needs similar kind of data from a user. For example, two ecommerce sites can ask name, address, phonenumber and email from a user. Not all credentials can be issued by an issuer. For example, how can a user get a credential for his physical address? In order to do that, govt. issuers (who issues address proofs like voter id card, addhar card etc.) need to be onboarded as issuer on Hypersign ecosystem which is not happening atleast in near future. In this case, a user should be able to create and issue credential to himself and then can share this *self signed credential* to the vendor. 


## Rationale

The rational behind *self signed credential* has two folds:  

- Say a user wants to buy a product from ecommerce site, he would not give someone elses address. So in this case *self signed credential* becomes a easy way to hold user data (by himself) and share it with service provider via QR code, whenever he wants without having to type it everytime. 
- In real world also we do have concept of *self signed ceredential*. Remember, sometime we are asked to share photocopy of some document but the vendor does not accept it unless we sign and give - thats where we self sign and give it to the vendor.

In short, *self signed credential* is digital representation of *signed photocopy of a physical document*.

## Privacy concerns

In the current legacy system, a user either has to go through the pain of typing his data every time he wants to share his data with the service provider or has to trust on service provider to store his data securly. We have many cases in the past where user data have been stolen from service providers end as single place with so much user data becomes honey pot to hackers. 

The concept of self signed credential with not only solve the data crentralization problem but also give user opportuniy to manage their data the way they wanted wihtout having to trust on anyone.

## Implementation idea
- From the credentials list page, user should get a *plus* icon to create credential. Upon clicking on *plus* icon, he should be asked to choose between set of predefine *schamas*. Say for example, a schema can have `name` and `address` attributes/fields. Another schama can have `credit` card information.
- Upon choosing *schema*, he should be asked to fill detials of attritbutes. 
- Finally, he clicks on "OK" button to generate self signed credential which then appears in the credential list page.
- Next, a service provider can request for credential (he already knows which schama to ask). For example, a ecommerce site can ask address and name schama at the checkout page, then he can ask credit card schema but showing a QR code. A user can select the desired schema and can share his credential to the service provider / website in P2P fashion. 
