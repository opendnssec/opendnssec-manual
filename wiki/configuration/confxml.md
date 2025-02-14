## conf.xml

The overall configuration of OpenDNSSEC is defined by the contents of the file `/etc/opendnssec/conf.xml`. In this configuration file you specify logging facilities (only syslog is supported now), system paths, key repositories, privileges, and the database where all key and zone information is stored.

### Preamble

    <?xml version=`1.0` encoding=`UTF-8`?>

Each XML file starts with a standard element `<?xml...`. As with any XML file, comments are included between the delimiters `<!--` and  `-->`.

    <Configuration>
       ...
    </Configuration>

The enclosing element of the XML file is the element `<Configuration>` which, with the closing element `</Configuration>`, brackets the configuration information.

### Repositories

    <RepositoryList>

OpenDNSSEC stores its keys in `repositories`. There must be at least one repository, although the system can be configured to run with multiple repositories.

There are a number of reasons for running with multiple repositories, including:

- Temporarily running with multiple repositories whilst a switch is made from one repository to another, e.g. when replacing hardware.
- The chosen type of repository has a limit on the number of keys that can be stored, and multiple repositories are needed to handle the chosen number of keys.
- Different types of repository are needed for different security levels, e.g. a key-signing key may require a higher level of security than a zone-signing key.

In practice, all repositories are `HSM`s (hardware security modules) or an emulation of one.

### SoftHSM

The software emulation of an HSM - SoftHSM - must be installed separately but is part of the OpenDNSSEC project, and its definition is shown below. The element names will be explained later.

    <Repository name=`SoftHSM`>
        <Module>/usr/local/lib/libsofthsm.so</Module>
        <TokenLabel>OpenDNSSEC</TokenLabel>
        <PIN>1234</PIN>
    </Repository>

### HSM

As indicated, any number of repositories can be specified in the configuration file:

    <Repository name=`sca6000`>
        <Module>/usr/lib/libpkcs11.so</Module>
        <TokenLabel>Sun Metaslot</TokenLabel>
        <PIN>test:1234</PIN>
        <RequireBackup/>
        <SkipPublicKey/>
    </Repository>

(The example above is commented out by the XML comment delimiters.)

- `<Repository>` - the definition of a repository is bracketed by the `<Repository>` and `</Repository>` elements. The name attribute must be supplied and must be unique. It is this name that is used in the kasp.xml file to identify which repository holds the keys. This field is limited to 30 characters.
- `<Module>` identifies the dynamic-link library that controls the repository. Each type of HSM will have its own library.
- `<TokenLabel>` identifies the `token` within the HSM that is being used - essentially a form of sub-repository. The token label is also used where there are two repositories of the same type, in that each repository should contain a different token label sub-repository. OpenDNSSEC will automatically go to the right HSM based on this. This field is limited to 32 characters.
- `<PIN>` is an optional element containing the password to the HSM. OpenDNSSEC have this stored en-claire in the configuration file. If the PIN is not present in the configuration file, then it must be entered by using the command ods-hsmutil login.
- `<RequireBackup>` is an optional element that specifies that keys from this repository may not be used until they are backed up. If backup has been done, then use ods-enforcer backup commit to notify OpenDNSSEC about this. The backup notification is needed for OpenDNSSEC to be able to complete a key rollover. (If the backup is not done then the old key will remain in use.)
- `<SkipPublicKey>` is an optional element which specifies that the public key objects should not be stored or handled in the HSM. The public key is needed in order to create the DNSKEY RR. In theory, the public part of the key is also available in the private key object. However, the PKCS#11 API does not require the HSM to behave in this way. We have not seen a HSM where we cannot do this, but you should remove this flag if you are having any problem with it. The benefit of adding this flag is that you save space in your HSM, because you are only storing the private key object.

### Common

These are the configuration that is shared among the components of OpenDNSSEC.

    <Common>
        <Logging>
            <Verbosity>3</Verbosity>
            <Syslog><Facility>local0</Facility></Syslog>
        </Logging>
        <PolicyFile>/etc/opendnssec/kasp.xml</PolicyFile>
        <ZoneListFile>/etc/opendnssec/zonelist.xml</ZoneListFile>
    </Common>

- All components of OpenDNSSEC logs errors, warnings and progress information. This is configurable and defined in the `<Logging>` element. Currently, the only syslog() feature configurable via the OpenDNSSEC configuration file is the facility name under which messages are logged. This can be any of the names listed in the operating system's syslog header file (usually `/usr/include/sys/syslog.h`, but the location is system dependent). 
- `<Verbosity>` controls the level of logging, 0 will disable logging and 3 (default level) will provide informational log messages. You can set it higher to get debug log messages.


Although any facility listed there can be used, it is recommended that one of the `local` facilities (usually `local0` through `local7`) be used.

Then you also have pointers to where the policy and zone list files can be found. There are also an optional element where you specify the path of the zone fetch configuration used for inbound AXFR.

### Enforcer

The KASP enforcer component of OpenDNSSEC - which deals with key rollover and key generation - has its own section in the configuration file:

    <Enforcer>
        <Privileges>
            <User>opendnssec</User>
            <Group>opendnssec</Group>
        </Privileges>
        <PidFile>/var/run/opendnssec/enforcerd.pid</PidFile>
        <WorkerThreads>4</WorkerThreads>
        <Datastore><SQLite>/var/opendnssec/kasp.db</SQLite></Datastore>
    </Enforcer>

- ``<Privileges>`` The Enforcer can drop its privileges if specified.
- ``<PidFile>`` The location of the pidfile.
- ``<WorkerThreads>`` Number of threads spawned to execute tasks in the Enforcer. 
- ``<Datastore>`` The database used by the Enforcer. OpenDNSSEC supports SQLite and MySQL (MySQL is the recommended choice for production environments), the choice being indicated by one of two mutually exclusive tags SQLite and MySQL. 

    <Datastore>
        <MySQL>
            <Host Port=`1213`>dnssec-db</Host>
            <Database>KASP</Database>
            <Username>kaspuser</Username>
            <Password>abc123</Password>
        </MySQL>
    </Datastore>

- ``<Host>`` is the name of the system on which the database resides. It is optional - if omitted, the database is assumed to run on the same system as OpenDNSSEC. The `Port` attribute gives the port to which the MySQL connection is made. It too is optional, and defaults to 3306 if omitted.
- ``<Database>`` is the name of the database holding the KASP Enforcer data.
- ``<Username>`` is the username needed to connect to the database.
- ``<Password>`` is the password associated with the username.

Other Enforcer Parameters:

- ``<ManualKeyGeneration/>`` will disable the automatic key generation in the Enforcer. The user have to generate the keys manually with the ods-enforcer key generate command.
- ``<DelegationSignerSubmitCommand>`` Configure this if you want to have a program/script receiving the new KSK during a key rollover.
- ``<DelegationSignerRetractCommand>`` Configure this if you want to have a program/script receiving the new KSK during a key rollover.
- ``<AutomaticKeyGenerationPeriod>``, Period to automatically pre-generate keys for, when ManualKeyGeneration is not used, default P1Y.
- ``<SocketFile>``, Socket to use for communicating between enforcer and enforcerd.
- ``<WorkingDirectory>``, Location to store intermediate enforcer information. Default: ``/var/opendnssec/tmp``

### DelegationSignerSubmitCommand and DelegationSignerRetractCommand

This will make it possible to create a fully automatic KSK rollover, where OpenDNSSEC feed your program/script on stdin with the current set of DNSKEYs that we want to have in the parent as DS RRs.

__Key Identifier__: If the command defined here ends with ` --cka_id` then that will be stripped off the command and `; <CKA_ID>` will be added to the output.

An example script for the `<DelegationSignerSubmitCommand>` command is available: which is a simple mail script (from 1.4 the eppclient is no longer supported). Remember that the ods-enforcer key ds-seen must be given in order to complete the rollover. This should only be done when the new DS RRs are available on the parents public nameservers.

<Signer>
        <Privileges>
            <User>opendnssec</User>
            <Group>opendnssec</Group>
        </Privileges>
        <PidFile>/var/run/opendnssec/signerd.pid</PidFile>
        <SocketFile>/var/run/opendnssec/engine.sock</SocketFile>
        <WorkingDirectory>/var/opendnssec/tmp</WorkingDirectory>
        <WorkerThreads>4</WorkerThreads>
        <SignerThreads>4</SignerThreads>
        <Listener>
            <Interface><Port>53</Port></Interface>
        <Listener>
        <NotifyCommand>/usr/local/bin/my_nameserver_reload_command</NotifyCommand>
     </Signer>

- ``<Privileges>`` the Signer Engine can drop its privileges if specified.
- ``<PidFile>`` the Signer Engine pidfile location.
- ``<SocketFile>`` the command handler socket file location.
- ``<WorkingDirectory>`` defines the location in which the Signer Engine will create temporary files.
- ``<WorkerThreads>`` specify the number of workers. One worker can handle one zone a time. When it is finished with the zone it takes the next one in queue.
- ``<SignerThreads>`` specify the number of threads that are dedicated to signing RRsets. Usually set to the number of parallel operations your HSM can handle. If the element is omitted from the configuration, the number of signer threads is equal to the number of workers.
- ``<Listener>`` is an element that is needed when you use DNS adapters. Within this element, you can specify a number of interfaces the Signer Engine has to listen to for DNS traffic, such as queries, NOTIFY messages and zone transfer requests. 
  - ``<Interface>`` has two optional elements: ``<Address>`` and ``<Port>``. 
    - Note that if no IP address is given, the Signer Engine defaults to both local IPv4 and IPv6 address. 
    - The default port is 53.
  - There can be multiple ``<Interface>`` sections to listen on multiple interfaces. Outgoing UDP traffic will be bound to the first interface listed here, i.e. the IP source address of the packets will be the IP address of the first interface listed. Listening on the ANY interface (`::`, `0.0.0.0`) leaves interface selection to the operating system. When having multiple interfaces with overlapping routes using the ANY interface should be carefully considered as the source address of the outgoing packet might not be predictable.
- ``<NotifyCommand>`` optional element that will tell the Signer Engine to call this command when the zone has been signed. Will expand the following variables: %zone (the name of the zone that was signed) and %zonefile (the filename of the signed zone).
