## SoftHSM

[SoftHSM](https://www.softhsm.org) is an implementation of a cryptographic store accessible through a PKCS#11 interface.
It was originally developed as a part of the OpenDNSSEC project.

- <https://www.softhsm.org>

### Background

OpenDNSSEC handles and stores its cryptographic keys via the PKCS#11 interface. This interface specifies how to communicate with cryptographic devices such as HSM:s (Hardware Security Modules) and smart cards. The purpose of these devices is, among others, to generate cryptographic keys and sign information without revealing private-key material to the outside world. They are often designed to perform well on these specific tasks compared to ordinary processes in a normal computer.

A potential problem with the use of the PKCS#11 interface is that it might limit the wide spread use of OpenDNSSEC, since a potential user might not be willing to invest in a new hardware device. To counter this effect, OpenDNSSEC is providing a software implementation of a generic cryptographic device with a PKCS#11 interface, the SoftHSM. SoftHSM is designed to meet the requirements of OpenDNSSEC, but can also work together with other cryptographic products because of the PKCS#11 interface.

### SoftHSM v1

[The first version of SoftHSM](softhsm1.md) was developed for OpenDNSSEC using the general requirements for DNSSEC. It uses the library Botan for the crypto operations and the keys are stored in a database backend using SQLite.

SoftHSM v1 is now no longer supported.

### SoftHSM v2

It focuses on a higher level of security by encrypting sensitive information and using unswappable memory. There is also a more generalized crypto backend, where you can use Botan or OpenSSL. Visit the [SoftHSM v2 page](softhsm2.md) for more information.

### Pros and Cons

This list gives some of the arguments why or why not you should use SoftHSM in your environment compared with regular HSM's.

Pros:

1. No need for extra hardware devices.
2. Much cheaper than proprietary hardware.
3. Can be used in an evaluation setup for OpenDNSSEC before the user might decide to invest in a real HSM.
4. Open source code under BSD-license.

Cons:

1. The database, with information like the private key material, is stored on the hard disk drive. Sensible information could leek to third parties if they get access to this file.  This is especially true for SoftHSMv1.  With SoftHSMv2 the keys are encrypted, however this is still more vunerable then physical locked inside a device.
2. The SoftHSM library reads the private key material into the memory to be able to perform cryptographic operations. The program that links with this library may thus get access to this sensitive information.  Some of this risk is mitigated in SoftHSMv2 as plain text keys are kept as short as possible.  But it is still possible when having access to the memory to try to intercept key material.
3. Resides on the same computer as the DNSSEC Signer, thus sharing the same computing resources.
