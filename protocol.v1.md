# JUPP Share Protocol - version 1

## JUPP-SP: Introduction

The jupp share protocol describes how a JUPP identity **must** be generated, how it **may** be transmitted, and how it **should** be stored.

### Introduction: Identity Generation

The sharable jupp identity is a concatenation of a plain text service domain and a base58 string within which is unique cryptographic digest:

i.e;

`<domain:text>:<address:base58>`

e.g;

`dcdc:7xZjQ8N9yQYUhB2u8T9bn4BYTqh`

Though the protocol does not dictate it, the service domain is assumed to be constant throughout the life of any one instance of a JUPP device. The address *must* be newly generated every time the device is activated.

End to end, jupp identity generation is a two stage process. The first stage occurs only once in order to initialise the identity generator. The second stage occurs as many times as a new identity is required.

#### Identity Generation: Stage 1

A jupp identity generating device must be initialised in the following way:

1. A user specifies a domain (D)
2. A user selects a public key system (S) advertised as compatible with (D)
3. A user produces private key material (p0) for (S)
4. A user produces optional salting material (us0)
5. A user executes HMAC_SHA256 with p0 as key and p0 + us0 as message (h0)

_h0_ must be securely stored within the device.

#### Identity Generation Stage 2

After initialisation, a jupp device must perform the following to produce a new identity:

1. If counter (c) is not initialised, initialised counter (c) to 0 as unsigned int32 
2. Increment counter c by 1
3. Perform HMAC_SHA256 with h0 as key and c as message (ip)
4. Calculate public key for (ip) given (S) as (P)
5. Perform address extraction for P

