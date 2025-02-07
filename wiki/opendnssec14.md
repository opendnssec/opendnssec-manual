## OpenDNSSEC 1.4 Documentation Home

### About

The OpenDNSSEC documentation gives information on how to install, configure, and run OpenDNSSEC. There might still remain some questions, so we try to reflect them in our a growing list of frequently asked questions.

Remember that you also need an HSM, which uses the PKCS#11 interface. We do provide the [SoftHSMv2](softhsm2), a software-only implementation of an HSM.

The latest version of OpenDNSSEC is 2.1.  Versions prior to 2.1 (like 1.3 and 1.4) are no longer supported.

### Scope

The goal of OpenDNSSEC is to have a complete DNSSEC zone signing system which maintains stability and security of signed domains. DNSSEC adds many cryptographic concerns to DNS; OpenDNSSEC automates those to allow current DNS administrators to adopt DNSSEC. This document provides DNS administrators with the necessary information to get the system up and running with a basic configuration.

### New in OpenDNSSEC 1.4

#### Major enhancements

DNS Adapters

OpenDNSSEC now supports both input and output adapters for AXFR and IXFR in addition to file transfer.
This requires migration for the zonefetch.xml which has been replaced by addns.xml to support this enhancement.
Also changes to the KASP database mean that a database migration is required to upgrade to 1.4 from earlier versions of OpenDNSSEC.

#### PIN Storage

The HSM PIN can now be omitted from the conf.xml file and entered via the 'ods-hsmutil login' command instead for increased security.

#### Auditor is deprecated

The auditor is no longer supported in 1.4. This greatly reduced the dependencies of OpenDNSSEC, namely it no longer depends on Ruby.

#### Minor enhancements

### Documentation downloads

- [OpenDNSSEC_Documentation_v1.4.pdf](assets/OpenDNSSEC_Documentation_v1.4.pdf)
- [OpenDNSSEC_Documentation_v1.4.html.zip](assets/OpenDNSSEC_Documentation_v1.4.html.zip)
- [OpenDNSSEC Documentation_v1.3.pdf](assets/OpenDNSSEC_Documentation_v1.3.pdf)
- [OpenDNSSEC Documentation_v1.3.html.zip](assets/OpenDNSSEC_Documenation_v1.3.html.zip)

