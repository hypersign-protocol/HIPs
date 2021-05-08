---
hip: 1
title: History of shared credentials
author: Vishwas Anand (@Vishwas1)
status: Draft
type: Standards Track
category: Identity-Wallet
created: 2021-05-08
---

## Simple Summary
A user should be able to see history or places where he has already shared his credentials wintout sacrificing his privacy.


## Abstract
Once user shares his credential (say at the time of login),  he has no way to know what all places did he share this credential. Having this feature will not only help him to know where and all his detials are shared, but also in future, he should be able to **logout** and **request deletion** of his data from service providers.

## Motivation
The motivation behind this feature is not only provide existing notification features which he/she was already getting from legacy thrid party authentcation system but also help him/her to preserve their privacy.

## Rationale
All majors 3rd party authentication systems notifies users where he has logged in or has shared his data. This is good to have feature but the problem with current systems are, these 3rd party authentication providers (such as Facebook or Google) also knows your whereabout. For example, if a user is shopping from Amazon or travelling to Vegas, Facebook/Google would know these information about a user provided Amazon / MakeMyTrip uses Facebook/Google login.  Hypersign can not only provide similar feature but also help user preserve his privacy by not letting thrid party authencation provider (Hypersign itself) know any information on how and where a user is using his credential.

## Implementation idea
- Once user shares his credential from Hypersign Identity Wallet, user gets response saying that his credential has been successfully shared.
- Once this response comes, there shold be way in mobile app to track or store this information and can be used to show as history either on the home page or on the credential details page.

