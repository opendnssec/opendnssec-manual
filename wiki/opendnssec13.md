## OpenDNSSEC 1.3 documentation

OpenDNSSEC documentation gives information on how to install, configure, and run OpenDNSSEC.

This documentation is for the 1.3 version of OpenDNSSEC.

![opendnssec13_workflow.png]

### Installation

Before you start to use OpenDNSSEC in your production environment you must first decide which hardware you going to run on.

When you have a good system to run on, then it is time to install the software that OpenDNSSEC depends on, and finally installing OpenDNSSEC.

#### Hardware set-up

Here are some short recommendations if you are planning to use OpenDNSSEC with many zones or a single large zone, and where the speed of signing is important.

- OpenDNSSEC is multi-threaded when it concerns the handling of multiple zone. But it is not currently multi-threaded in the handling of a single zone. So a multi-core machine will not give any benefits if you plan to only run a single large zone.
> :bulb:
> For handling on a single large zone is is therefore more important to go with a CPU that is fast rather than a CPU with many cores.
- The OpenDNSSEC signer engine makes backup files to recover your zone data with no loss. These backup files will use up approximately three times the size of the signed zone on the HDD. The zones are also stored in memory. To keep track of updates, OpenDNSSEC maintains a previous, current and a new version of the zone.

#### Platform support

OpenDNSSEC has been tested on the following platforms:

- Debian Linux 5.0
- Mac OS X 10.5
- NetBSD
- OpenBSD 4.4
- Red Hat Enterprise Linux 5
- Red Hat Enterprise Linux 6
- Solaris 10
- Ubuntu Linux 10.04

#### Dependencies

The following sections list the prerequisite software required for OpenDNSSEC. For Ubuntu users, the name of the package (where relevant) is listed.

> :warn:
> The version of the package available from the Ubuntu download sites may not be compatible with OpenDNSSEC; in that case, the latest version of the package should be obtained (and built if required).

Implicit in these sections is the assumption that the operating system has the following languages installed: C, C++, Ruby. If any are absent, consult the documentation for your operating system.

In all cases, a location from where to get a copy of the package source code and build instructions for the package are given.

> :warn:
> As there are some dependencies between the prerequisite components, they should be installed in the order listed here.

Note, where no version number is specified any fairly recent distribution (e.g. Ubuntu 10.04) will have a new enough version of the software in its standard repositories. Also note that these are minimum version numbers, so they provide API calls that we use; there may be bug fixes in later versions which are useful.

The minimum version needed for OpenDNSSEC 1.3.0 are:

- ldns: 1.6.9
- libxml2, libxml2-dev, libxml2-utils: 2.6.16
- ruby, rubygems
- dnsruby: 1.52
- libopenssl-ruby
- java: not required
- sqlite3, libsqlite3, libsqlite3-dev: 3.3.9
- (mysql-client, libmysqlclient15, libmysqlclient15-dev): 5.0.3

With regards to ldns furthermore:

- Version 1.3.5 and later require 1.6.12. (Note that an issue was also found where OpenDNSSEC 1.3.11 and earlier will not build against ldns 1.6.16 on platforms that rely on the OpenDNSSEC implementation of strlcpy/cat. This will be fixed in 1.3.12.)
- * Note that due to issues found in ldns version 1.3.11 (and later) of OpenDNSSEC does not support version 1.6.14 or 1.6.15 of ldns.

**ldns**

ldns is a DNS programming library used in the signer component.

To Ubunto users:  Make sure the the packages "libldns-x.z.y" (where "x.y.z" is the ldns version number) and "libldns-dev" are installed on your system.

Installing from source distribution instead:

Download a copy of ldns from <http://www.nlnetlabs.nl/downloads/ldns>.

When downloaded and unpacked, "cd" into the directory into which you have unpacked the tar file and issue the following commands:

    ./configure (--disable-gost)
    make
    sudo make install

This installs the ldns library in /usr/local/lib. If you require the software to be installed elsewhere, add the switch --prefix=<location> to the ./configure command.

**libxml2**

libxml2 is a C-library for handling XML. It is used in all parts of OpenDNSSEC.

To Ubunto users:  Install the packages "libxml2", "libxml2-dev", and "libxml2-utils".

Installing from	source distribution instead:

Download a copy of libxml2 from <http://xmlsoft.org/downloads.html>

When downloaded and unpacked, "cd" into the directory into which you have unpacked the tar file and issue the following commands:

    ./configure
    make
    sudo make install

This installs the libxml2 library in /usr/local/lib. If you require the software to be installed elsewhere, add the switch --prefix=<location> to the ./configure command.

**Ruby Gems**

Ruby Gems is the standard way of installing Ruby packages. It is only used for the installation of the DNSRuby package.

To Ubunto users:  Install the package "rubygems".

Installing from source distribution instead:

Download a copy of RubyGems from <http://rubyforge.org/projects/rubygems>.

When downloaded and unpacked, make sure that "ruby" is in your path, then issue the following command:

    sudo ruby setup.rb

... to install RubyGems. The command "gem" will be installed in the same place as the "ruby" command.

**dnsruby**

dnsruby is a third-party DNS library used by the Auditor.

There is no Ubuntu package for dnsruby. Instead, on all operating systems, install dnsruby using the command:

    sudo gem install dnsruby

**OpenSSL for Ruby**

The Auditor uses the OpenSSL library for some operations.

To Ubunto users:  Install the package "libopenssl-ruby".

Installing from source distribution instead:

Most operating systems seem to include this software as part of the operating system. Consult your documentation for more information.

**SQlite**

SQlite is a cut-down SQL database system, used by the KASP component of OpenDNSSEC.

To Ubunto users:  Install the packages "sqlite3" and "libsqlite3-dev".

Installing from source distribution instead:

When downloaded and unpacked, "cd" into the directory into which you have unpacked the tar file and issue the following commands:

    ./configure
    make
    sudo make install

This installs sqlite in /usr/local/bin. If you require the software to be installed elsewhere, add the switch --prefix=<location> to the ./configure command.

**MySQL**

>:bulb:
> You can choose to use MySQL instead of SQLite for the KASP database. This will give you better performance when handling thousands of zones.

To Ubunto users: Install the packages "mysql-client", "libmysqlclient15", "libmysqlclient15-dev".

Installing from source distribution instead:

Installing from Source Distribution
Download it from <http://dev.mysql.com/downloads/mysql>

At this site there are links for various different systems, or to the source code if you want to build the code yourself. Full documentation is also available from the download page.

### Pre-built Binaries

You can find information about packages for your operating system here: <http://www.opendnssec.org/download/packages/>

### Building from source

The latest version of OpenDNSSEC can be found as a tarball on <http://www.opendnssec.org>

The development (unstable) version of OpenDNSSEC is available from the GitHub repository and can be obtained using the following command:

    git clone https://github.com/opendnssec/OpenDNSSEC.git

1. If you downloaded the tarball then first untar it:
    tar -xzf opendnssec-<VERSION>.tar.gz
    cd OpenDNSSEC
   or if you are working from the repository:
    cd OpenDNSSEC
    sh autogen.sh
2. Then it is time to configure the build scripts:
    ./configure
   You may also need some other options to configure.
      --disable-auditor       Disable auditor build (default enabled)
  --enable-eppclient      Enable eppclient build (default disabled) (experimental)
  --enable-timeshift      For debugging purposes
  --with-database-backend Select database backend (sqlite3|mysql) (default sqlite)
   Use the following command to find out which other options that are available:
     ./configure --help
   The configure script defaults to --prefix=/usr/local, --sysconfdir=/etc, and --localstatedir=/var
3. Once configured, build OpenDNSSEC using:
    make
   ... and install using ...
    sudo make install
   > If the build fails it might be because of a missing software dependency. Please read the error messages carefully.
4. Post-installation
   Depending on operating system, there may be a few additional steps required after installation.
   Linux users need to rebuild the dynamic linker caches. To do this, issue the command:
    sudo ldconfig [library-path [library-path ...]]
   If OpenDNSSEC or any of the pre-requisites were installed in non-standard directories, the list of library paths should be specified as arguments on the command line.

### Configuration files

The default configuration installs good default values for anyone who just wants to sign their domains with DNSSEC. There are four configuration files for the basic OpenDNSSEC installation. You have 

- conf.xml which is the overall configuration of the system, 
- kasp.xml which contains the policy of signing, 
- zonelist.xml where you list all the zones that you are going to sign, 
- zonefetch.xml (which is optional) for zone transfers.

Click on the filenames below to see details of the file contents.

#### Date/time durations

All date/time durations in the configuration files are specified as defined by ISO 8601.

> Durations are represented by the format P[n]Y[n]M[n]DT[n]H[n]M[n]S. In these representations, the [n] is replaced by the value for each of the date and time elements that follow the [n]. Leading zeros are not required. The capital letters 'P', 'Y', 'M', 'D', 'T', 'H', 'M', and 'S' are designators for each of the date and time elements and are not replaced.

- P is the duration designator (historically called "period") placed at the start of the duration representation.
- Y is the year designator that follows the value for the number of years.
- M is the month designator that follows the value for the number of months.
- D is the day designator that follows the value for the number of days.
- T is the time designator that precedes the time components of the representation.
- H is the hour designator that follows the value for the number of hours.
- M is the minute designator that follows the value for the number of minutes.
- S is the second designator that follows the value for the number of seconds.

For example, "P3Y6M4DT12H30M5S" represents a duration of "three years, six months, four days, twelve hours, thirty minutes, and five seconds". Date and time elements including their designator may be omitted if their value is zero, and lower order elements may also be omitted for reduced precision. For example, "P23DT23H" and "P4Y" are both acceptable duration representations.

> :warn:
> Exception: A year or month vary in duration depending on the current date. For OpenDNSSEC, we assume fixed values:
> 
> - One month is assumed to be 31 days.
> - One year is assumed to be 365 days.

#### conf.xml

The overall configuration of OpenDNSSEC is defined by the contents of the file /etc/opendnssec/conf.xml. In this configuration file you specify logging facilities (only syslog is supported now), system paths, key repositories, privileges, and the database where all key and zone information is stored.

#### kasp.xml

kasp.xml - found by default in /etc/opendnssec - is the file that defines policies used to sign zones. KASP stands for "Key and Signature Policy”, and each policy details

- security parameters used for signing zones
- timing parameters used for signing zones

You can have any number of policies and refer to the proper one by name in for example the zonelist.xml configuration file.

#### zonelist.xml

The zonelist.xml file is used when first setting up the system, but also used by the ods-signerd when signing zones. For each zone, it contains a Zone tag with information about

- the zone's DNS name
- the policy from kasp.xml used to sign the zone
- how to obtain the zone
- how to publish the zone

#### zonefetch.xml

OpenDNSSEC can sign zonefiles on disk, but can also sign zones received from transfer (AXFR). If you configure a zone fetcher configuration, the Signer Engine will kick off the zone fetcher that will listen to NOTIFY messages from the parent and store AXFR messages on disk.

Information in this file details

- where to fetch zone data from
- protection mechanisms to be used

#### Checking your configuration files

The OpenDNSSEC XML configuration files (conf.xml and kasp.xml) offer the user many options to customise the OpenDNSSEC signing system. Not all possible configuration texts are meaningful however.

A tool (ods-kaspcheck)[opendnssec13#kaspcheck] is provided to check that the configuration files (conf.xml and kasp.xml) are semantically sane and contain no inconsistencies. 

> :bulb:
> It is advisable to use this tool to check your configuration before starting to use OpenDNSSEC.

#### Signer configuration

There are also xml files for each of the zones that the user wants to sign, but those are only used for communication between the Enforcer and the Signer Engine. And they are created automatically be the Enforcer. The location of these files can be found in zonelist.xml.

These are described in the signconf.xml configuration files.

### Configuration files details

#### kasp.xml

> :memo:
> KASP stands for "Key and Signature Policy"

kasp.xml (found by default in /etc/opendnssec) is the file that defines policies used to sign zones. Each policy comprises a series of parameters that define the way the zone is signed. This section explains the parameters by referring to the example kasp.xml file supplied with the OpenDNSSEC distribution.

**Preamble**

    <?xml version="1.0" encoding="UTF-8"?>
    <!-- $Id: kasp.xml.in 1154 2009-06-24 13:02:25Z jakob $ -->

Each XML file starts with a standard element "<?xml...". As with any XML file, comments are included between the delimiters "<!--"  and "-->".

**Policy Description**

    <KASP>

The enclosing element of the XML file is the element <KASP> which, with the closing element </KASP>, brackets one or more policies.

    ...
        <Policy name="default">

Each policy is included in the <Policy>...</Policy> elements. Each policy has a "name" attribute giving the name of the policy. The name is used to link a policy and the zones signed using it; each policy must have a unique name.

The policy named "default" is special, as it is associated with all zones that do not have a policy explicitly associated with them.

    ...
        <Description>A default policy that will amaze you and your friends</Description>

A policy can have a description associated with it. Unlike XML comments, the description can be understood by programs and may be used to document the policy, e.g. a future GUI may display a list of policies along with their description and ask you to select one for editing.

**Signatures**

The next section of the file is the Signatures section, which lists the parameters for the signatures created using the policy.

    ...
        <Signatures>
            <Resign>PT2H</Resign>
            <Refresh>P3D</Refresh>
            <Validity>
                    <Default>P7D</Default>
                    <Denial>P7D</Denial>
            </Validity>
            <Jitter>PT12H</Jitter>
            <InceptionOffset>PT300S</InceptionOffset>
        </Signatures>

Here:

- <Resign> is the re-sign interval, which is the interval between runs of the Signer Engine.
- <Refresh> is the refresh interval, detailing when a signature should be refreshed. As signatures are typically valid for much longer than the interval between runs of the signer, there is no need to re-generate the signatures each time the signer is run if there is no change to the data being signed. The signature will be refreshed when the time until the signature expiration is closer than the refresh interval. Set it to zero if you want to refresh the signatures each re-sign interval.
- <Validity> groups two elements of information related to how long the signatures are valid for - <Default> is the validity interval for all RRSIG records except those related to NSEC or NSEC3 records. In this case, the validity period is given by the value in the <Denial> element.
- <Jitter> is the value added to or extracted from the expiration time of signatures to ensure that not all signatures expire at the same time. The actual value of the <Jitter> element is the -j + r %2j, where j is the jitter value and r a random duration, uniformly ranging between -j and j, is added to signature validity period to get the signature expiration time.
- <InceptionOffset> is a duration subtracted from the time at which a record is signed to give the start time of the record. This is required to allow for clock skew between the signing system and the system on which the signature is checked. Without it, the possibility exists that the checking system could retrieve a signature whose start time is later than the current time.

The relationship between these elements is shown below. 

![assets/signature-lifetime.png]
![assets/reuse-of-signatures.png]

**Authenticated Denial of Existence**

Authenticated denial of existence - proving that domain names do not exist in the zone - is handled by the <Denial> section, as shown below:

    ...
        <Denial>
            <NSEC3>
                <TTL>PT3600S</TTL>
                <OptOut/>
                <Resalt>P100D</Resalt>
                <Hash>
                    <Algorithm>1</Algorithm>
                    <Iterations>5</Iterations>
                    <Salt length="8"/>
                </Hash>
            </NSEC3>
        </Denial>

<Denial> includes one element, either <NSEC3> (as shown above) or <NSEC>.

NSEC3:

- <NSEC3> tells the signer to implement NSEC3 scheme for authenticated denial of existence (described in RFC 5155). The elements are:
- <TTL> - if present, use this duration for NSEC3PARAM TTL. If not present, PT0S (0) will be used as TTL (included since version 1.3.16).
  > This will only affect the time-to-live value for the NSEC3PARAM resource records. The time-to-live value for NSEC3 records is set to the value of the SOA <Minimum>.
- <OptOut/> - if present, enable "opt out". This is an optimisation that means that NSEC3 records are only created for authoritative data or for secure delegations; insecure delegations have no NSEC3 records. For zones where a majority of the entries are delegations that are not signed - typically TLDs during the take-up phase of DNSSEC - this reduces the number of DNSSEC records in the zone.
- <Resalt> is the interval between generating new salt values for the hashing algorithm.
- <Algorithm>, <Iterations> and <Salt> are parameters to the hash algorithm, described in (RFC5155)[http://tools.ietf.org/html/rfc5155].

NSEC:

- Should, instead, NSEC be used as the authenticated denial of existence scheme, the <Denial> element will contain the single element <NSEC/> - there are no other parameters.

**Key Information**

Parameters relating to keys can be found in the <Keys> section.

    ...
        <Keys>

Common Parameters:

The section starts with a number of parameters relating to both zone-signing keys (ZSK) and key-signing keys (KSK):

    ...
            <TTL>PT3600S</TTL>
            <RetireSafety>PT3600S</RetireSafety>
                        <PublishSafety>PT3600S</PublishSafety>
            <ShareKeys/>
            <Purge>P14D</Purge>

- <TTL> is the time-to-live value for the DNSKEY resource records.
- <PublishSafety> and <RetireSafety> are the publish and retire safety margins for the keys. These intervals are safety margins added to calculated timing values to give some extra time to cover unforeseen events, e.g. in case external events prevent zone publication.

If multiple zones are associated with a policy, the presence of <ShareKeys/> indicates that a key can be shared between zones. E.g. if you have 10 zones then you will only use one set of keys instead of 10 sets. This will save space in your HSM. If this tag is absent, keys are not shared between zones.

If <Purge> is present, keys marked as dead will be automatically purged from the database after this interval.

**Key-Signing Keys**

Parameters for key-signing keys are held in the <KSK> section:

    ...
            <KSK>
                <Algorithm length="2048">7</Algorithm>
                <Lifetime>P1Y</Lifetime>
                <Repository>softHSM</Repository>
                <!-- <Standby>1</Standby> (Experimental) -->
                <ManualRollover/>
            </KSK>

- <Algorithm> determines the algorithm used for the key (the numbers reserved for each algorithm can be found in the appropriate IANA registry).
- <Lifetime> determines how long the key is used for before it is rolled.
- <Repository> determines the location of the keys. Keys are stored in "repositories" (currently, only hardware security modules (HSMs) or devices conforming to the PKCS#11 interface), which are defined in the conf.xml. In the example above, the key is stored in softHSM - the example configuration file distributed with OpenDNSSEC defines this as being the software emulation of an HSM distributed as part of OpenDNSSEC.
- <Standby> **Experimental** (we are currently not supporting offline HSM, which is needed to get the security level needed to fulfill the idea behind standby keys. Will be fixed in a future version) Determines the number of standby keys held in the zone. These keys allow the currently active key to be immediately retired should it be compromised, so enhancing the security of the system. (Without an standby key, additional time is required to allow information about the new key to reach validator caches - see <http://tools.ietf.org/html/draft-ietf-dnsop-dnssec-key-timing-02> for timing details.)
<ManualRollover/> is an optional tag. This tag indicate that the key rollover will only be initiated on the command by the operator. There is still a second step for the KSK, where the key needs to be published to the parent before the rollover is completed. Read more in the chapter "Running OpenDNSSEC". The ZSK rollover will although be fully automatic if this tag is not present.

**Zone-Signing Keys**

Parameters for zone-signing keys are held in the <ZSK> section, and have the same meaning as for the KSK:

    ...
            <ZSK>
                <Algorithm length="1024">7</Algorithm>
                <Lifetime>P14D</Lifetime>
                <Repository>softHSM</Repository>
                <!-- <Standby>1</Standby> (Experimental) -->
            </ZSK>

The ZSK information completes the contents of the <Keys> section.

    ...
        </Keys>

**Zone Information**

General information concerning the zones can be found in the <Zone> section:

    ...
        <Zone>
            <PropagationDelay>PT9999S</PropagationDelay>
            <SOA>
                <TTL>PT3600S</TTL>
                <Minimum>PT3600S</Minimum>
                <Serial>unixtime</Serial>
            </SOA>
        </Zone>

- <PropagationDelay> is the amount of time needed for information changes at the master server for the zone to work its way through to all the secondary nameservers.
- The <SOA> element gives values of parameters for the SOA record in the signed zone. 
  > :memo:
  > These values will override values set for the SOA record in the input zone file.
  The values are:
  - <TTL> - TTL of the SOA record.
  - <Minimum> - value for the SOA's "minimum" parameter.
  - <Serial> - the format of the serial number in the signed zone. This is one of:
    - counter - use an increasing counter (but use the serial from the unsigned zone if possible)
    - datecounter - use increasing counter in YYYYMMDDxx format (xx is incremented within each day)
    - unixtime - the serial number is set to the "Unix time" (seconds since 00:00 on 1 January 1970 (UTC)) at which the signer is run.
    - keep - keep the serial from the unsigned zone (do not resign unless it has been incremented)

**Parent Zone Information**

If a DNSSEC zone is in a chain of trust, digest information about the KSKs used in the zone will be stored in DS records in the parent zone. To properly roll keys, timing information about the parent zone must be configured in the <Parent> section:

    ...
        <Parent>
            <PropagationDelay>PT9999S</PropagationDelay>
            <DS>
                <TTL>PT3600S</TTL>
            </DS>
            <SOA>
                <TTL>PT3600S</TTL>
                <Minimum>PT3600S</Minimum>
            </SOA>
        </Parent>

- <PropagationDelay> is the interval between the time a new KSK is published in the zone and the time that the DS record appears in the parent zone.
- The <DS> tag holds information about the DS record in the parent. It contains a single element, <TTL>, which should be set to the TTL of the DS record in the parent zone.
- <SOA> gives information about parameters of the parent's SOA record, used by KASP in its calculations. As before, <TTL> is the TTL of the SOA record and <Minimum> is the value of the "minimum" parameter.

**Auditing**

The zone will be audited before it is written to the signed directory, if the following tag is included.

> :warn:
> If you are signing a large number of zones and have a high work load on your server, the memory resources might get exhausted because each instance of the auditor has its own Ruby VM.

    ...
        <Audit />

If you are signing a very large zone (more than half a million records, for example), then you may wish to use the Partial Auditor. This checks a sample of the zone (rather than every RRSet cryptographic signature), and performs many of the same checks as the full auditor (including key lifetime tracking). To enable this, replace the above Audit tag with :

    ...
        <Audit>
            <Partial />
        </Audit>

This is the last section of the policy specification, so the next element is the policy closing tag:

    ...
        </Policy>

If there are any additional policies, they could be entered here, starting with <Policy> and ending with </Policy>. However, in this case there are no additional policies, so the file is ended by closing the <KASP> tag:

    </KASP>

#### signconf.xml

There are xml files for each of the zones that the system is configured to sign. These define the API between the Enforcer and the Signer Engine. The Enforcer creates these files while implementing the policies and key management and the Signer Engine reads them when signing zones.

> :warn:
> These files are should not be created or modified by users. However inspection of the content can be useful to troubleshoot problems.

The location of these files can be found in zonelist.xml, but we refer to signconf.xml if we speak about the general signer configuration file. The actual location of these files can be found in zonelist.xml.

**Preamble**

    <?xml version="1.0" encoding="UTF-8"?>
    <!-- $Id: signconf.xml.in 3061 2010-03-16 19:23:51Z jakob $ -->

Each XML file starts with a standard element "<?xml...". As with any XML file, comments are included between the delimiters "<!--" and "-->".

**Configuration per zone**

    <SignerConfiguration>

The enclosing element of the XML file is the element <SignerConfiguration?> which, with the closing element </SignerConfiguration>, brackets one signer configuration.

    ...
    <Zone name="opendnssec.org">

Each zone is included in the <Zone>...</Zone> elements. Each zone has a "name" attribute giving the name of the zone.

**Signatures**

    ...
        <Signatures>
            <Resign>PT2H</Resign>
            <Refresh>P3D</Refresh>
            <Validity>
                <Default>P7D</Default>
                <Denial>P7D</Denial>
            </Validity>
            <Jitter>PT12H</Jitter>
            <InceptionOffset>PT300S</InceptionOffset>
        </Signatures>

For more information on the meaning of the elements and their elements, take a look at KASP policy file.

**Authenticated Denial of Existence**

Authenticated denial of existence - proving that domain names do not exist in the zone - is handled by the <Denial> section, as shown below:

    ...
 
        <Denial>
            <NSEC3>
                <TTL>PT3600S</TTL>
                <OptOut/>
                <Resalt>P100D</Resalt>
                <Hash>
                    <Algorithm>1</Algorithm>
                    <Iterations>5</Iterations>
                    <Salt length="8"/>
                </Hash>
            </NSEC3>
        </Denial>

<Denial> includes one element, either <NSEC3> (as shown above) or <NSEC>.

NSEC3:

- <NSEC3> tells the signer to implement NSEC3 scheme for authenticated denial of existence (described in RFC 5155). The elements are:
- <TTL> - if present, use this duration for NSEC3PARAM TTL. If not present, PT0S (0) will be used as TTL (included since version 1.3.16).
  > This will only affect the time-to-live value for the NSEC3PARAM resource records. The time-to-live value for NSEC3 records is set to the value of the SOA <Minimum>.
- <OptOut/> - if present, enable "opt out". This is an optimisation that means that NSEC3 records are only created for authoritative data or for secure delegations; insecure delegations have no NSEC3 records. For zones where a majority of the entries are delegations that are not signed - typically TLDs during the take-up phase of DNSSEC - this reduces the number of DNSSEC records in the zone.
- <Resalt> is the interval between generating new salt values for the hashing algorithm.
- <Algorithm>, <Iterations> and <Salt> are parameters to the hash algorithm, described in RFC 5155.

NSEC:

- Should, instead, NSEC be used as the authenticated denial of existence scheme, the <Denial> element will contain the single element <NSEC/> - there are no other parameters.

**Key Information**

Parameters relating to keys can be found in the <Keys> section.

    ...
            <Keys>

Common Parameters:

The section starts with a common parameter, TTL:

    ...
            <TTL>PT3600S</TTL>

<TTL> is the time-to-live value for the DNSKEY resource records.

Keys:

Each key has a number of elements so the signer knows how the keyset should be used and published.

Those are held in the <Key> section:

    ...
            <Key>
                <Flags>257</Flags>
                <Algorithm>5</Algorithm>
                <Locator>DFE7265B783F418685380AA784C2F31D</Locator>
                <Publish/>
                <KSK/>
                <ZSK/>
            </Key>

- <Flags> tells the signer what value it should set on this key when publishing the corresponding DNSKEY resource record.
- <Algorithm> determines the algorithm used for the key (the numbers reserved for each algorithm can be found in the appropriate (IANA)[http://www.iana.org/assignments/dns-sec-alg-numbers/dns-sec-alg-numbers.xhtml] registry).
- <Locator> stores the CKA_ID of the key, e.g. the location of the physical key.- <KSK/> - if present, use key as KSK. If a key is used as KSK, it must sign the DNSKEY RRset. It should not sign other RRsets, unless the <ZSK/> element is also present.
- <ZSK/> - if present, use key as ZSK. If a key is used as ZSK, it must sign all other RRsets. It should not sign the DNSKEY RRset, unless the <KSK/> element is also present.
- <Publish/> - if present, publish the corresponding DNSKEY resource record in the zone. The Public Key RDATA should be retrieved from the HSM using the CKA_ID (<Locator>).

The keyset is closed with the tag:

    ...
        </Keys>

**Source of Authority**

When automating DNSSEC, the SOA record needs to be adjusted from time to time. The necessary parameters can be found in the <SOA> section:

    ...
        <SOA>
            <TTL>PT3600S</TTL>
            <Minimum>PT3600S</Minimum>
            <Serial>unixtime</Serial>
        </SOA>

> :warn:
> These values will override values set for the SOA record in the input zone file.

The values are:

- <TTL> - TTL of the SOA record.
- <Minimum> - value for the SOA's MINIMUM RDATA element.
- <Serial> - the format of the serial number in the signed zone. This is one of:
  - counter - use an increasing counter (but use the serial from the unsigned zone if possible)
  - datecounter - use increasing counter in YYYYMMDDxx format (xx is incremented within each day)
  - unixtime - the serial number is set to the "Unix time" (seconds since 00:00 on 1 January 1970 (UTC)) at which the signer is run.
  - keep - keep the serial from the unsigned zone (do not resign unless it has been incremented)

**Auditing**

The zone will be audited before it is written to the signed directory, if the following tag is included.

    ...
        <Audit />

This is the last section of the signconf specification, so the next element is the zone closing tag:

    ...
    </Zone> 

and the file is ended by closing the <SignerConfiguration> tag:

    </SignerConfiguration>

### Running OpenDNSSEC 1.3

This section describes how to start OpenDNSSEC and the operations used to manage and monitor the system.

Details on the command utilities are described in a separate section.

> :bulb:
> All directories are prepared by the build script and are set to be owned by root, so all commands in the default configuration must also be run by root. To change this, alter the configuration or privileges on the files and directories.

#### Before starting OpenDNSSEC for the first time

Before you run the system for the first time you must import your policy and zone list into the database using the following command:

    ods-ksmutil setup

After running this the first time, you will be ready to run OpenDNSSEC with an empty set of zones. On the other hand, if this command is run on an existing database, then will all meta-information about the zones be lost. The keys would then still exist in HSMs, so you should not forget to clean them up.

#### Starting / Stopping the system

OpenDNSSEC consist of two daemons, ods-signerd and ods-enforcerd. To start and stop them use the following commands:

    ods-control start

A proper-looking response to this commands is:

    Starting enforcer...
    OpenDNSSEC ods-enforcerd started (version 1.2.0b1), pid 11424
    Starting signer engine...
    OpenDNSSEC signer engine version 1.2.0b1

At any time, you can stop OpenDNSSEC's daemons orderly with:

    ods-control stop

After this, your logs should contain messages like:

    Stopping enforcer...
    Stopping signer engine..
    Engine shut down.

#### Uploading a Trust Anchor (Publish of DS to the parent)

Your zone will be signed, once you have setup the system and started it. When you have verified that the zone is operational and working, it is time to upload the trust anchor to the parent zone. The Enforcer is waiting for zone to be connected to the trust chain before considering the KSK to be active.

    ods-ksmutil key list --verbose
     
    Keys:
    Zone:                           Keytype:      State:    Date of next transition:  CKA_ID:                           Repository:                       Keytag:
    example.com                     ZSK           active    2010-10-15 06:59:28       92abca348b96aaef42b5bb62c8daffb0  softHSM2                          28743
    example.com                     KSK           ready     waiting for ds-seen       9621ca39306ce050e8dd94c5ab837001  softHSM1                          22499

1. Export the public key either as DNSKEY or DS, depending on what format your parent zone wants it in. See the section Export the public keys, on how to get the key information.
   > This step can be automated or semi-automated by placing a command in the <DelegationSignerSubmitCommand> tag. This should point to a binary which will accept the required key(s) as DNSKEY RRs on STDIN.
2. Notify the Enforcer when you can see the DS RR in your parent zone. You usually give the keytag to the Enforcer, but if there are KSKs with the same keytag then use the CKA_ID.
    ods-ksmutil key ds-seen -z example.com -x 22499
   or
    ods-ksmutil key ds-seen -z example.com -k 9621ca39306ce050e8dd94c5ab837001
   resulting in:
    Found key with CKA_ID 9621ca39306ce050e8dd94c5ab837001
Key 9621ca39306ce050e8dd94c5ab837001 made active
   And you will see that your KSK is now active:
    ods-ksmutil key list
     
    Keys:
    Zone:                           Keytype:      State:    Date of next transition:
    example.com                     ZSK           active    2010-10-15 07:20:53
    example.com                     KSK           active    2010-10-15 07:31:03

#### Zone Management

**Adding / Removing zones***

Zones can be added and removed at will. If the optional parameters are not given, then it will default to the policy default and the /var/opendnssec/ subdirectories.

    ods-ksmutil zone add --zone example.com [--policy <policy> --signerconf <signerconf.xml> --input <input> --output <output>]
    ods-ksmutil zone delete --zone example.com

This command will report positively with a message like:

    zonelist filename set to /etc/opendnssec/zonelist.xml.
    SQLite database set to: /var/opendnssec/kasp.db
    Imported zone: example.com

> :warn:
>  Using this command thousands of times might be slow since it also writes to zonelist.xml. Use --no-xml to stop this behavior. Then export the zonelist when you are finished: 
>     ods-ksmutil zonelist export > zonelist.xml

Alternatively, you could manually edit the zonelist.xml and then give the command:

    ods-ksmutil update zonelist

After zones are added, they will show up in your logs as follows:

    ods-enforcerd: Zone example.com found.
    ods-enforcerd: Policy for example.com set to default.
    ods-enforcerd: Config will be output to /var/opendnssec/signconf/example.com.xml.

If you opened the latter file, you would find the settings that were applied to the zone at the time this file was added.

**Updating an unsigned zone**

When you update the content of an unsigned zone you must tell the signer engine to re-read the unsigned zone file using the ods-signer command like this:

    ods-signer sign example.com

This also have the effect that you schedule the zone for immediate resigning.

#### Key Management

**Marking keys as backed up**

You can configure the system to only make keys active once they have been backed up. This is done by editing the conf.xml file. The user must do backups and then notify OpenDNSSEC about this, so that the key rollover process can continue. The keys must be backed up regulary, because OpenDNSSEC is generating new keys prior to a rollover.

1. First prepare the backup by telling the Enforcer that you want to do backup of the keys. This is so that keys generated after you have done your backup won't accidentally be marked as backed up.  
   For all of the repositories:
    ods-ksmutil backup prepare
   or a single repository:
    ods-ksmutil backup prepare --repository <repository>
2. Then you can safely do your backups. Please read the documentation of your HSM for instructions on how to do backups.  When you are done, then notify the Enforcer about this:  
   For all of the repositories:
    ods-ksmutil backup commit
   or a single repository:
    ods-ksmutil backup commit --repository <repository>
   > :warn:
   > The command ods-ksmutil backup done will mark your keys as backed up in one step. This means that keys may have been generated between you doing the backup and giving the command. Thus accidentally marking them as backed up. This command is deprecated and should not be used, unless you make sure to stop the Enforcer when doing your backup.

Backups are specified by way of a repository option in conf.xml:

    <RequireBackup/>

If you decide you want to change this facility, you should edit conf.xml accordingly, and run:

    ods-ksmutil update conf

It will report something along the lines of:

    RequireBackup set.

**Export the public keys**

You need to publish your key to the parent or to interested parties. If you are doing a key rollover, then only the ready KSK should be exported. The following command will extract the trust anchors:

    ods-ksmutil key export --zone example.com [--keystate READY]
    ods-ksmutil key export --zone example.com --ds [--keystate READY]

What you get in return is the DNSKEY or DS in BIND-format.

**Key rollovers**

First step

> :memo:
> This step is not needed for a scheduled rollover.  
>   
> The rollovers are done automatically according to the policy of the zone. But a manual keyrollover may be desired in cases of emergency, such as having lost a private key.

A manual rollover can be done using the ods-ksmutil command like this:

    ods-ksmutil key rollover --zone example.com --keytype KSK

This will roll the KSK key in a timely manner following the policy used for the zone example.com. If you want to roll the Zone Signing Key use --keytype ZSK instead.

You can also roll all the keys for zones which have a certain policy. This can be useful if you want to move all keys from one key store to another.

    ods-ksmutil key rollover --policy default --keytype KSK

Second step for KSKs - Publish the DS to the parent

Unlike ZSKs, a KSK rollover requires a second step involving manual intervention. This intervention is a multi-stage process. First, the DNSKEY record for the new key is added to the zone. Then, after a suitable interval, the new DS record is submitted to the parent; at this point the old DS record can be removed from the parent.

The stages are:

1. Extract the DNSKEY record for the new key and publish it in the parent zone. (The new record replace any existing records for the zone being signed.) When it is time for this to happen a message with log-level "info" will be sent to syslog looking something like:

    Mar 16 11:39:05 sion ods-enforcerd: DS Record set has changed, the current set looks like:
    Mar 16 11:39:05 sion ods-enforcerd: example.com. 3600 IN DNSKEY 257 3 7 AwEAAbcTSmphJUMKvegvDgqGspRM8IHlKZqoU5pkPaTtRLkioxGyZ5iIh4bNnvqmx1zWIttuJ6erGUMOatMm3SXxiTr9OLaRPr86KVpo6mzejTqFicGxSp3KsrbUvyIs/V84Ry7XZBKVKVjgppjmqeS8mRtXM4UynwTEJk0hKQfCcmkH0Q/fhZibwBVG+OcBfvTdsQbp8LZN4oVqn/vzhnuxFkE8biTr19jmKTdtgkhp524ML59v7prg7F/+Lb2OJLc8Gg6pastUeqXc/Iv2CdVyOvMWRW39VCzyLbKpmyqB8Hc4Kn1pT5Idqc3/N3qBvXVe3HyyiZbjHGxOT6RZNNT8= ;{id = 51994 (ksk), size = 2048b}
    Mar 16 11:39:05 sion ods-enforcerd: Once the new DS records are seen in DNS please issue the ds-seen command for zone example.com with the following cka_ids, 04260cd6eac67280cd2dea94c6e38cb7

   The DNSKEY or DS RR can also be retrieved by using the commands in the section Export the public keys. 
   > :bulb:
   > This step can be automated or semi-automated by placing a command in the <DelegationSignerSubmitCommand> tag. This should point to a binary which will accept the required key(s) as DNSKEY RRs on STDIN.

2. When the records indicated have been seen in DNS then this can be communicated to OpenDNSSEC with the ds-seen command as indicated:

    ods-ksmutil key ds-seen --zone example.com --cka_id 04260cd6eac67280cd2dea94c6e38cb7

3. If the DS records were not swapped, i.e. the old DS was left in the parent when the new one was added, then the --no-retire flag can be added to the ds-seen command. Then, at some later time, the old key can be retired with the command:

    ods-ksmutil key ksk-retire --zone example.com ---cka_id 87f1385b114f9f9b299e6b551d728bfb

   or

    ods-ksmutil key ksk-retire --zone example.com

   The former command will retire the specific key (provided the key is active, and the action will not leave the zone without any active keys). The latter command will retire the oldest active key on the zone, again provided it will not leave the zone without any active keys.
   > If you wish to run like this and use the DelegationSignerSubmitCommand hook then you will need to add the current key back into the set yourself.

Key rollovers on exact dates

Some users want to have more control over their key rollovers and roll keys on exact dates, for example the first day of each month. To do this you need to specify that you want manual key rollovers in the kasp.xml configuration. Add the <ManualRollover/> tag to the type and key you want to roll manually.

When this is done you can add the rollover commands to a cron job, with a command like this:

    ods-ksmutil key rollover --zone example.com --keytype ZSK

#### Updating the xml config files (including the KASP policy)

When you make changes to conf.xml, kasp.xml or zonelist.xml you must run the

    ods-ksmutil update all

command (or the appropriate command listed below) in order for the changes to be propagated to the system database.

- If you make changes to the enforcer or auditor section of the conf.xml file then you must run
    ods-ksmutil update conf
  For most other changes to the conf.xml file it is advisable to stop and start OpenDNSSEC to ensure the changes are detected. 
- When you make changes to a policy or add a new policy in kasp.xml you must update the changes to the database.
    ods-ksmutil update kasp
  When making changes to the KASP policy the following should also be considered:
  - It is advised not to update policy details (in particular propagation times) while a rollover is in progress.
  - Changing the algorithm used in a policy is not supported in 1.3 or 1.4.
  - Certain policy changes e.g. changing 'Standby keys" from 'on' to 'off' may lead to orphaned keys.
  - After updating signature timers in the policy it may be helpful to issue the command ( as it will speed up acclimatising timers for the signatures.):
    ods-signer clear <zone>; ods-signer sign <zone>
- If you add zones directly into the zonelist (rather than using the ods-ksmutil zone add command) you must tell the enforcer to re-read the zone list by using the command:
    ods-ksmutil update zonelist

#### Monitoring the system

- The pids used by the enforcer and signer processes are reported in syslog on startup.
- The command 'ods-signer running' will report the status of the signer process, or restart it if it is not running.
- When the enforcer daemon has run and completed enforcing the zones is sends a message to the syslog containing the text "Sleeping for" reporting how long it will be until it next runs 
- The signer produces a log containing the text "[STAT]" whenever a zone is successfully signed
- A Nagios plugin is available to check signed zones: <https://github.com/opendnssec/dnssec-monitor>

Currently all logging is handled by syslog. Other logging methods may come later.

There is a lot of output from the daemons of which a lot of things right now are for debugging. We might reduce the amount of logging for later versions of OpenDNSSEC.

The signer outputs some statistics into the logs:

    [STATS] opendnssec.org RR[count=32 time=1(sec)] NSEC[count=32 time=1(sec)] RRSIG[new=1 reused=31 time=1(sec) avg=1(sig/sec)] AUDIT[time=2(sec)] TOTAL[time=5(sec)]

The RR[...] block says something about reading the unsigned zone file. The line above tells us that there have been 32 RRs read and it took about one second. The count will be 0 on lines were only re-signing occurred.

The NSEC[...] block says something about the number of NSEC or NSEC3 records created on the latest run. This count will also be 0 on lines were only re-signing occurred.

The RRSIG[...] block says something about the number of created signatures. In the above example, the last time there was only created one new signature, and 31 other signatures were reused. The re-signing was finished in about a second and thus the average number of signatures created per second is in this example 1.

The AUDIT[...] block tells us how long the auditor needed to check upon the zone.

The TOTAL[...] block tells us how long the re-signing took, including reading the unsigned zone and adding NSEC(3) records, if applicable.

#### Troubleshooting

There are a number of common issues that are straightforward to diagnose and fix... If OpenDNSSEC is not behaving as expected then the first place to look is in the logs. Where these will be depends on your system and your configuration.

The following are log messages which you may see, and what to do about them (if anything).

**Enforcer**

??? ods-enforcerd: ERROR: Trying to make non-backed up ZSK active when RequireBackup flag is set 
     This is not an error as such. It means that in conf.xml you have indicated that keys should not be used unless they are backed up. However, the enforcer has determined that if it continues then a non backed up key will be made active.  
     The solution Take a backup of your keys (how this is done will depend on your key storage).  
      Once this has been done then run ods-ksmutil backup done to mark all keys as having been backed up. 

??? ods-enforcerd: WARNING: Making non-backed up KSK active, PLEASE make sure that you know the potential problems of using keys which are not recoverable 
    This is the same as above, but without RequireBackup being set in conf.xml 

??? ods-enforcerd: WARNING: key rollover not completed as there are no keys in the ready state: ods-enforcerd will try again when it runs next
    This is seen when a rollover is happening but there is no replacement key ready (because one has not been published for long enough). It indicates that the rollover will be delayed until the replacement key is ready, the time that this will happen depends on the policy.

??? ods-enforcerd: Could not call signer engine 
     If the enforcer makes a change to a zones signer configuration (say it adds a new key) it calls the signer to get it to resign that zone. This message indicates that the signer is not running, although it has been seen on a system where everything is working fine. (See KNOWN_ISSUES.)

??? ods-enforcerd: Not enough keys to satisfy zsk policy for zone 
    One of these messages will be seen if the enforcer does not have enough unallocated keys to provide for the zone specified. If the ManualKeyGeneration tag is set in conf.xml then you will need to create new keys using ods-ksmutil key generate, otherwise new keys will be created when the enforcer runs next. (Don't forget to backup any new keys.)

??? ods-enforcerd: Rollover of KSK expected at <DATE TIME> for <ZONE> 
    This is not an error, but a notification of an upcoming (scheduled) rollover. This will appear in your logs at a time prior to the rollover as configured in conf.xml (theEnforcer/RolloverNotification tag).

??? ods-enforcerd: WARNING: KSK Retirement reached; please submit the new DS for <ZONE> and use ods-ksmutil key ksk-roll to roll the key. 
     Rolling a KSK requires the DS record of the replacement key to be published in the parent of the zone. This message indicates that your KSK has reached the end of its life (as specified by your policy), and that it is time to submit the DS record to the parent.

??? ods-enforcerd: Error: database in config file <path_to_conf.xml> does not match libksm 
    This indicates that either you have libksm built for sqlite, but have specified a MySQL database in conf.xml, or vice versa.  
    The solution is to either rebuild libksm or to change conf.xml
