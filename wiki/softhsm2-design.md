# SoftHSMv2 design

### Design overview

The diagram shows the design overview of SoftHSM v2 and its constituent components.

![assets/highleveldesign.png]

Below is a description of all the components shown in the diagram.

#### PKCS#11 interface

The (PKCS#11)[pkcs11] interface (dark orange, at the top) is just that: an implementation of the PKCS#11 interface version 2.30. It is the only interface layer that SoftHSM v2 exposes to the outside world. All calls from outside the library enter here and are passed on to the other components that make up SoftHSM v2.

#### Initialisation and configuration

The initialisation and configuration component (shown in blue, at the left hand side of the diagram) is used all through SoftHSM v2 to interact with configuration files and is used during the initialisation of SoftHSM v2 when it is loaded.

#### Auditing

The auditing component (show in blue, at the right hand side of the diagram) is used all through SoftHSM v2 to provide logging services to other components. The logs can serve as an audit trail for the actions that have been performed by SoftHSM v2. Although the actual levels have not been decided upon yet, the auditing component will very likely provide different log-levels to allow enabling and disabling of certain log message types.

#### Session manager

The session manager component tracks all PKCS#11 session and the associated state. It also manages session objects.

#### Slot manager

The slot managers component is responsible for managing all PKCS#11 slots and their associated tokens. All tokens loaded from the secure object store configured in the SoftHSM v2 configuration file are always present in a slot. A design decision was made to always have one extra slot available that contains a blank token. Calling C_InitToken on this slot can be used to create a new token.

#### User manager

The user manager component is tracks the state of the PKCS#11 user credentials. These consist of the user PIN and the security officer (SO) PIN. The user manager knows per token whether or not the token is logged in.

#### Secure object store

The secure object store component forms the backend storage of SoftHSM v2. It stores PKCS#11 objects in a directory structure organised as follows:

- There is a top-level directory for the complete secure object store that is configurable in the SoftHSM v2 configuration file
- For each token there is a separate directory; tokens are uniquely identified using a UUID
- Inside the token directory there are separate files for each token. There is also a special file that stores token specific attributes (such as the label, the PINs, etc.)

The secure object store - as it name implies - is capable of storing sensitive attributes of an object securely using the secure data manager (see below).

#### Secure data manager

The secure data manager derives a key from the user PIN that is used for secure storage of sensitive object attributes. The key derived from the PIN is used to encipher/decipher a token key that is used for the actual encryption of the sensitive data. The benefit of this is that not all objects have to be re-encrypted when the user PIN changes. The token key is stored in memory during the time a token is logged in. To protect it against eavesdropping by snooping the memory of the SoftHSM v2 it is cloaked using a blob of random data that is used to XOR the actual key data. The implementation should be such that it is configurable whether or not a new blob of random data is generated every time the key is used.

Key derivation from the PIN is performed using PKCS#5 methodology for key derivation.

#### Crypto abstraction

The crypto abstraction forms a layer between an actual cryptogaphic library (such as Botan or OpenSSL) and the SoftHSM v2 core. This extra layer makes it possible to use different cryptographic implementations with SoftHSM v2 . For the moment, two implementations are planned, one that uses Botan and one that use OpenSSL underneath.

### Interfaces

There should be clearly defined interfaces between some of the main components in the design specified above. These interfaces make it possible to break down the work on SoftHSM v2 into separate parts and facilitate unit testing. Below is a list of components with an internal API:

- The Secure object store
- The User manager
- The Secure data manager
- The Cryptographic abstraction

### Module testing

SoftHSM v2 will incorporate module tests for each component at the interface level of the interfaces specified above; these tests are implemented using the (CPPunit test framework)[http://cppunit.sourceforge.net/].

### Design verification

To verify the design, we have performed a detailed analysis of two use cases and created the corresponding sequence diagrams:

#### Use case: Initialising a token (Sequence1InitToken)

This use case describes the steps taken internally within SoftHSM v2 to initialise a token when a caller of the library calls C_InitToken.

!(Sequence diagram)[assets/inittoken.png]

Steps in the sequence diagram:

1. An external application calls C_GetSlotList on the PKCS#11 interface
2. This triggers a call to the Slot Manager component to obtain a list of available slots
3. The PKCS#11 interface component translates this result to PKCS#11 primitives and returns from the call to C_GetSlotList
4. The external application calls C_GetTokenInfo on the PKCS#11 interface
5. This triggers a call to the Slot Manager component to obtain information about the requested slot
6. The PKCS#11 interface component translates the result to PKCS#11 primitives and returns from the call to C_GetTokenInfo
7. The external application calls C_InitToken on the PKCS#11 interface
8. This triggers a call to the Slot Manager component
9. The Slot Manager calls the Secure Object Store to request the creation of a new token
10. The Secure Object Store returns a new token object
11. The Slot Manager calls the User Manager to set a new SO PIN on the newly created token
12. The User Manager calls the Crypto Abstraction to hash the SO PIN
13. The User Manager calls the Secure Object Store to store the hashed SO PIN
14. The call to C_InitToken on the PKCS#11 interface returns succesfully
15. The external application calls C_OpenSession to open a session to the new token in the specified slot
16. The PKCS#11 interface calls the Session Manager to open a session
17. The Session Manager calls the Slot Manager to check if a token is present in the specified slot
18. The Slot Manager calls the Secure Object Store to check if a token is present
19. The call to C_OpenSession returns succesfully
20. The external application calls C_Login to login with the SO PIN
21. The PKCS#11 interface calls the Session Manager to log the session in
22. The Session Manager calls the User Manager to verify the PIN
23. The call to C_Login returns successfully
24. The external application calls C_InitPIN to initialise the user PIN
25. The PKCS#11 interface calls the Session Manager to initialise the PIN for the token in that session
26. The Session Manager calls the User Manager to initialise the user PIN
27. The User Manager calls the Crypto Abstraction to hash the user PIN
28. The User Manager calls the Secure Object Store to store the hashed PIN
29. The User Manager calls the Secure Data Manager to create a new secret key derived from the PIN
30. The Secure Data Manager derives a new key from the PIN using the Crypto Abstraction
31. The Secure Data Manager generates a new secret key which it encrypts using the key derived from the PIN
32. The Secure Data Manager returns the encrypted key which is stored by the User Manager in the Secure Object Store
33. The call to C_InitPIN returns successfully

#### Use case: Using a key to sign data

This use case describes the steps taken within SoftHSM v2 to sign some data using a key stored on a SoftHSM v2 token.

!(Sequence diagram)[assets/signdata.png]

