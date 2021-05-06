.. _doc_ods_overview:

Overview
========

OpenDNSSEC takes in unsigned zones, adds the signatures and other records for
DNSSEC and passes the zones on to the authoritative name servers for that zones.

It does this according to a Key and Signing Policy (KASP) that describes how an
organisation wants their DNSSEC configured. 

.. figure:: img/ods-architecture_1.4.png
    :align: center
    :width: 100%
    :alt: Password screen

    The architecture of OpenDNSSEC