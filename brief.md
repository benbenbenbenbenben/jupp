# JUPP - Just Useful Personal Privacy

A Protocol For Digital Native Consent

The Digital Cooperative Development Consortium

---

### What is JUPP?

JUPP is a initiative to promote consent in digital relationships. The initiative includes the promotion of JUPP-SP; the JUPP Sharing Protocol, and JUPP-ID; devices that make JUPP-SP easy to use for end users.

### JUPP-SP - The JUPP Sharing Protocol

JUPP-SP facilitates the binding of JUPP addresses into consenting data relationships. When a user shares an address using JUPP-SP, consent to use the address is automatically granted to the receiver.

To understand the flow of data between addresses let's look at some example interactions.

**#1 Alice meets Bob, Bob requests data from Alice:**

In this scenario, Alice shares a cryptorandom address with Bob, and Bob requests data using the address to communicate with Alice. A distributed ledger records the proofs of interaction and consent, but does not record any personally identifying information.

![High Level](JUPP%20Sharing%20Protocol.png "High Level")


**#2 Alice collects an e-receipt:**

In this scenario, Alice shares a cryptorandom address with a retailer, and the retailer records the receipt where Alice can use her private key to retrieve it.


---

Diagram Source:
```
title Alice meets Bob #1

database Distributed Ledger
actor Alice
actor Bob

Alice->Bob: Shares Cryptorandom Address
Bob->Distributed Ledger: Records Signed Request for Data
Distributed Ledger->Alice: Notifies of Request for Data
Alice->Bob: Sends Signed Request for Data Response
Bob->Distributed Ledger: Records Signed Request for Data Response

title Alice collect e-Receipt

database Distributed Ledger
actor Alice
participant Retailer
database Retailer Backend

Alice->Retailer: Shares Cryptorandom Address
Retailer->Distributed Ledger: Records Receipt Reference
Distributed Ledger->Alice: Notifies of Receipt Reference
Alice->Retailer Backend: Sends Signed Receipt Reference
Retailer Backend->Alice: Delivers Receipt
```