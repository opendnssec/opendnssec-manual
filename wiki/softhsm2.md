## SoftHSM Documentation v2

[SoftHSM](https://www.softhsm.org) is an implementation of a cryptographic store accessible through a PKCS#11 interface.
It was originally developed as a part of the OpenDNSSEC project.

The second version of SoftHSM focuses on a higher level of security by encrypting sensitive information and using unswappable memory. There is also a more generalized crypto backend, where you can use Botan or OpenSSL.

### Download

Releases can be downloaded from:

- https://www.softhsm.org

### Dependencies

SoftHSM depends on a cryptographic library, Botan or OpenSSL. Minimum required versions:

- Botan 1.10.0
- OpenSSL 1.0.0

If you are using Botan, make sure that it has support for GNU MP (--with-gnump). This will improve the performance when doing public key operations.

There is a migration tool for converting token databases from SoftHSMv1 into the new type of tokens. If this tool is built, then SQLite3 is required (>= 3.4.2).

### Building from the repository

If the code is downloaded directly from the code repository, you have to prepare the configuration scripts.

1. You need to install automake, autoconf, libtool, etc.
2. Run the command 'sh autogen.sh'
3. Continue with the instructions below.

### Installing

1. Configure the installation/compilation scripts.

         tar -xzf softhsm-<version>.tar.gz  
         cd softhsm-<version>  
         ./configure

         Options:

         --disable-non-paged-memory  
                                 Disable non-paged memory for secure storage  
                                 (default enabled)  
         --disable-ecc           Disable support for ECC (default enabled)  
         --disable-gost          Disable support for GOST (default enabled)  
         --enable-visibility     Enable -fvisibility=hidden GCC flags so  
                                 only the PKCS#11 C_* entry points are kept  
         --with-crypto-backend   Select crypto backend (openssl|botan)  
         --with-openssl=PATH     Specify prefix of path of OpenSSL  
         --with-botan=PATH       Specify prefix of path of Botan  
         --with-loglevel=INT     The log level. 0=No log 1=Error 2=Warning 3=Info  
                                 4=Debug (default INT=3)  
         --with-migrate          Build the migration tool. Used when migrating  
                                 a SoftHSM v1 token database. Requires SQLite3.  
         --with-objectstore-backend-db  
                                 Build with database object store (SQLite3)  
         --with-sqlite3=PATH     Specify prefix of path of SQLite3  

2. Compile the source code using the following command:

         make

3. Install the library using the follow command:

         sudo make install

4. Location of the configuration file.

   The default location of the config file is /etc/softhsm2.conf. This location can be change by setting the environment variable.

        export SOFTHSM2_CONF=/home/user/config.file

   Details on the configuration can be found in `man softhsm2.conf`.

5. Initialize your tokens.

   Use either softhsm-util or the PKCS#11 interface. The SO PIN can e.g. be used to re-initialize the token and the user PIN is handed out to the application so it can interact with the token.

       softhsm2-util --init-token --slot 0 --label "My token 1"

   Type in SO PIN and user PIN.  Once a token has been initialized, more slots will be added automatically with a new uninitialized token.

6. Link to this library and use the PKCS#11 interface

### Backup

All of the tokens and their objects are stored in the location given by softhsm2.conf. Backup can thus be done as a regular file copy.

### Developer information

- [Requirements](softhsm-requirements.md)
- [Design](softhsm2-design.md)

### Supported Mechanisms

C_Encrypt/C_Decrypt

- CKM_DES_ECB
- CKM_DES_CBC
- CKM_DES3_ECB
- CKM_DES3_CBC
- CKM_AES_ECB
- CKM_AES_CBC
- CKM_RSA_PKCS
- CKM_RSA_X_509
- CKM_RSA_PKCS_OAEP

C_Digest

- CKM_MD5
- CKM_SHA_1
- CKM_SHA224
- CKM_SHA256
- CKM_SHA384
- CKM_SHA512
- CKM_GOSTR3411

C_Sign/C_Verify

- CKM_MD5_HMAC
- CKM_SHA_1_HMAC
- CKM_SHA224_HMAC
- CKM_SHA256_HMAC
- CKM_SHA384_HMAC
- CKM_SHA512_HMAC
- CKM_GOSTR3411_HMAC
- CKM_RSA_PKCS
- CKM_RSA_X_509
- CKM_MD5_RSA_PKCS
- CKM_SHA1_RSA_PKCS
- CKM_SHA224_RSA_PKCS
- CKM_SHA256_RSA_PKCS
- CKM_SHA384_RSA_PKCS
- CKM_SHA512_RSA_PKCS
- CKM_DSA
- CKM_DSA_SHA1
- CKM_DSA_SHA224
- CKM_DSA_SHA256
- CKM_DSA_SHA384
- CKM_DSA_SHA512
- CKM_ECDSA
- CKM_GOSTR3410
- CKM_GOSTR3410_WITH_GOSTR3411

C_GenerateKey

- CKM_DSA_PARAMETER_GEN
- CKM_DH_PKCS_PARAMETER_GEN
- CKM_DES_KEY_GEN
- CKM_DES2_KEY_GEN
- CKM_DES3_KEY_GEN
- CKM_AES_KEY_GEN

C_GenerateKeyPair

- CKM_RSA_PKCS_KEY_PAIR_GEN
- CKM_DSA_KEY_PAIR_GEN
- CKM_DH_PKCS_KEY_PAIR_GEN
- CKM_EC_KEY_PAIR_GEN
- CKM_GOSTR3410_KEY_PAIR_GEN

C_WrapKey/C_UnwrapKey

- CKM_AES_KEY_WRAP
- CKM_AES_KEY_WRAP_PAD

C_DeriveKey

- CKM_DH_PKCS_DERIVE
- CKM_ECDH1_DERIVE
