## Migration from OpenDNSSEC prior to 2.0

### Migrating between 1.3 and 1.4

Version 1.4  has some kasp database changes compared to 1.3 to allow for an update to the zonelist.xml schema (these changes support flexibility in the input and output adapters).

> :memo: Also see the MIGRATION file in the source code.

This means that in order to use version 1.4 of OpenDNSSEC with a database created with an earlier minor version of OpenDNSSEC (1.3 or earlier) there are two options:

1. If you do NOT need to retain the key information for the system:  
   Wipe and recreate your kasp database (by running ods-ksmutil setup) which __will lose all of the current state for the system including all existing key information.__.
2. If you DO need to retain the key information for the system:
   - Assuming that you are currently using v1.3; run the sql statements against your existing database (depending on which databse is used) given in:
     - enforcer/utils/migrate_adapters_1.mysql __or__
     - enforcer/utils/migrate_adapters_1.sqlite3
   - If you are using v1.1 and wish to maintain your existing keys then you will __first__ need to run one of:
     - enforcer/utils/migrate_keyshare_mysql.pl __or__
     - enforcer/utils/migrate_keyshare_sqlite3.pl
   depending on your database, before running the above.

> :warning: Although these scripts have been tested it is recommended to make a backup of your database prior to running them.

### Migrating between patch versions (1.4.n to 1.4.m)

Patch versions normally do not require changes to the database schema and nothing needs to be done to the database in order to upgrade. However, in exceptional cases this may be required, but this will be clearly stated in the NEWS file and in the release announcement. 

### Migrate pre-1.4 zone fetchers

OpenDNSSEC version 1.4 introduced DNS adapters. With the introduction of DNS adapters, OpenDNSSEC is able to read unsigned zones directly from AXFR or IXFR, and to output signed AXFR and IXFRs. Version 1.3 also understood incoming, unsigned AXFRs, thanks to the zone fetcher process. The zone fetcher had its own configuration file, zonefetch.xml. With the introduction of the DNS adapters, a zone fetcher is not needed anymore and is removed in version 1.4. Version 1.4 also provides IXFR and outgoing zone transfers, and requires more configuration options. A new configuration file, addns.xml, is required to configure the DNS adapters. 

If you upgrade OpenDNSSEC from 1.3 or lower to 1.4 or higher, and you used the zone fetcher, you will have to migrate the configuration. This page explains how you do that.

#### Changes in conf.xml

In 1.3 and earlier, the zone fetcher configuration filename had to be specified in conf.xml in the <ZoneFetchFile> element. In version 1.4, this element must be removed.

In 1.3 and earlier, the zonefetch.xml file contains an element <NotifyListen>: this defines which interface and port the system must bind to listen NOTIFY messages on. For example, to let the zone fetcher listen on the IPv4 address 192.0.2.100 on port 53, you would have configured the zone fetcher like this:

> ...
>     <NotifyListen>
>         <IPv4>192.0.2.100</IPv4><Port>53</Port>
>     </NotifyListen>

With OpenDNSSEC 1.4, the signer can listen to more than NOTIFY messages. It also accepts zone transfer requests and 'regular queries' like SOA, DNSKEY, and so on. The listener becomes more general and therefore the configuration now goes in conf.xml, under the <Signer> element:

> ...
>     <Listener>
>         <Interface><Address>192.0.2.100</Address><Port>53</Port></Interface>
>     </Listener>

Instead of <NotifyListen>, the element name is made more general and is now called <Listener>. There is an additional element <Interface> to indicate this describes an interface and to support multiple listening interfaces. The configuration does not need different elements for IPv4 and IPv6 anymore, so also for an IPv6 address you would use the <Address> element. The <Port> element remains unchanged.

#### From zonefetch.xml to addns.xml

In 1.3 and earlier, all the zone fetcher configuration (except the <NotifyListen>) is encapsulated within the elements <Zonefetch><Default>. In 1.4, the DNS adapter configuration is encapsulated in <Adapter><DNS>. The <TSIG> part is unchanged and can be copied straightforward.

Because the zone fetcher only was able to do inbound zone transfers, the <RequestTransfer> element was immediately listed under the <Default> element. The DNS Adapter configuration is for both inbound and outbound zone transfers, so it should now be encapsulated within the <Inbound> element. Again, suppose you had the following configuration in zonefetch.xml:

> <Zonefetch>
>     ...
>     <Default>
>         <RequestTransfer>
>             <IPv4>192.0.2.100</IPv4>
>             <Port>53</Port>
>             <Key>secret.example.com</Key>
>         </RequestTransfer>
>         ...
>     </Default>
> </Zonefetch>

You would have to translate that into the following configuration in addns.xml:

> <Adapter>
>     <DNS>
>         <TSIG>
>             <Name>secret.example.com</Name>
>             <Algorithm>hmac-sha256</Algorithm>
>             <Secret>sw0nMPCswVbes1tmQTm1pcMmpNRK+oGMYN+qKNR/BwQ=</Secret>
>         </TSIG>
>         <Inbound>
>             <RequestTransfer>
>                 <Remote>
>                     <Address>192.0.2.100</Address>
>                     <Port>53</Port>
>                     <Key>secret.example.com</Key>
>                 </Remote>
>             </RequestTransfer>
>         ...
>         </Inbound>
>         ...
>     </DNS>
> </Adapter>

The <IPv4> element is renamed to <Address> again, which is IP version independent. <Port> and <Key> remains unchanged. The three elements are encapsulated into the element <Remote>, so that multiple remote zone transfer sources can be configured.

That's it! In the DNS adapter configuration you can now also configure from which sources you allow NOTIFY messages and you can configure outbound zone transfers. For more information on that, see the documentation on addns.xml.
