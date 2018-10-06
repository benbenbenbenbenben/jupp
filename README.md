# jupp - just useful personal privacy
A low energy NFC device for cryptographic operations (and GDPR friendliness!)

## Brief Description

The jupp device (herein jupp) is an electronic low energy device capable of operation i. with battery support and ii. with energy harvested from an NFC RF field.

The primary purpose of jupp is to a. store an EC private key (ipk) and b. return a single use derived EC public key hash (f*) on contact with a NFC reader and c. store PIN-protected x509 certificate material for performing on-device cryptographic digests.

On a.; when a user first activates a jupp, it will generate a private key internally, which cannot be extracted. Additionally a user may initialise jupp by transmitting an off-device computed key as an NFC/NDEF write operation.

On b.; the jupp will run HKDF over the private key + salt + a usage counter. The output of this function is a hash of the public key (f*) which will be available for reading one time as an. Once read the jupp will repeat the derivation process incrementing the usage counter. The jupp will be capable of precaulcating derived keys (f*) and storing them in internal memory if the jupp remains powered on and has completed communicating with the reader.

On c.; the jupp has memory slots that can store x509 certificate material that will allow it to load cryptographic identities which the user can later use or retrieve provided they input the requisite PIN via to the reading device.

## Block Diagram

![block diagram][blockdiagram]

## The (f*) Function

f* performs [HKDF](https://tools.ietf.org/html/rfc5869) over the primary stored EC private key, using a constant value for SALT and a deterministic value for INFO.

SALT = any number of bytes, the value of which is constant throughout the lifetime of the jupp device

INFO = any number of bytes that includes a 32bit reference counter value RC

RC = a 32bit unsigned integer that must increment by 1 each time f* is executed

## Form Factor Suggestions

### Weaable Form Factors
* Finger ring*
* Wrist bracelet
* Necklace pendant

*extant patents may prohibit commericialisation of jupp in this form factor

### Alternative Form Factor Suggestions
* Key fob
* Contactless smartcard
* Dual interface smartcard
* USB dongle

### Additional Contact Interface Considerations
* 3.5mm Audio Connector Interface **
* Contact smartcard

**data communication can be modulated in the audio spectrum in a similar manner the chirp.io project https://github.com/chirp/chirp-arduino

## Prototype Device BOM (version hw1)

A prototype jupp can be built using the following parts:

* NFC Dynamic Tag: [ST25DV-I2C](https://www.st.com/en/nfc/st25dv-i2c-series-dynamic-nfc-tags.html?querycriteria=productId=SS1950)
* Microcontroller: [EFM8 Sleepy Bee](https://www.silabs.com/products/mcu/8-bit/efm8-sleepy-bee)
* Hardware cryptographic accelerator: [ATECC608A](https://www.microchip.com/wwwproducts/en/ATECC608A)
* Optional battery: any 3.0-3.3v button cell configuration, e.g. 2 x LR621 (in series) -or- 1 x CR2025
* Power management circuit: a discrete component subcircuit combining EH and battery supply, the EFM8 should be configured to enable the battery supply and disconnect the battey when both conditions are true: the jupp is not within an NFC RF field and the EFM8 has completed computing (f*)

## Prototype Device BOM (version hw2)

A prototype jupp can be built in software and loaded onto a supporting JavaCard device. There are numerous available devices, and no assurances can be made about what devices work and how well they work. This said, a jupp applet has been successfully deployed on an [Infineon SLE78 based device sourced from Universal Smartcards](https://www.usmartcards.co.uk/universal-jcard-dual-interface-white-gloss-pvc-card.html) and on an [ACS ACOSJ based device sourced from Smartcard Focus](https://www.smartcardfocus.com/shop/ilp/id~790/acosj-dual-interface-java-card/p/index.shtml). In the case of the ACS device, there is known/confirmed issue with extended ADPUs over the contact interface which is discuseed [here](https://stackoverflow.com/questions/51295639/acosj-gives-incorrect-result-from-apdu-getoffsetcdata) and a workaround for the issue described [here](https://gist.github.com/benbenbenbenbenben/cfe31b2f6abb38bdf503b4679d783d9a). 

*nb: In our limited experience, the Infineon based device though more expensive offered superior contactless reliability. We have not explored the possible reasons for this owing that the resources are not available for such effort at this time.*

## Intended Use Cases

The jupp enables the following use cases:

1. e-receipt/paperless receipts:
  A jupp user can share a one time identity with a retailer. The retailer can use this provided value to index customer data in their internal database without the user revealing personally identifying information. The retailer can store this information in a distributed ledger (a blockchain or similar) where only the owner of the initial private key (ipk) is able to retrieve and decrypt the record.
  
2. sharing contact information in a GDPR conscious manner:
  A jupp user can share a one time identiy with a second party using a jupp aware device. Such a device can extract an address from the information jupp provides which can then be used to communicate fromt he second party to the jupp user. Because the identity information the jupp provides can only be shared with a single second party, the jupp user can abandon the identity information and from thereonin no longer receive communications from the second party. In this way jupp enables communication between parties without the exchange of personally identifying information.  

## Related Work

 [DCDC](https://dcdc.io) provided a public/private blockchain platform that enables the infrastructure required to enable the above mentioned use cases.
## Licensing

The jupp device specification is released under [MPL 2.0](https://tldrlegal.com/license/mozilla-public-license-2.0-(mpl-2))

This is a copyleft license that permits commerical use, and requires that those producing derivative work cite this original work and publish changes under the same license terms.

This license does not prohibit anybody from making and selling the above described device.

## The jupp Name

The jupp name is not a trademark. jupp is the Estonian word for "a little thing, a bit of something" and a backronym for Just Useful Personal Privacy.

---

© copyright and copyleft 2018 - all rights reserved - Benjamin Babik

[blockdiagram]: https://github.com/benbenbenbenbenben/jupp/blob/master/Untitled%20Diagram.png "Block Diagram"
