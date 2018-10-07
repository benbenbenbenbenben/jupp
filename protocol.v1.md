<style>
ol > li {
    counter-increment: root;
}

ol > li > ol {
    counter-reset: subsection;
    list-style-type: none;
}

ol > li > ol > li {
    counter-increment: subsection;
}

ol > li > ol > li:before {
    content: counter(root, lower-roman) "." counter(subsection, lower-alpha) ". ";
}
</style>

# JUPP Share Protocol - version 1

## JUPP-SP: Introduction

The JUPP Share Protocol describes how a JUPP identity **must** be generated, how it **may** be transmitted, and how it **should** be stored.

This document covers:

1. Identity Generation by HKDF
    1. Identity Generation: Stage 1 of 2 - Extract
    2. Identity Generation Stage 2 of 2 - Expand
    3. DCDC Standard Address Generation Example
2. Sharing Identities as URLs
    1. HTTP Handling for JUPP Identity URLs
        1. Hand to Hand Identity Sharing
3. Direct Communication to JUPP Identity Owners




### i. Identity Generation by HKDF

The sharable JUPP identity is a concatenation of a plain text service domain and a base58 string within which is unique cryptographic digest:

_JUPP identity format:_

`<domain:string>:<address:base58 string>`

_An example identity:_

`dcdc:7xZjQ8N9yQYUhB2u8T9bn4BYTqh`

Though the protocol does not dictate it, the service domain is assumed to be constant throughout the life of any one instance of a JUPP device. The address **must** be newly generated every time the device is activated.

End to end, JUPP identity generation is a two stage process. The first stage occurs only once in order to initialise the identity generator. The second stage occurs as many times as a new identity is required.



#### i.a. Identity Generation: Stage 1 of 2 - Extract

> In Brief: a compliant device **must** securely store a 256bit value _Hash0_ where: `Hash0 = HMAC_SHA256(key = RootPrivateKey, message = RootPrivateKey + UserSalt)`

A JUPP identity generating device must be initialised in the following way:

1. A user specifies a domain *(Domain)*
2. A user selects a public key system *(PKSystem)* advertised as compatible with *(Domain)*
3. A user produces private key material *(RootPrivateKey)* for *(PKSystem)*
4. A user produces optional salting material *(UserSalt)*
5. A user executes HMAC_SHA256 with *(RootPrivateKey)* as `key` and *(RootPrivateKey)* + *(UserSalt)* as `message` yielding *(Hash0)*

_Hash0_ **must** be securely stored within the device.

#### i.b. Identity Generation Stage 2 of 2 - Expand

> In Brief: a compliant device **must** create an address from the public key of a generated private key where: `GeneratedPrivateKey = HMAC_SHA256(key = Hash0, message = Counter)`

> Note: _(Counter)_ must increment by _at least_ 1 for each published address.

After initialisation, a JUPP device must perform the following to produce a new identity:

1. If counter _(Counter)_ is not initialised, initialised counter _(Counter)_ to 0 as `unsigned int32` 
2. Increment counter _(Counter)_ by 1
3. Perform HMAC_SHA256 with _(Hash0)_ as `key` and _(Counter)_ as `message` yielding _(GeneratedPrivateKey)_
4. Calculate public key for _(GeneratedPrivateKey)_ given _(PKSystem)_ yielding _(GeneratedPublicKey)_
5. Convert _(GeneratedPublicKey)_ to an address format advertised as compatible with *(Domain)*

#### i.c. DCDC Standard Address Generation Example

> In Brief: a standard DCDC address is computed as: `address = base58(0x23, ...RIPEMD160(SHA256(GeneratedPublicKey)))`

The DCDC smart contract platform's standard address algorithm is derived from the address algorithm used by the Neo blockchain, which was earlier derived from the algorithm used by the original Bitcoin blockchain. Because of this a JUPP device can be easily adapted to support one-time address generation for those and derived distributed ledgers that depend on Eliptic Curve Cryptography, SHA256 and RIPEMD160.

To convert a public key to a standard address for DCDC, the following must be true:

1. *(Domain)* = dcdc
2. *(PKSystem)* = EC with secp256r1 aka NIST P-256 aka prime256v1
3. *(RootPrivateKey).length* = 32 bytes or 256 bit

Once stages 1 and 2 of the identity generation are complete, the newly created *(GeneratedPublicKey)* should be hashed with `SHA256` and the output hash then hashed with `RIPEMD160`. This new value `i.e. RIPEMD160(SHA256(GeneratedPublicKey))` is prepended with byte `0x23`, yielding a byte array of length 21. This array is encoded as base58 and the output is a shareable address.

### ii. Sharing Identities as URLs

To be compliant, a JUPP device must output a new address as parameter `suid` in a parameterised URL. To be compliant the output URL must employ HTTPS.

_Example compliant JUPP-SP URLs:_

`https://myjupp.io/?suid=dcdc:7xZjQ8N9yQYUhB2u8T9bn4BYTqh`

`https://acmejuppservice.com/?param1=1&param2=2&suid=dcdc:7xZjQ8N9yQYUhB2u8T9bn4BYTqh`

#### ii.a. HTTP Handling for JUPP Identity URLs

An HTTP server that receives a request for a JUPP-SP URL is expected to act as a broker and enable the creation of a communication channel between the owner of the JUPP identity and the third party. It is helpful to understand the basic scenarios where JUPP-SP URLs might be generated to discuss how brokers should react.

_Scenario 1. Hand to Hand; sharing contact with another person via a GET request_

Alice: has a smartcard with the JUPP application.

Bob: has an Android device with NFC enabled.

Alice: taps the smartcard against the Android device.

Bob: the Android device opens the JUPP-SP URL as a GET request in the web browser.

At this stage the sharing is completed. In order for Bob to directly contact Alice, they must request to open a channel. The web page displayed by the HTTP broker server is expected to include all the instructions necessary to perform this action.

! A GET request must not be used to create a direct communication channel between the holder of an identity and any other party. Creating a direct communication channel must be performed by either a POST request or by directly opening the corresponding channel in the Domain specified in the URL

_Scenario 2. Hand to Device, sharing contact with a retailer via a POST request_

Alice: has a smartcard with the JUPP application.

Retailer: has a contactless card terminal.

Alice: taps to against the card terminal.

Retailer: the card terminal reads the JUPP-SP URL and instructs the POS system to deliver an e-receipt.

At this stage the sharing is completed and the POS system **must either** make a POST request to the JUPP-SP URL **or** directly open the corresponding channel in the Domain specified in the URL.

_Scenario 3. Destroying a channel via a DELETE request_

Alice: has a smartcard with the JUPP application.

Alice: has an Android device paired with the smartcard.

Alice: has an open channel with a retailer.

Alice: signs a DELETE request using the regenerated private key corresponsing to the address used to open the channel.

At this stage the channel is closed and the retailer will no longer be able to communicate through this direct channel.