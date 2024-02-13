## addns.xml

Per zone you can configure the zone transfer settings. One file can be used for multiple zones, just point to the same file in the zone list file /etc/opendnssec/zonelist.xml.

This section explains the parameters of the DNS adapter configuration by referring to the example addns.xml file supplied with the OpenDNSSEC distribution. 

Elements of the addns.xml file:

### Preamble

     <Adapter>
         <DNS>
         ...
         </DNS>
     </Adapter>

Each XML file starts with a standard element "<?xml...". As with any XML file, comments are included between the delimiters "<!--" and "–>". The enclosing element of the XML file is the element <Adapter> which, with the closing element </Adapter>, brackets the zone transfer configuration. Because we are configuring the DNS Adapter, the element <DNS> is used.

### TSIG

    <TSIG>
        <Name>secret.example.com</Name>
        <!-- http://www.iana.org/assignments/tsig-algorithm-names -->
        <Algorithm>hmac-sha256</Algorithm>
        <!-- base64 encoded secret -->
        <Secret>sw0nMPCswVbes1tmQTm1pcMmpNRK+oGMYN+qKNR/BwQ=</Secret>
    </TSIG>

The first element of the <DNS> tag is <TSIG>, which is the dedicated protection mechanism used. TSIG requires three elements:

- ``<Name>`` is the name of the TSIG.
- ``<Algorithm>`` specifies the algorithm used.
- ``<Secret>`` is the base64 encoded secret.

You can list zero or more TSIGs.

### Inbound

The inbound DNS configuration is bracketed in the enclosing element <Inbound>, and the closing element </Inbound>.  Within the inbound configuration one specifies how to request transfers and optionally how to receive notifies.

    <Inbound>
        <RequestTransfer>
            <!-- EXAMPLE: send request to 198.51.100.3 on the default port 53 -->
            <Remote>
                <Address>198.51.100.3</Address>
            </Remote>
            <!-- EXAMPLE: send request to 2001:db8::3 on port 5353, TSIG signed with secret.example.com -->
            <Remote>
                <Address>2001:db8::3</Address>
                <Port>5353</Port>
                <Key>secret.example.com</Key>
            </Remote>
        </RequestTransfer>
        <AllowNotify>
            <!-- EXAMPLE: allow notifies from 198.51.100.3 -->
            <Peer>
                <Prefix>198.51.100.3</Prefix>
            </Peer>
        </AllowNotify>
    </Inbound>

``<RequestTransfer>`` is used to hold a list of master name servers where this zone can request zone transfers. The name servers are given by the <Remote> element:

- ``<Address>`` is a required element that stores the IPv4 or IPv6 address of the master name server.
- ``<Port>`` is an optional element that specifies the port to use. If not provided, the port is defaulted to 53.
- ``<Key>`` refers to a configured TSIG key name. The TSIG must be provided in the same file. If no key is given, TSIG will not be used for this name server.

You can list multiple master name servers. In the example above, the zone transfer can be requested with no TSIG from the IPv4 address, or at the IPv6 address with the secret.example.com TSIG.

``<AllowNotify>`` lists the name servers that may notify OpenDNSSEC that there is a new version of the zone. Usually, the master name server will also provide the NOTIFY messages. In that case, the address and TSIG key from <RequestTransfer> can be copied here. Separating this in the configuration file allows for more flexible environment setups. Here, you can list a number of <Peer> elements:

- ``<Prefix>`` is a required element that stores an address or address prefix.
- ``<Key>`` refers to a configured TSIG key name. The TSIG must be provided in the same file. If no key is given, TSIG will not be used for this server.

> :memo:
> If no NOTIFY messages are being received, OpenDNSSEC will request a new zone transfer after the SOA REFRESH value has passed in time. If a zone transfer has failed, OpenDNSSEC will retry after the SOA RETRY value has passed in time.

### Outbound

Similar to <Inbound>, the outbound DNS configuration goes between <Outbound> and </Outbound>.

    <Outbound>
        <!-- Provide XFR to host -->
        <ProvideTransfer>
            <!-- EXAMPLE: provide XFR to 192.0.2.2 with key secret.example.com -->
            <Peer>
                <Prefix>192.0.2.2</Prefix>
                <Key>secret.example.com</Key>
            </Peer>
        </ProvideTransfer>
        <!-- Send NOTIFY messages to host -->
        <Notify>
            <!-- EXAMPLE: send NOTIFY to 192.0.2.2 on the default port 53 -->
            <Remote>
                <Address>192.0.2.2</Address>
            </Remote>
        </Notify>
    </Outbound>

<ProvideTransfer> allows you to configure a list of secondary name servers that can pick up the signed zone through a zone transfer. One or more <Peer> elements is used to do that. In this example, one secondary name server is configured: The server may request a zone transfer if it is correctly signed with the secret.example.com TSIG key.

> :warn:
> If OpenDNSSEC acts as a secondary for certain zones (e.g., it has Inbound DNS adapters configured), zones may expire if inbound zone transfers are failing. This happens if the SOA EXPIRE value has passed in time after the latest successful zone transfer. If a zone is expired, OpenDNSSEC will stop providing signed zone transfers, but it will still serve normal queries, for troubleshooting purposes.

For the same reasons as with the inbound DNS configuration, sending notifies and zone transfers has been split up in the configuration to be more flexible. Here, OpenDNSSEC will send a NOTIFY to the server, not using TSIG. Again, more than one servers can be configured.
