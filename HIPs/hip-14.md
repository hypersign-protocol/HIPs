---
hip: 14
title: Unique wallet addresses across DID Documents
author: Arnab Ghose <arnab@hypermine.in>
status: Implemented
type: Standards Track
category: Core
created: 2023-06-23
---

## Problem Statement

One of our customers is using the Hypersign Identity Network to leverage Decentralized Identifiers by providing a reputation score to each one of them. The way a Decentralized Identifier is scored is by considering every verification method that has a `blockchainAccountId`. Each verification method is individually scored based on parameters such as total assets, transaction counts, etc. Once every verification method receives a score, a total summation is assigned to that particular DID Document.

However, an issue was highlighted by the customer. By default, any verification method of any DID can be added to some other DID, which poses a problem. If a score is given to a DID that has two verification methods, the user of that DID can essentially add any one of these verification methods to a new DID. This would result in duplicate scoring, which is not expected.

To solve this problem, we propose restricting the addition of verification methods with a populated `blockchainAccountId` field to any other DIDs if they are already present in an active DID. This way, we provide an alternative solution to users who want to add verification methods to their DIDs by choosing methods where the `blockchainAccountId` is absent. This proposal summarizes the approaches we explored and the decision we made.

## Approaches

### Key Value Store

In this approach, we plan to store the wallet addresses as keys and their corresponding DID IDs as values in the Key-Value (KV) Store. During a DID Creation operation, if a `blockchainAccountId` is present in the KV Store, the operation is discarded; otherwise, it proceeds as intended.

Insert, Search, and Delete operations can be achieved in `O(1)` complexity, making wallet inclusion verification in a DID efficient and fast. However, the space complexity of this approach is `O(N)`, meaning that as the number of wallets increases, the space required will grow linearly over time.

### Merkle Tree and Sparse Merkle Tree

A Merkle tree is essentially a binary tree formed by recursively hashing its leaf nodes, combining them in pairs, and continuing this process until a single root node is obtained. A Sparse Merkle tree is a variant of the Merkle tree that optimizes space efficiency by selectively including only non-empty leaf nodes and intermediate nodes in the tree structure.

A similar property shared by these two data structures is that we can prove whether a leaf node is part of a particular tree or not, which conveniently fits with the problem statement we are trying to solve. In this case, `blockchainAccountId` could act as a leaf of the tree. During a DID Create Operation, if a `blockchainAccountId` is already part of the tree, we will stop the operation. If it isn't, it is added to the tree. Following schematic is a rough implementation idea of Merkle Tree for `blockchainAccountId` on Hypersign:

<img width="970" alt="merkletree_hypersign" src="https://github.com/hypersign-protocol/HIPs/assets/32813269/0a0cafee-6f5d-4122-9355-01e30ccece8a">

Unlike the former approach, we won't need to maintain a separate storage specific to `blockchainAccountId` since we can utilize the existing DID Document storage where the `blockchainAccountId` is already present to fetch all the `blockchainAccountId`s.

## Decision

After thorough discussion, we have decided to adopt the **Key-Value Store** approach. The reason for this decision is that while the Merkle Tree approach could have avoided creating an additional storage for `blockchainAccountId`, it would have required a complete traversal of the DID Document store to gather information about all existing verification methods in order to form the tree after adding a leaf. This would result in a time complexity of `O(N^2)`. Considering the importance of performance, we concluded that the **Key-Value Store** approach is more suitable for our needs at the moment.
