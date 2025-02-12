## SoftHSM Documentation v1

[SoftHSM](https://www.softhsm.org) is an implementation of a cryptographic store accessible through a PKCS#11 interface.  The first version of SoftHSM was developed for OpenDNSSEC using the general requirements for DNSSEC. It uses the library Botan for the crypto operations and the keys are stored in a database backend using SQLite.  You can use SoftHSM as an HSM for OpenDNSSEC.

SoftHSM version 1 has been superseded by version 2 and is no longer supported.  It is still available for reference.  The code for SoftHSMv1 can be downloaded from the OpenDNSSEC website and is hosted on GitHub.

- Download location: [https://dist.opendnssec.org/](https://dist.opendnssec.org/source/).
- GitHub location: [https://github.com/opendnssec/SoftHSMv1](https://github.com/opendnssec/SoftHSMv1)

### Dependencies, building and installing

SoftHSM depends on the Botan 1.8.0 or greater (a cryptographic library) and SQLite 3.3.9 or greater (a database library). But it is recommended to use Botan 1.8.5 or greater since there is a known issues on some OS which freezes the application when it tries to pull entropy.

If the packaged version for your distribution does not work try to compile the latest version from source (this will require a C++ compiler). They can be found at [http://botan.randombit.net](http://botan.randombit.net) and [http://www.sqlite.org](http://www.sqlite.org).

Since this package is outdated we won't provide exact details on compilation and installing.  However normal autoconf/automake scripts are supplied and a normal configure script can be called to prepare compilation with make.  Some notes on configuration after installation:

SoftHSMv1 uses a default location of its run-time configuration file at /etc/softhsm.conf.  This location can be change by setting an environment variable:

    export SOFTHSM_CONF=/home/user/config.file

In this configuration file you can specify what slots will be used (note there should be no whitespace at the beginning of the lines):

    0:/home/user/my.db
    # Comments can be added
    4:/home/user/token.database

The token databases do not exist at this stage. The given paths are just an indication to SoftHSM on where it should store the information for each slot/token.  Each token is now treated as uninitialized.  You can initialize each of your tokens.   This stage creates the databases on disk that will be used to store the keys. Use either the softhsm tool or the PKCS#11 interface.  For each token you will be asked to enter:

- The SO PIN. This is the security officer PIN which can e.g. be used to re-initialize the token.
- The user PIN. This is handed out to applications so the application can interact with the token (for example it is specified in the conf.xml file of OpenDNSSEC or (in v1.4 it can be entered via a command line option 'ods-hsmutil logon').

      softhsm --init-token --slot 0 --label "OpenDNSSEC"

When using SoftHSM with OpenDNSSEC the repository is identified by the following information in the OpenDNSSEC conf.xml file:

    <Repository name="SoftHSM">
     <Module>/usr/local/lib/libsofthsm.so</Module>
     <TokenLabel>OpenDNSSEC</TokenLabel>
     <PIN>1234</PIN>
    </Repository>

### Key management

It is possible to export and import keys to libsofthsm.

1. Importing a key pair. 

   Use the PKCS#11 interface or the softhsm tool where you specify the path to the key file, slot number, label and ID of the new objects, and the user PIN.  
   The file must be in PKCS#8 format.

       softhsm --import key1.pem --slot 1 --label "My key" --id A1B2 --pin 123456

   Add, `--file-pin <PIN>`, if the key file is encrypted.  
   Use, `softhsm --help`, for more info.

2. Exporting a key pair.

   All keys can be exported from the token database by using the softhsm tool. The file will be exported in PKCS#8 format.

       softhsm --export key2.pem --slot 1 --id A1B2 --pin 123456

   Add, `--file-pin <PIN>`, if you want to output an encrypted file.  
   Use, `softhsm --help`, for more info.

### Converting keys to/from BIND

The softhsm-keyconv tool can convert keys between BIND .private-key format and PKCS#8 key file format.

1. Convert from BIND .private to PKCS#8

   Keys used for DNSSEC in BIND can be converted over to PKCS#8. Thus possible to import them into SoftHSM.

       softhsm-keyconv --topkcs8 --in Kexample.com.+007+05474.private --out rsa.pem

   Add, `--pin <PIN>`, if you want an encrypted PKCS#8 file.  
   Use, `softhsm-keyconv --help`, for more info.

2. Convert from PKCS#8 to BIND .private and .key

   PKCS#8 files can be converted to key used for DNSSEC signing in BIND. The public key is also saved to file.

       softhsm-keyconv --tobind --in rsa.pem --name example.com. --ttl 3600 \
       --ksk --algorithm RSASHA1-NSEC3-SHA1

   Add, `--pin <PIN>`, if you the PKCS#8 file is encrypted.  
   Use, `softhsm-keyconv --help`, for more info.

   The following files will be created in this example:

       Kexample.com.+007+05474.private
       Kexample.com.+007+05474.key

### Backup

A token can be backed up by issuing the command:

    sqlite3 <PATH TO YOUR TOKEN> ".backup copy.db"

Copy the "copy.db" to a secure location. To restore the token, just copy the file back to the system and add it to a slot in the file softhsm.conf.

If you are using SQLite3 version < 3.6.11, then you have to use the command below. But it will not copy the "PRAGMA user_version", which is used by SoftHSM for versioning. So you have to do that manually. In this case the version number is 100.

    sqlite3 <PATH TO YOUR TOKEN> .dump | sqlite3 copy.db
    sqlite3 copy.db "PRAGMA user_version = 100;"

Some attributes in the PKSC#11 API are defined as CK_ULONG, unsigned long integer, where the length of the data depends on the architecture (32-bit, 64-bit). The attributes are stored directly in the database. The database can thus not be moved between two systems with different architectures.

### Limitations

SoftHSM v1 has a number of limitations regarding the number of concurrent sessions and the number of stored objects. It also has some limitations on the algorithm support. Only the RSA algorithm can be used for public key operations. Outside the scope of DNSSEC, there is also support for X.509 certificates.

Sessions

- Maximum 256 concurrent sessions with the library

Objects

- The number of objects per token is limited by the integer counter of the database.

Generation

- RSA 512-4096 bit

Sign and verify

- CKM_RSA_PKCS
- CKM_RSA_X_509
- CKM_MD5_RSA_PKCS
- CKM_RIPEMD160_RSA_PKCS
- CKM_SHA1_RSA_PKCS
- CKM_SHA256_RSA_PKCS
- CKM_SHA384_RSA_PKCS
- CKM_SHA512_RSA_PKCS
- CKM_SHA1_RSA_PKCS_PSS
- CKM_SHA256_RSA_PKCS_PSS
- CKM_SHA384_RSA_PKCS_PSS
- CKM_SHA512_RSA_PKCS_PSS

Encrypt and decrypt

- CKM_RSA_PKCS

Digest

- CKM_MD5
- CKM_RIPEMD160
- CKM_SHA_1
- CKM_SHA256
- CKM_SHA384
- CKM_SHA512

Certificate

- X.509


### Performance

The performance tests was done using the OpenDNSSEC speed test. Below is a comparison between the results of the ods-hsmspeed tool with the results of OpenSSL.

Measurement performed on 31 Mar 2011 using:  
**Server:** HP PROLIANT DL380 G6  
**Processors:** Intel(R) Xeon(R) CPU E5520 @ 2.27GHz, 4 cores with HT  
**OS:** Ubuntu 8.04 64-bit  
SoftHSM v1.2.1 with Botan 1.8.8  
OpenSSL 0.9.8g 19 Oct 2007

RSA1024

    ods-hsmspeed -r SoftHSM -i 100000 -s 1024 -t 1ig/s6278.18 sig/s
    6278.18 sig/s

    openssl speed rsa1024 -multi 16
    5994.3 sig/s

RSA2048

    ods-hsmspeed -r SoftHSM -i 10000 -s 2048 -t 16
    1221.10 sig/s

    openssl speed rsa2048 -multi 16
    1106.7 sig/s


### Design of SoftHSM v1

SoftHSM implements functions in accordance with the PKCS#11 v2.20 specified by RSA Security Inc. Read the API for a more detailed view of this interface.

___Tokens / Slots___

The tokens are created by the user with the tool softhsm. Each token is initialized with a security officer PIN and an user PIN. The tokens are then added to the slots by the user in the config file.

___Sessions___

Each advanced interaction with SoftHSM requires a session. The maximum number of concurrent sessions is limited to 2048 by default. This limit can be changed by editing the config file (config.h) in the source code tree before compiling the library.

A session can either be in Read/Write mode or Read-only mode, which is decided by the user upon the creation of the session.

Each session caches a copy of a cryptographic key, whenever it is used. This is to save time by not loading key material from the attributes every single time it is used by the session. To limit the size of the key cache, it is recommended to use a session for a limited time to avoid loading every single key into the memory.

___Objects___

Access to the objects is treated in accordance with the PKCS#11 standard.

SoftHSM currently only supports private keys, public keys, and certificates (CKO_PUBLIC_KEY, CKO_PRIVATE_KEY, and CKO_CERTIFICATE).

___Log in___

Only one login is required for multiple sessions on a single slot.

___Attributes___

SoftHSM is supporting all the attributes connected to general objects, private keys, public keys, certificates, and the RSA algorithm with some minor exceptions. CKA_ALLOWED_MECHANISMS and CKA_WRAP_TEMPLATE is not supported due to their complexity.

___Database___

The database for a token, which contains all the information about the objects, is loaded from the location indicated in the config file. All the information is stored unencrypted.  
Only a hashed version of the SO's and user’s password is stored.

___Thread safety___

The library can be initialized with or without mutex locking. It is safe to use multiple threads with one session per thread. It will never be safe to use multiple threads in one session, because simultaneously use of e.g. the signing functions can give unexpected results.

___Logging___

Events like key generation, deletion of objects, errors, and function calls are logged to the syslog. The level of logging is defined by the user and the configuration script.

___Backup___

A backup of a token is simply done by copying the database file to a secure location.
