## Requirements for SoftHSM

### The following where the requirements for SoftHSM v1

#### General

- Must be a library that can be linked with other software.
- Must implement the PKCS#11 interface v2.20, specified by RSA Labratories.
- Must be licensed under a BSD license.
- Must use a cryptographic library that is licensed under BSD.
- The user must be able to specify the number of tokens to use and its corresponding slots.
- Should keep a log of important events.

#### Algorithms

- Must handle RSA, SHA1, and SHA256.
- Should handle RIPEMD160, SHA384, and SHA512.

#### Key management

- Must be able to protect the keys by using a PIN via the PKCS#11 interface.
- Must be possible to do backup of the keys.
- Must be able to manage 1000 1024-bit RSA key pairs

#### Key generation

- Must be able to generate RSA keys of length 1024 and 2048 bits.
- Should be able to generate RSA keys of length greater than 512 bits, but limited to maximum 4096.
- The user must be able to specify the exponent when generating the RSA keys.

#### Sessions

- Must handle at least 2048 concurrent sessions.

#### Functions

- Must be able to create a hash with a given algorithm.
- Must be able to sign with a given algorithm.
- Should be able to verify with a given algorithm.

#### Support program: softhsm

- Must be able to initialize a token with SO PIN, user PIN, and label.
- Must be able to show which tokens that are available.
- Must be able to import/export RSA keys in PKCS#8 format.
- Must handle both encrypted and unencrypted PKCS#8 files.

#### Support program: softhsm-keyconv

- Must be able to convert from BIND .private format to PKCS#8.
- Must be able to convert from PKCS#8 format to BIND .private and .key format.
- Must handle both encrypted and unencrypted PKCS#8 files.
- Must support the algorithms: RSAMD5, DSA, RSASHA1, DSA-NSEC3-SHA1, RSASHA1-NSEC3-SHA1, RSASHA256, RSASHA512

### SoftHSM v2 requirements adds to these.

The requirements for the SoftHSM v2 are based on the original requirements for SoftHSM v1.
New or changed requirements are marked with __new__ and __changed__.

#### General

- Must be a library that can be linked with other software.
- Must implement the PKCS #11 interface v2.30, as specified by RSA Laboratories. __changed__
- Must be licensed under a BSD license.
- Must be able to use a cryptographic library that is licensed under a BSD license.
- Must support multiple security realms. __new__
- Must be auditable; events should be traceable to the specific role using the SoftHSM. __new__
- Must be flexible enough to use different underlying cryptographic libraries. __new__
- When designing and implementing SoftHSM v2 secure coding guidelines and design principles shall be applied. __new__

The applicable secure coding standards are:  
The [CERT C Secure Coding Standard](https://www.securecoding.cert.org/confluence/display/seccode/CERT+C+Secure+Coding+Standard) and 
The [CERT C++ Secure Coding Standard](https://www.securecoding.cert.org/confluence/pages/viewpage.action?pageId=637).

#### Algorithms

- Must support the following cryptographic algorithms: RSA, DSA __changed__
- Should support the following cryptographic algorithms: GOST __new__
- Must support the following hash algorithms: SHA-1, SHA-256, SHA-512 __changed__
- Must support the following padding mechanisms: PKCS #1 v1.5
- Should support the following padding mechanisms: PKCS #1 PSS __new__

#### Key management

- Sensitive key material should only be exposed when necessary to perform cryptographic operations. __new__
- It must be possible to create backups of the SoftHSM v2 backend store. __changed__
- The implementation must not limit the number of keys the SoftHSM v2 supports. __changed__

#### Performance

- The performance of SoftHSM v2 in relation to the number of objects stored on a token should in the worst case degrade linearly. __new__

#### Key generation

- Must support RSA key-pairs from 1024-bits to at least 2048-bits. __changed__
- Should support RSA key-pairs up to 4096-bits. __new__
- The user must be able to specify the exponent when generating the RSA keys.

#### Sessions

- The number of concurrent sessions must not be limited by the implementation. __changed__

#### Functions

- Must support stand-alone digesting.
- Must support stand-alone signing.
- Must support combined-mechanism signing (digesting and signing in one operation).
- Must support raw signing (PKCS#11 CKM_RSA_X509) __new__
- Must support stand-alone verification. __changed__
- Must support key wrap and unwrap. __new__
- Should support decryption and encryption. __new__

#### Object types __new__

- Must support public key objects as token objects
- Must support private key objects as token objects
- Must support symmetric key objects as session objects
- Must support data objects as token objects
- Should support certificate objects as token objects

#### Support program: softhsm __changed__

softhsm and softhsm-keyconv have been merged into one tool in the requirements.

- Must be able to initialise a token with SO PIN, user PIN and label using PKCS#11
- Must be able to show which tokens are available using PKCS#11
- Must be able to support importing PKCS#8 formatted keys using PKCS#11
- Must be able to support importing BIND .private formatted keys using PKCS#11
- Must be able to dynamically load any PKCS#11 library
- Must NOT support export in BIND format
