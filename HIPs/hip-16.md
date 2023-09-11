---
hip: 16
title: Support for BabyJubJub curve on Hypersign
author: Arnab Ghose <arnab@hypermine.in>
status: Draft
type: Standards Track
category: Core
created: 2023-09-11
---

## Abstract

The selection of an efficient curve for zk-SNARKs is a critical consideration. Within the spectrum of available curves, the BabyJubJub curve stands out as one of the most efficient options. This HIP proposal outlines our efforts to incorporate support for BabyJubJub KeyPairs and Signature Verification into Hypersign. This development will empower us to explore a wide range of use cases built on zk-SNARKs technology.

## Implementation

In HIP-12, we introduced a method for incorporating ZK-SNARKs into CosmWasm smart contracts by verifying presentations. This approach involves the generation and verification of proofs, which necessitate the storage and retrieval of BabyJubJub Keypairs. These keypairs are retrieved from DID Documents. Therefore, this HIP focuses on the technical challenges associated with implementing the BabyJubJub Curve.

Numerous prominent entities are heavily invested in Zero-Knowledge technology development. One of these entities is Iden3, which, in collaboration with Polygon, is diligently working on ZK solutions tailored for the EVM ecosystem. Given their substantial progress in developing BabyJubJub, we have chosen to utilize their libraries.

There are two pivotal libraries at play:

- [circomlibjs](https://github.com/iden3/circomlibjs): This library provides a Javascript implementation of BabyJubJub.
- [go-iden3-crypto](https://github.com/iden3/go-iden3-crypto): It offers a Golang implementation of BabyJubJub.

The registration of a DID Document (or any other SSI document) entails both the signing of the document from client SDKs, written in JavaScript, and the verification of the signature on the Hypersign Blockchain, which is implemented in Go. Consequently, our initial plan was to employ the `circomlibjs` library in the Client SDK to generate the document's signature and then validate this signature using the `go-iden3-crypto` package on the Hypersign end.

While we achieved success when individually testing the `signPosiedon` and `verifyPosiedon` functions from these two libraries, we encountered an issue when attempting to integrate them. The `signPosiedon` function of `circomlibjs` expects a message in the form of a Uint8Array with an exact size of 32 elements. Conversely, the `verifyPosiedon` function of go-iden3-crypto expects the message in the form of a BigInt array, with the only requirement being that the BigInt representation of the message is large enough to fit within the finite field limit. The value of the finite field limit is `p = 21888242871839275222246405745257275088548364400416034343698204186575808495617`. This condition also applies to the `signPosiedon` function of go-iden3-crypto, where providing an input message with an equivalent BigInt representation exceeding the finite field limit results in a panic.

In our initial approach, we attempted to employ a SHA-256 hash of the message due to its ability to produce a 32-byte array, which would align with the expectations of `circomlibjs` during the signing process. However, while verifying this message on the Golang side, it triggered a panic error because its BigInt representation exceeded the constraints of the finite field limit.

After conducting extensive experimentation within the Golang environment, we discovered that the BigInt representation of a SHA3-224 Hash, measuring 28 bytes in length, exhibited compatibility with both the Sign and Verify functions provided by `go-iden3-crypto`. Nevertheless, as previously highlighted, the use of SHA3-224 in `circomlibjs` was not a viable option since the library specifically mandates a 32-byte array input.

To address this compatibility challenge, we turned to [GopherJS](https://github.com/gopherjs/gopherjs), a Go package designed to facilitate the transpilation of Golang code into JavaScript. This strategic move empowered us to generate the `signPoseidon` function from `go-iden3-crypto` in a JavaScript file, enabling straightforward import and utilization of this function in other JS files which is ideal to used in the Client SDK.

Following is the Go code which was used to generate the JS file:

```go
package main

import (
	"encoding/hex"
	"math/big"

	"github.com/iden3/go-iden3-crypto/babyjub"
	"golang.org/x/crypto/sha3"
)

func signBabyJubJub(msg []byte, privateKey string) string {
	// Decode private key from hex
	privateKeyBytes, err := hex.DecodeString(privateKey)
	if err != nil {
		panic(err)
	}
	var privateKeyBytes32 [32]byte
	copy(privateKeyBytes32[:], privateKeyBytes)

	var privKeyObj babyjub.PrivateKey = privateKeyBytes32

	// SHA-224 the message and convert it to BigInt
	msgHash := sha3.Sum224(msg)
	msgBigInt := new(big.Int).SetBytes(msgHash[:])

	// Get Signature
	signatureObj := privKeyObj.SignPoseidon(msgBigInt)
	signatureHex := signatureObj.Compress().String()

	return signatureHex
}

func main() {
	js.Module.Get("exports").Set("signBabyJubJub", signBabyJubJub)
}
```
