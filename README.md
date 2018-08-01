# jupp
A low energy NFC device for cryptographic operations

## Brief Description

The jupp device (herein jupp) is an electronic low energy device capable of operation i. with battery support and ii. with energy harvested from an NFC RF field.

The primary purpose of jupp is to a. store an EC private key and b. return a single use derived EC public key hash (f*) on contact with a NFC reader and c. store PIN-protected x509 certificate material for performing on-device cryptographic digests.

On a.; when a user first activates a jupp, it will generate a private key internally, which cannot be extracted. Additionally a user may initialise jupp by transmitting an off-device computed key as an NFC/NDEF write operation.

On b.; the jupp will run HKDF over the private key + salt + a usage counter. The output of this function is a hash of the public key (f*) which will be available for reading one time as an. Once read the jupp will repeat the derivation process incrementing the usage counter. The jupp will be capable of precaulcating derived keys (f*) and storing them in internal memory if the jupp remains powered on and has completed communicating with the reader.

On c.; the jupp has memory slots that can store x509 certificate material that will allow it to load cryptographic identities which the user can later use or retrieve provided they input the requisite PIN via to the reading device.

## Block Diagram

## Form Factor Suggestions

##

