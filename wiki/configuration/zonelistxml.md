## zonelist.xml

Since OpenDNSSEC 2.0 zones should be added and deleted using the commandline interface of the enforcer. However, to provide backwards compatibility and convenience it is still able to import (and export) zones in bulk via the zonelist.xml. Its use is discouraged. The zonelist specifies which policy a zone should use and how it is updated.

### Preable and XML root element ZoneList

    <?xml version=`1.0` encoding=`UTF-8`?>
    <ZoneList>
        ...
    </ZoneList>

Each XML file starts with a standard element `<?xml...`. As with any XML file, comments are included between the delimiters `<!–-` and `-–>`. The enclosing element of the XML file is the element `<ZoneList>` which, with the closing element `</ZoneList>`, brackets the list of zones.

### Zone specification

Each zone is defined by a `<Zone>` element:

    <Zone name=`example.com`>
        ...
    </Zone>

The mandatory attribute `name` identifies the zone. Each zone within the zone list must have a unique name. Use `.` when signing the root.

### Policy

    <Policy>default</Policy>

The first element of the `<Zone>` tag is `<Policy>`, which identifies the policy used to sign the file. Policies are defined in the kasp.xml file, and the name in this element must be that of one of the defined policies.

### Configuration

Information from the Enforcer to the Signer Engine is passed via a special signer configuration file, the name of which is given by the `<SignerConfiguration>` section of the zone definition:

    <SignerConfiguration>/var/opendnssec/signconf/example.com.xml</SignerConfiguration>

(Note that this file is a temporary file that passed between OpenDNSSEC components and is not intended to be edited by users.)

### Adapters

The next part of the zone element specifies from where OpenDNSSEC gets the zone data and to where the signed data is put.

    <Adapters>
        <Input>
           <Adapter type=`File`>/var/opendnssec/unsigned/example.com</Adapter>
        </Input>
        <Output>
            <Adapter type=`DNS`>/etc/opendnssec/addns.xml</Adapter>
        </Output>
    </Adapters>

The `<Adapters>` element comprises an `<Input>` and `<Output>` element which (fairly obviously) identify the input source and output sink of the data.

Within each element is a tag defining the type of data source/sink and its parameters. There is `type=`File``, which takes as its only data the name of the input unsigned file, or output signed zone file. And there is `type=`DNS``, which takes a configuration file as its data. The DNS adapter configuration file is described in more detail in the description of the [addnsxml documentation](addnsxml).
The `</Zone>` tag closes the definition of the zone. As indicated above, one or more zones can be defined in this file. The `</ZoneList>` element closes the file.
