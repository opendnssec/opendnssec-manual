### Introduction

### Background

A domain name, like www.google.com, is actually a representation of an IP address, the address of a computer. This translation is done with the help of the Domain Name System (DNS). This is useful for an ordinary user because the domain name is more mnemonic than the IP address. The main objective for the DNS system is to translate the domain name into an IP address, but it can also do load balancing, host aliasing, mail server aliasing, and more.

This translation from a domain name to an IP address is not as straight forward as one could think. The DNS system is a service that has been around for many years and was conceived in a time when security was not a design issue for internet protocols. By time, vulnerabilities are inevitably found in the system and its protocols. There are several know classes of threats to the DNS system: Packet Interception, ID Guessing and Query Prediction, Name Chaining, Betrayal By Trusted Server, and Denial of Service (DoS). The impact of some of these threats is that an attacker could for instance redirect the traffic for a web page to a malicious server without the user knowing anything and thereby having a complete control of the information flow to the user.

The vulnerabilities in the DNS system was addressed at an IETF meeting in November 1993 where the first organized work started on the Domain Name System Security Extentions (DNSSEC). The resolver/user can, with the help of digital signatures, detect any modifications by third parties on the information from the DNS system, thus defeating most of the known vulnerabilities. DNSSEC is although vulnerable to the DoS attacks, like all other network services. For more information on how DNSSEC is working, read <http://www.dnssec.net/>.

### Problem discussion

A major obstacle to the widespread adoption of DNSSEC is the complexity of implementing it. There is no package that one can install on a system, click the "start" button, and have DNSSEC running. Instead, there are a variety of tools, none of which on their own is a complete solution. To actually run a DNSSEC-enabled authoritative server requires writing custom scripts to link them together. Even then, aspects of DNSSEC management such as key management and use of hardware assistance (such as HSMs) have not been adequately addressed.

### Purpose

The purpose of the OpenDNSSEC project is to make the DNSSEC handling as automated as possible with as high performance as possible, thus reducing the effort for the system administrator. This project will produce software that will provide the comprehensive package mentioned in the problem discussion.

### Goals

Installed on a system it will, with a minimum amount of operator input:

- Handle the signing of DNS records, including the regular re-signing needed during normal operations.
- Handle the generation and management of keys, including key rollover.
- Seamlessly integrate with external cryptographic hardware such as HSM:s (including USB tokens and smart cards).
- Seamlessly integrate into an existing deployment scenario, without the necessity to overhaul the entire existing infrastructure.

### Target group and scope

The OpenDNSSEC software will be applicable to a wide variety of DNS configurations, including (but not limited to):

- Organisations (such as TLDs) that manage few zones, each with a large number of records.
- Organisations (such as ISPs) that manage a large number of zones, each with few records.
- Organisations (such as companies managing their own zones) that have a single zone with relatively few records.


