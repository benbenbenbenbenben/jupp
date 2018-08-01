# jupp
A low energy NFC device for cryptographic operations

## Brief Description

The jupp device (herein jupp) is an electronic low energy device capable of operation i. with battery support and ii. with energy harvested from an NFC RF field.

The primary purpose of jupp is to a. store an EC private key and b. return a single use derived EC public key hash (f*) on contact with a NFC reader and c. store PIN-protected x509 certificate material for performing on-device cryptographic digests.

On a.; when a user first activates a jupp, it will generate a private key internally, which cannot be extracted. Additionally a user may initialise jupp by transmitting an off-device computed key as an NFC/NDEF write operation.

On b.; the jupp will run HKDF over the private key + salt + a usage counter. The output of this function is a hash of the public key (f*) which will be available for reading one time as an. Once read the jupp will repeat the derivation process incrementing the usage counter. The jupp will be capable of precaulcating derived keys (f*) and storing them in internal memory if the jupp remains powered on and has completed communicating with the reader.

On c.; the jupp has memory slots that can store x509 certificate material that will allow it to load cryptographic identities which the user can later use or retrieve provided they input the requisite PIN via to the reading device.

## Block Diagram

![block diagram][blockdiagram]


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

## Prototype Device BOM

A prototype jupp can be built using the following parts:

* NFC Dynamic Tag: ST25DV-I2C https://www.st.com/en/nfc/st25dv-i2c-series-dynamic-nfc-tags.html?querycriteria=productId=SS1950
* Microcontroller: https://www.silabs.com/products/mcu/8-bit/efm8-sleepy-bee
* Hardware cryptographic accelerator: ATECC508A https://www.microchip.com/wwwproducts/en/ATECC508A
* Optional battery: any 3.0-3.3v button cell configuration, e.g. 2 x LR621 (in series) -or- 1 x CR2025

[blockdiagram]: https://github.com/benbenbenbenbenben/jupp/blob/master/Untitled%20Diagram.png "Block Diagram"

