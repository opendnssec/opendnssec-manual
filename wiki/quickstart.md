
## Quick start guide

In this quick start guide we will show how you can a simple OpenDNSSEC installation running from source code, using SoftHSMv2 as your crypto library and key store.  We assume you have root access through sudo, but will be running OpenDNSSEC as the current logged in user and have ``/usr/sbin`` in your PATH. Sometimes we may refer to Ubuntu packages, but other distributions have the same packages available.  We will pass through the following steps:

1. Get the necessary dependencies in place
2. Obtain SoftHSMv2 source code, compile and install.
3. Obtain OpenDNSSEC source code, compile and install
4. Prepare the system for first usage
5. Add a zone, get it signed
6. Know where messages go and basic commands
7. Steps where to go next.

### Getting the dependencies

OpenDNSSEC requires only a few dependencies to run, the ``ldns`` library, ``libxml2`` library and either SQLite3 (preferred) or MySQL, and any PKCS#11 compatible library.  To compile OpenDNSSEC you need of course more tooling, but everything is fairly standard when you have the generic gcc compiler suite installed, and add the development packages of the earlier mentioned libraries.  Since we'll be using SoftHSMv2, we also need to get those dependencies in place, which will add ``libbotan-2`` to the list.  On Ubuntu noble everything can be pulled in using:

    sudo apt-get install build-essential libsqlite3-dev libxml2-dev libldns-dev libbotan-2-dev

### Install SoftHSM

In one go we can get, unpack, configure, compile and install SoftHSM:

    wget https://dist.opendnssec.org/source/softhsm-2.6.1.tar.gz
    tar xzf softhsm-2.6.1.tar.gz
    cd softhsm-2.6.1
    ./configure
    make
    sudo make install

Whether the configure command explicitly needs a path to the ``botan-2`` library, and where this is located, will depend on your Linux or Unix distribution.  After this SoftHSMv2 is installed, but not ready to be used, we will come back to that later.

### Install OpenDNSSEC

 Again we can fetch the OpenDNSSEC source code, and perform all necessary steps to get OpenDNSSEC installed in one go:

    wget https://dist.opendnssec.org/source/opendnssec-2.1.14.tar.gz
    tar xzf opendnssec-2.1.14.tar.gz
    cd opendnssec-2.1.14
    ./configure
    make
    sudo make install

OpenDNSSEC is now installed, SoftHSMv2 and OpenDNSSEC still need to be prepared for first use.

### Prepare for first use.

Since we have installed SoftHSMv2 and OpenDNSSEC with root privileges, but will be running OpenDNSSEC as the current user, we need to perform some steps because OpenDNSSEC will need write permissions to certain locations, it will also be convenient to directly edit the configuration.  We will use a blunt method here to add write permissions, if setting up a very secure environment you can be far more selective.

SoftHSM version 1 will store the key material which will be used to sign the zone in the location ``/var/lib/softhsm``.  OpenDNSSEC will use diverse places in ``/var/opendnssec`` to keep state, as well as files in ``/var/run/opendnssec``.  It is convenient to be able to update the configuration files of OpenDNSSEC as well in this simple explanation, which are contained in ``/etc/opendnssec``.  We will change the ownership so we will not run in permissions later.

    sudo chown $USER /var/lib/softhsm
    sudo chown $USER /etc/opendnssec /etc/opendnssec/*.xml
    sudo chown -R $USER /var/opendnssec
    sudo chown $USER /var/run/opendnssec

We now no longer need root access, but still have not configured or prepared the system for actual first usage.  To do this, we need to:

1. Initialize our key store, the SoftHSM storage;
2. Initialize the database to be used by OpenDNSSEC;
3. Load initial configuration into OpenDNSSEC.

We will start to initialize the HSM storage:

    softhsm2-util --init-token --label OpenDNSSEC --pin 1234 --so-pin 1234 --free

This will instruct SoftHSM to initialize the token database, which is in the first free (virtual) physical location slot, give it the name OpenDNSSEC and protect it with a PIN number.  The PIN number and label are referred to by the OpenDNSSEC configuration and will match the shipped configuration in ``/etc/opendnssec/conf.xml`.  We can now proceed to initialize the OpenDNSSEC database.

    ods-enforcer-db-setup

Respond with Y to initialize the database.  OpenDNSSEC can now almost be used, however in order to sign zones we need to give OpenDNSSEC information on the policy how to sign zones.  Sample policies are located in ``/etc/opendnssec/kasp.xml`` and can be loaded into OpenDNSSEC. However OpenDNSSEC requires that the daemon is running before we can load this data.  Lets start the daemons:

    ods-control start

And then load the policies:

    ods-enforcer policy import

### Add a simple zone and sign it.

We will now add a simple zone and sign it.  Its usage will not be very realistic, because we want a quick result for now.  We will add a zone for the domain "example.com", so we first need to get the zone data in place.  With our current set-up, OpenDNSSEC will look at the location ``/var/opendnssec/unsigned`` for the input zone files, which should get the same name as the domain, so now create the file ``/var/opendnssec/unsigned/example.com`` with the following contents:

    $ORIGIN example.com.
    $TTL 86400
     
    example.com.    3600    IN      SOA     ns.example.com. username.example.com. 1 86400 7200 2419200 300
    example.com.            IN      NS      ns
    ns                      IN      A       192.0.2.1

Now instruct OpenDNSSEC to start signing this zone using the "lab" policy.  This policy is not very realistic.  The default policy that has been loaded from the ``kasp.xml`` is suitable for many usages but will take longer to take effect.  The default policy is used by either replacing "lab" by "default" or by ommiting the "-p lab" altogether:

    ods-enforcer zone add -z example.com -p lab

After a few minutes the zone should be fully signed and available in ``/var/opendnssec/signed/example.com`` for you.  Of course the actual signing of such a small zone does not take very long, but in order to introduce the ZSK, KSK and so forth and allow all DNS systems in the world to have noticed you new zone to get signed, the OpenDNSSEC policy introduces a few delays.  These delays are set very low for the lab policy, but more realistic for the default policy.

### Know where messages go

We have now done every thing to get a zone signed.  There is a bit of extra information that is crucial before you can explore further.  First OpenDNSSEC is designed as a daemon process that runs in the background.  This means that any problems cannot be reported to the end-user directly.  Instead problems are logged to syslog.  In many systems this will end up in the file ``/var/log/syslog``, but other locations are very much possible.  Since many actions, such as adding a zone involve background processes, even those actions require you to review syslog in case the action yields unexpected results.

### Where to go next

- Start OpenDNSSEC using ``ods-control start``, and stop it using ``ods-control stop``.  OpenDNSSEC actually consist of two daemon processes, the signer and enforcer which can also be controlled separately.
- It is not enough to reset your system using ``ods-enforcer-db-setup`` as state is also kept in ``/var/opendnssec``.
- Look at the configuration file ``/etc/opendnssec/conf.xml`` and its documentation.  You need to change this if you want to use a different HSM or tie OpenDNSSEC into other software systems.
- Look at the configuration of policies in ``/etc/opendnssec/kasp.xml``, it is highly configurable to indicate how often and fast you want to roll keys.  If you want to change it, you will need to reload the policies using ``ods-enforcer policy import`` again. Use ``ods-kaspcheck`` to validate your policy file!

- Have a look at the ``ods-enforcer`` manual page.  This is the primary control tool to add, delete zones, review key states, et cetera.

