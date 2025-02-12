## Project Management

### Project Abstract

A major obstacle to the widespread adoption of DNSSEC is the complexity of implementing it. There is no package that one can install on a system, click the "start" button, and have DNSSEC running. Instead, there are a variety of tools, none of which on their own is a complete solution. To actually run a DNSSEC-enabled authoritative server requires writing custom scripts to link them together. Even then, aspects of DNSSEC management such as key management and use of hardware assistance (such as HSM:s) have not been adequately addressed.

The aim of the OpenDNSSEC project is to produce software that will provide this comprehensive package. Theoretical and operational experience has resulted in the specified requirements and the system design.

Further more, the project plan specifies all the deliverables, the time plan, and how the project is managed.

### Introduction

This chapter will give an introduction to the OpenDNSSEC project. The background and the purpose of this project define why it exists and are the foundation of the project goals. A clear view of what to expect of this project is given by defining the goals and the delimitations.

**Background**

A domain name, like  <http://www.google.com>, is actually a representation of an IP address, the address of a computer. This translation is done with the help of the Domain Name System (DNS). This is useful for an ordinary user because the domain name is more mnemonic than the IP address. The main objective for the DNS system is to translate the domain name into an IP address, but it can also do load balancing, host aliasing, mail server aliasing, and more.

This translation from a domain name to an IP address is not as straight forward as one could think. The DNS system is a service that has been around for many years and was conceived in a time when security was not a design issue for internet protocols. By time, vulnerabilities are inevitably found in the system and its protocols. There are several know classes of threats to the DNS system: Packet Interception, ID Guessing and Query Prediction, Name Chaining, Betrayal By Trusted Server, and Denial of Service (DoS). The impact of some of these threats is that an attacker could for instance redirect the traffic for a web page to a malicious server without the user knowing anything and thereby having a complete control of the information flow to the user.

The vulnerabilities in the DNS system was addressed at an IETF meeting in November 1993 where the first organized work started on the Domain Name System Security Extensions (DNSSEC). The resolver/user can, with the help of digital signatures, detect any modifications by third parties on the information from the DNS system, thus defeating most of the known vulnerabilities. DNSSEC is although vulnerable to the DoS attacks, like all other network services. For more information on how DNSSEC is working, read <http://www.dnssec.net>.

**Problem discussion**

A major obstacle to the widespread adoption of DNSSEC is the complexity of implementing it. There is no package that one can install on a system, click the "start" button, and have DNSSEC running. Instead, there are a variety of tools, none of which on their own is a complete solution. To actually run a DNSSEC-enabled authoritative server requires writing custom scripts to link them together. Even then, aspects of DNSSEC management such as key management and use of hardware assistance (such as HSM:s) have not been adequately addressed.

**Purpose**

The purpose of the OpenDNSSEC project is to make the DNSSEC handling as automated as possible with as high performance as possible, thus reducing the effort for the system administrator. This project will produce software that will provide the comprehensive package mentioned in the problem discussion.

**Goals**

Installed on a system it will, with a minimum amount of operator input:

- Handle the signing of DNS records, including the regular re-signing needed during normal operations.
- Handle the generation and management of keys, including key rollover.
- Seamlessly integrate with external cryptographic hardware such as HSM's (including USB tokens and smart cards).
Seamlessly integrate into an existing deployment scenario, without the necessity to overhaul the entire existing infrastructure.

**Target group / customers**

The OpenDNSSEC software will be applicable to a wide variety of DNS configurations, including (but not limited to):

- Organisations (such as TLDs) that manage few zones, each with a large number of records.
- Organisations (such as ISPs) that manage a large number of zones, each with few records.
- Organisations (such as companies managing their own zones) that have a single zone with relatively few records.

**Scope**

The OpenDNSSEC project is limited into three phases.

**Including**

The first phase will firstly deliver a proof of concept. This will be various pieces of software (coded and scripted) bundled together to perform the desired tasks. It will only contain bare operating minimals. Firstly zone file in/out and secondly AXFR in/out. The code will be improved during a number of development iterations.

The second phase will deliver a version with somewhat richer features.

**Excluding** 

The third phase of OpenDNSSEC, and its future, is not part of this project plan. What the third phase of OpenDNSSEC consist of will be evaluated during the second phase of this project.

**Dependencies**

The work of this project is mainly depending on the most current RFCs from the IETF.

**Purpose of this document**

- To get project on the right track.
- To give a common view of the project, its goals, and future development.
- Give the possibility of evaluating the project.

**Project organisation**

This collection of information is the result of a cooperation between the following parties:

- [.SE](http://www.iis.se/)
- [John A Dickinson](http://jadickinson.co.uk/)
- [Kirei AB](http://www.kirei.se/)
- [NLNet Labs](http://www.nlnetlabs.nl/)
- [Nominet](http://www.nominet.org.uk/)
- [SURF](http://www.surf.nl/)

## Use Cases

The use-case diagram essentially lists the functionality required of the OpenDNSSEC software (this functionality is expanded in the associated requirements document). By and large (a) the use-cases are fairly simple and (b) the actions are largely automatic, requiring little input from the user. For these reasons, rather than detail a step-by-step procedure for each use-case (as is usual in such documents), only a broad description of the function is given.

Two sets of use-cases are given, one for each phase of the project.

**Phase 1 - Zone Files**

The following diagrams show the use-cases covered by this phase:

![assets/ZoneFileUseCasesA](assets/ZoneFileUseCasesA.png)
![assets/ZoneFileUseCasesB](assets/ZoneFileUseCasesB.png)

**Actors**

Actors are external entities involved in the system – they are not part of it. The following entities have been referenced in the use-cases:

__Parent Zone__  
The parent of the zone under consideration. It is included in these use cases because the action of editing the file may require changes to the NS and glue records in the parent.

__Signed Zone File__  
The output of the process, and what is ultimately loaded into the nameserver.

__Timer__  
Many actions are automated and are triggered by the passage of time. The Timer represents the system clock initiating an action.

__Trust Anchor Location__  
Introduction of a new KSK into a zone requires that a corresponding trust anchor be published somewhere. This actor represents that location, whatever it is (e.g. parent zone, trust anchor repository etc.).

__(Un)signed Zone File__  
The input zone file. If signed, DNSSEC information is stripped off before processing.

__Zone Manager__  
The person (or persons) responsible for managing the data in the zone.

__Security Manager__  
The person (or persons) responsible for the security of the zone.

(Note that the roles of Zone Manager and Security Manager may well be filled by the same person.)

**Use Cases**

__Scheduled KSK Rollover__  
Initiated by the timer, this use-case is concerned with replacing the existing key-signing key with a new one. As described in RFC 4631, the process involves the introduction of a new key, parallel running, and the withdrawal of the old key.

The system keeps track of when keys were introduced into the zone and how long they have been there. The determination of when to initiate a key rollover is based on this data and the parameters defining the zone policies.

The output is an updated signed zone file. Also output is a trust anchor for the new KSK.

__Emergency KSK Rollover__  
Initiated by the security manager if a key compromise is suspected, the process forces a roll-over of the current KSK. In practice, it can be achieved by marking the current KSK as retired then following the process for scheduled KSK rollovers.

__Scheduled ZSK Rollover__  
Like the scheduled KSK rollover, this use-case automatically rolls a zone-signing key based on time and zone policies. Like the KSK rollover, introduction of a new ZSK will lead to an updated signed zone file. Unlike the KSK case, there is no updated trust anchor.

__Emergency ZSK Rollover__  
Initiated by the security manager if a key compromise is suspected, the process forces a roll-over of the current ZSK. In practice, it can be achieved by marking the current ZSK as retired then following the process for scheduled ZSK rollovers.

__Edit Key Policies__  
Key policies – which determine how DNSSEC in the zone operates, e.g the lifetimes of keys and signatures, how often the zone is re-signed, etc – are expressed as a set of parameters. The OpenDNSSEC solution must have a way whereby these parameters can be modified.

__Generate Keys__  
Keys will be stored in a key store. Although keys could be generated as needed, some sites may have a requirement for redundancy, i.e. duplicate key stores in separated locations. As such, it may be simpler to pre-generate all keys for the lifetime of the key store.

__Delete Keys from Key Store__  
Keys that have been used and have expired may be deleted from the key store. The system should allow the security manager to do this.

__Change Key Store__  
In the event that a key store is implemented in hardware (e.g. a hardware security module, HSM), there must be some way of coping with the end of life event of the hardware. Either there must be a way of moving existing keys to the new key store, or there must be a way of transitioning between the two.

__Scheduled Re-Signing__  
DNSSEC requires that signatures have a fixed lifetime, which in turn means that the zone needs periodic re-signing.

__Emergency Re-Signing__  
There may be circumstances where the zone manage may want to trigger an emergency re-signing (e.g. if the scheduled re-signing job has failed for some reason). The system must allow them to do this. The use-case is marked as an extension of the scheduled re-signing, since both perform the same actions, only the method of invocation is different.

__Edit Zone File  
Although OpenDNSSEC is concerned with DNSSEC, the zone file will have non-DNSSEC related changes applied to it. The solution must allow this and allow the updated zone file to be signed.

## Project Requirements

1. Introduction

1.1 Scope of the System

As envisaged, the signing system sits between a private master nameserver and a public master. The system receives data from the public master, signs it, and sends the signed data to the public master.

The scope of this document covers the first phase of development, a system that will be able to sign whole zones. It does not cover the intended subsequent phase that will allow for incremental zone updates.

2. Requirements for 1.0.0 

2.1 Basic Requirements 

1. The system MUST be able to deal with configurations containing zero or more zones.
2. The system MUST be able to handle zone sizes ranging from a few RRs to millions of RRs.
3. The system MUST accept zone data in the form of a zone file in standard presentation format
4. The system MUST output signed zone data as a zone file in standard presentation format. 
   - The system SHOULD be able to send a notify command to the local nameserver upon changed output signed zones.
5. The system MUST be able to accept input zone data via an AXFR: 
   - The system MUST be able to request an AXFR from an authoritative server.
   - The system MUST be able to process NOTIFY messages received from an authoritative server.
6. In the case of AXFR transfers, the system MUST support TSIG authentication with the upstream authoritative server.
7. The system MUST NOT depend on the BIND tools for signing.

2.2 Normal Operations 

2.2.1 Startup/Shutdown?

1. A start-up script SHOULD be supplied that allows the software to be started when the system starts up.
2. The system MUST be able to drop privileges upon start up.
3. It MUST be possible to shutdown all long-running software cleanly.
4. If the software is not cleanly shutdown, it MUST be possible to restart it with no loss of data.
   __ Note: if this provides difficult/impossible to implement automatically, it is allowable for written instructions to be supplied describing how to manually recover from a failure.__

2.2.2 Configuration

1. It MUST be possible to specify a list of zones to be signed.
2. It MUST be possible to specify a policy for each zone that specifies how the zone should be signed.
3. Sensible default values MUST be provided for all configuration parameters.
   __ Note: This is likely to be in the form of a default policy.__

2.2.3 Operation

1. syslog() MUST be used as the logging mechanism.
2. The choice of which operations to log SHOULD be configurable.
3. The syslog() facility under which messages are logged SHOULD be configurable
4. Components of the system should use the following log levels:

   - Info - for informational messages. (System has worked, this is possibly useful information.)
   - Warning - for warning messages. (System has worked, but this condition was sufficiently unusual to remark upon.)
   - Error - for error messages. (System has encountered a problem.)
   - Alert - for messages where the operator needs to be notified immediately.
     __ Note: if something goes wrong, the system might log multiple error messages as a result. However, only one alert message is required.__

5. All errors MUST be logged.

2.3 Signing Process 

2.3.1 Signing Policy 

1. The system MUST sign each zone according to a pre-defined policy. (This determines parameters such as signing interval, key length, signature lifetime etc.)
2. It MUST be possible for each zone to have its own policy.
3. The policy for the zone MUST allow values for the following signing parameters to be set: 
   1. Inception offset. (This is the time before the present at which the signatures first become valid. It allows for clock skew, a difference in times between two systems on a network.)
   2. The signature re-sign interval. The period of which the Signer runs.
   3. The signature refresh interval. The Signer will reuse a signature until it has this amount of time remaining in its validity period. After this will a new signature be generated.
   4. The default signature validity period of RRSIGs. (Note: this is the time from signing for which the records are valid.)
   5. Jitter. (Note: This is a value, uniformly distributed between zero and some maximum, added to the signature validity period to prevent all records signature expiring at once.)
   6. The TTL for DNSKEY records. (Note: this is the TTL for the DNSKEY records generated by the system. It also overrides the TTL associated with any DNSKEY records in the input file (as all DNSKEY records in an RRset should have the TTL).)
   7. Signature scheme: 
      1. RSA/SHA1 MUST be supported for signatures.
      2. RSA/SHA-256 MUST be supported for signatures.
      3. RSA/SHA-512 MUST be supported for signatures.
4. The policy MUST allow a choice of authenticated denial scheme: 
   - NSEC MUST be supported.
   - NSEC3 without opt-out MUST be supported.
   - NSEC3 with opt-out MUST be supported.
5. The policy for the zone SHOULD allow values for the following to be set: 
   1. The signature lifetime of the RRSIG for NSEC/NSEC3 records.  
       __Note: If not specified, the signature lifetime of NSEC/NSEC3 records should be that of other RRSIG records in the zone.__
   2. The parameters for the NSEC3PARAM record: hash algorithm, flags, iterations and salt.
   3. The parameters for the SOA record: serial, minimum, ttl.

2.3.2 Signing Process 

1. The signer MUST discard all DNSSEC RRs (except DNSKEY RRs) from the input data.  
   Note: DNSKEY RRs may be in the input zone file if the zone is in the process of moving between DNS operators.

2.4 Key Management 

2.4.1 Key Generation

1. The system MUST generate keys as required according to the policy for the zone.
2. The system MUST allow additional keys to be manually generated by the operator.
3. The policy for the zone MUST allow the following key parameters to be set: 
   1. The key algorithm: 
      - RSA MUST be supported as a key algorithm.
   2. RSA key lengths between 1024 and 4096 bits MUST be supported.
4. The MUST have an option that will system not allow keys to be used until they have been backed up.  
   __Note: this means that there must be some way of notifying to the system that the backup has been done.__

2.4.2 Key Storage

a. All access to key material MUST be via the  PKCS#11 API  
   __Note: This requirement is to allow use of any compliant HSM.__
b. The system MUST be capable of working with multiple PKCS#11 providers simultaneously.  
   __Note: this is to allow migration to new HSMs as the old ones reach the end of their life.__
c. If multiple PKCS#11 providers are in use: 
   a. It MUST be possible to select which provider to use for new KSKs.
   b. It MUST be possible to select which provider to use for new ZSKs.
   c. The system MUST be able to access all existing keys, regardless of which connected provider they are stored in.

2.4.3. Key Rollovers 

a. The system MUST be able to roll ZSKs automatically with no operator intervention.
b. The rolling of the keys MUST be done according to the algorithm in  draft-morris-dnsop-dnssec-key-timing (or its successor documents). In particular, it MUST be possible to roll a ZSK without requiring multiple signatures per RRset.
c. It MUST be possible to manually roll the ZSK for a zone.
d. It MUST be possible to manually roll the KSK for a zone.
e. It MUST be able to supply a DS record or a DNSKEY RR for every KSK.
f. Where the KSK rolling process removes a key from the zone, the system SHOULD cause the generation of a notification that the corresponding DS record be removed from the parent zone.
g. The system MUST be able to supply the operator with an advance warning of the the production of a new KSK for a zone if such notifications are enabled.
   __Note: this requirement aids installations where the generation of a KSK is a manual operation.__
h. The production of a new KSK for a zone SHOULD be notified to the operator if such notifications are enabled.
   __Note: this requirement aids installations where the passing of trust anchor information to an external organisation is a manual operation.__

2.5 Integrity Requirements 

Since the loading of incorrect DNSSEC data into a zone could have serious consequences, e.g. a zone being perceived as bogus, the system must take steps to verify that the data it is outputting is correct.

a. The system SHOULD have an independent check that the output zone data is correct.
   __Note: This is handled by the KASP Auditor, which has its own set of requirements.__

2.6 Performance Requirements 

For large zones, or for large numbers of small zones, it is important that the signing is completed in a reasonable time. The figures given here are an estimate of acceptable performance, and need to be refined.

a. With appropriate hardware, it SHOULD be possible to sign a one-million name zone in one hour.
   __Note: Verification is not included in this time period__

2.7 Standards Conformance

a. The signatures created by the system and operation of the system MUST conform to the following standards: 
   - RFC 4033
   - RFC 4034
   - RFC 4035
   - RFC 5155
   - RFC 5702

2. Requirements for 1.1.0 

2.1 Performance 

a. The Signer SHOULD be able to sign RRs with multiple threads.
b. With appropriate hardware, it MUST be possible to sign a zone in 30 minutes. 5M name zone, 10M NS, 25000 glue. Using NSEC3 with opt-out. With 8GB RAM.
c. The system MUST handle 50.000 zones. (Please add more information)
d. A newly added zone MUST be signed as soon as possible.
   __Note: A newly added zone must be prioritized.__

2.2 Hooks and commands 

a. It MUST be possible to select an auditor program that will be used by the Signer.
b. It MUST be possible to configure a command that will accept the zone name and the current set of keys that should be at the parent. Used for sending DS and/or DNSKEY RR to the parent.
c. There MUST be a way of notifying the system that the current set of KSKs has been sent to the parent.
d. There MUST be a way of notifying the system that a particular DS RR has been seen at the parent.

2.3 ods-auditor 

a. It MUST possible to configure the auditing level.

2.4 PKCS#11 

a. The system MUST only save the private key object in order to save space in the provider (and improves performance).
b. The system SHOULD have a per provider flag, indicating whether the security module needs to be activated before starting the system.

3. Requirements for 2.0.0 

3.1 Basic Requirements 

a. The system SHOULD be able to output signed zone data via an AXFR: 
   a. The system SHOULD be able to send the data as an AXFR in response to a request from a slave server.
   b. The system SHOULD be able to send NOTIFY messages to all configured slave servers when new zone data is available.
b. In the case of AXFR transfers, the system MUST support TSIG authentication with the downstream authoritative server.

3.2 Normal Operations 

3.2.2 Configuration 

a. A utility to check the policy for consistency SHOULD be provided: 
   a. It MUST be possible to run the utility independently of the rest of the system.
   b. The utility (or a variant) MUST be run automatically before the policy is loaded into the database.
   c. The utility SHOULD compare parameter values against the limits for that parameter and warn when the value lies outside the allowable range.
   d. The utility SHOULD check the values of all the parameters and warn if the combination is inconsistent.
   e. The utility SHOULD check the values of all the parameters and check that they form a policy that conforms to accepted practice.
      __Note: In practice, this means checking that the policy is not obviously absurd.__
b. The system SHOULD have a GUI that simplifies the configuration process.
   __Note: the GUI will be specified in a separate document.__

3.2.4 Reporting

a. The system SHOULD have an alternative method (e.g. email) of notifying the operator of urgent conditions.

3.5 PKCS#11

a. The signer MUST channel all traffic to providers through a session proxy, which keeps open a single session.
b. The signer MUST support PKCS#11 standard external authentication pads, to replace a filesystem-stored PIN.

3.6 Performance 

a. The Signer MUST be able to sign RRs with multiple threads.

3.7 Standards Conformance

a. The signatures created by the system and operation of the system MUST conform to the following standards:
   - RFC 5011

## Deliverables

It is expected that this project delivers some important deliverables during its life time:

- The software package  
  Called by the working name: DNSSEC Signer. Specified accordingly to its requirements.
- A software implementation of a HSM  
  Called by the working name: SoftHSM. Specified accordingly to its requirements.
- A project web page  
  Contains all the information about the project and has a source code repository.
- Comparison of HSM's  
  A study which compares a number of available HSMs. The reason is to give a good view of what choices a future user might have and what the user should expect concerning speed, price, configuration, etc.
- Market study  
  The first aim with the market study is to find out what requirements different user groups might have and if they are interested in a solution like DNSSEC Signer. The second aim is to formulate different use cases, which the DNSSEC Signer can be tested in to see if it fits the given requirements. The third aim is to find out how we should market our project.
- User guide  
  A complete and understandable user guide on how to get the DNSSEC Signer up and running with different use cases.
- Tests  
  Unit testing of each component, system tests and random tests. The tests check for requirements fulfilment, bugs, and security holes.
- Project evaluation  
  Did we manage to do fulfil the requirements? Was the project a success? Did we complete the tasks to the deadlines? Could we have done something different? What is the next step? What is the future of the project? Maintaining the code?

## Risk Management

This project has a greater chance of success if possible risks are analyzed and handled in good way.

### SWOT

The aim with a SWOT analysis is to get a better view of the current situation of the project and its environment. By having this analysis in mind future problems might be avoided.

| SWOT             |                                                                                                                                                                                                         |                                                                                                                                                                                                     |
|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Internal factors | **Strengths**<br><br>Practical experiences<br>High knowledge level<br>Good reputation<br>Developed similar projects<br><br>The basic technologies already exist<br>We do not have to reinvent the wheel | **Weaknesses**<br><br>Wide customer group<br>Decentralized project<br>Time pressure<br>Depending on multiple stakeholders<br><br>Outperformed by commercial<br>We do not have to reinvent the wheel |
| External factors | **Opportunities**<br><br>Increasing market demand                                                                                                                                                       | **Treats**<br><br>Outperformed by commercial products<br>Intellectual property claims<br>Do not meet customers requirements                                                                         |

### Risk analysis

Different potential risks are presented here and are assigned a value depending on its consequence and probability. This helps us identify which risks are the main threats to our project. These risks are then further analysed in the next section.


| # | Risk                                                                                   | Consequence (1-5) | Probability (1-5) | Risk value (CxP) |
|---|----------------------------------------------------------------------------------------|-------------------|-------------------|------------------|
| 1 | No/few customers knows about our product when the project is considered to be finished | 3                 | 4                 | 12               |
| 2 | Lack of time and resources                                                             | 4                 | 3                 | 12               |
| 3 | Lack of knowledge                                                                      | 4                 | 1                 | 4                |
| 4 | Key developers leaving the project                                                     | 4                 | 2                 | 8                |
| 5 | Non-discloser of source code                                                           | 5                 | 1                 | 5                |
| 6 | Unable to make a decision within the group                                             | 5                 | 2                 | 10               |

| # | Risk                                                                                   | Management                                                                                                                                                                                                 | Responsible | Finish date   |
|---|----------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------|---------------|
| 1 | No/few customers knows about our product when the project is considered to be finished | Make a marketing plan on how to let the market know about our product                                                                                                                                      | Rickard     | February 2009 |
| 2 | Lack of time and resources                                                             | Find out which resources this project has and how much they can commit to the project Then plan the project according to this. If it still is not feasible, then we have to look for other participants.   | Rickard     | January 2009  |
| 3 | Lack of knowledge                                                                      | Each subproject must look at their requirements and make sure that they have the knowledge to be able to finish their task. If this is not the case then a discussion must be made with the project group. | Everyone    | January 2009  |
| 4 | Key developers leaving the project                                                     | All of our work must be documented and published on our project web page. This also includes the source code.                                                                                              | Everyone    | January 2009  |
| 5 | Non-discloser of source code                                                           | All of the source code must be developed and published under a BSD license.                                                                                                                                | Everyone    | January 2009  |
| 6 | Unable to make a decision within the group                                             | All project members have their right to an opinion. All pros and cons are noted down and a voting is performed. The project manager makes sure that a decision is reached.                                 | Rickard     | January 2009  |

## Project Management processes

This chapter describes the overall management of the project.

Reports

The project manager will during the course of this project inform all project members of the current status via a newsletter. This newsletter will be published every second week via the  mailing list and on the wiki web page. The content is based on debriefings between the project manager and the individual project members.

Meetings

The project members get together on a regular basis. Each meeting is normally chaired by the project manager, but otherwise by a volunteering member. The meeting minutes are written by a volunteering member present at the meeting. The minutes are published via the  mailing list and the wiki web page. All the decisions are consensus decisions made by the group and not the project manager.

By Jabber 

A meeting can be performed via an instant messaging service, preferably  Jabber. Which conference channel to be used during a meeting is announced via the  mailing list.

By telephone 

The project aims at having a conference call every third week. How to connect to the conference call is announce via the  mailing list.

In real life 

A meeting in real life between the project members can not be performed so often, due to limited resources. An aim is to have these types of meetings in connection to other events where we all are participating, like conferences such as  IETF and  RIPE.

Budget

This project has no budget, thus relying on that the participants of the project has its own budget for the desired commitment.

External information 

The main source of information is available via our wiki page. This is where external parties can get further information about the project and access the source code.

Project acceptance 

When the deliverables have been done and accepted to their requirements, the project is considered to be finished and accepted by the participants.

Final report 

Before the project is considered to be finished according to the given limitations and requirements, a final report must be written which summarizes the project. The report must answer questions similar to:

- Did we manage to do fulfil the requirements?
- Was the project a success?
- Did we complete the tasks to the deadlines?
- Could we have done something different?
- What is the next step?
- What is the future of the project?
- Maintaining the code?

Documentation 

The project documents are presented via the  mailing list or the wiki page, where they can be approved by the project members.

## System test

1. Introduction

This document details the tests required to check that the OpenDNSSEC software conforms to the specified requirements. The tests fall into two groups:

- Checking the overall operation of the system.
- Checking specific requirements not covered in normal operation (such as start-up and shut-down).

This version of the document is applicable to the Alpha release of the software.

1.1 Installation 

The software should be installed according to the specified instructions.

1.2 Test Data 

The following sets of data will be used. zonegen.pl and other data files can be found in the repository.

- **LargeZone** - a zone file holding 100 000 RR. The data should comprise solely delegations - a mixture of NS records and glue records. (This is intended to simulate a large TLD.). Roughly 10% of the names should have a DS record associated with them.

    perl zonegen.pl --zonename <ZONE NAME> --nzones 1 --nrr 100000 --nns 2 --pns 100 --pds 10 --output <DIRECTORY>

- **MediumZone** - a zone file holding a single zone with about a hundred names. The resource records should be of a variety of types. (This is intended to simulate the zone file of a medium to large company.)

    perl zonegen.pl --zonename <ZONE NAME> --nzones 1 --nrr 150 --nns 2 --pns 5 --pds 50 -pa 100 -paaaa 10 --output <DIRECTORY>

- **SmallZone** - One thousand files, each holding a small zone. (This is intended to simulate the zone file of an ISP hosting web sites for a large number of customers.) Each zone should be a "typical" small zone comprising:
  - SOA record
  - two or three NS records (with at least one associated glue record)
  - MX record
  - A/AAAA for labels

    perl zonegen.pl --zonename <ZONE NAME SUFFIX> --nzones 1000 --nrr 4 -pa 100 -paaaa 10 --output <DIRECTORY>

If you want to add the zones automatically to OpenDNSSEC then add these flags:

    --addtoksm --config <DIRECTORY> --signeroutput <DIRECTORY> --policy <POLICY NAME>

2 Operations Tests

2.1 Basic Test

The basic test runs the system for a period of time (with very short key and signature lifetimes) to check that OpenDNSSEC can:

- Sign multiple zones
- Correctly handles key rollovers

It tests a number of the requirements in sections 2.3 (Signing Process) and 2.4 (Key Management) of the requirements document.

3. Other Tests

3.1 Startup/Shutdown?

The startup and shutdown test tests the requirements in section 2.2.1 concerning the startup and shutdown of the system.

## OpenDNSSEC Architecture Board

The OpenDNSSEC Architecture Board advices the OpenDNSSEC project management and consist of the following members:

- Jakob Schlyter, Kirei – chair
- Olaf Kolkman, Internet Society
- Siôn Lloyd, Nominet
- Joe Abley, Dyn
- Roland van Rijswijk, SURFnet
- Jacques Latour, CIRA
- Ondrej Surý, CZ.NIC
- Patrik Wallström, .SE
- Benno Overeinder, NLnet Labs

The OAB met at the four times a year, but is no longer active.

## Release Management Policy

The releases of our software are handled according to this process. They use the numbering policy described below and can go through three different pre-releases. The support policy is also outlined here.

### Version Numbering Policy

There are two different numbering schemes in use, one scheme for binaries and another for libraries.

#### Numbering of binaries

For earlier releases a component based numbering scheme was used (listed at the bottom of the page for reference). From 1.4.0 and 1.3.14 onwards OpenDNSSEC will switch to using an API based versioning scheme as described here: 

Releases are numbered using the following scheme: <name>-<major>.<minor>.<patch>. We base our scheme on that described by http://semver.org/ which summarises to:

-  <name> - The name of the software, e.g. opendnssec or softhsm.
-  <major> - Backwards incompatible changes must increase the major version.
-  <minor> - New, backwards compatible functionality, deprecated functionality (while maintaining backwards compatibility), or substantial new functionality within private code.
-  <patch> - Backwards compatible bug fixes or new, backwards compatible command line utility options (that will not change the behaviour for existing options).

Pre-releases have one of the following suffixes appended to the above. They are described more in detail in a section below.

- aN - alpha release (very volatile development releases)
- bN - beta release (no new features in beta releases)
- rcN - release candidate

Examples on version numbers:

- 1.0.0b1 is the 1st beta for 1.0.0
- 1.0.0rc1 is the 1st release candidate for 1.0.0
- 1.0.0 is the first real (production) release
- 1.0.1 is a fix for 1.0.0

#### Numbering of libraries

The shared libraries are numbered according to the libtool's versioning system: <http://www.gnu.org/software/libtool/manual/html_node/Libtool-versioning.html#Libtool-versioning>

#### Maintenance of old releases

From 1.3 we are adopting a split between Long Term Support (LTS) releases and Standard Releases somewhat along the lines of the Ubuntu model. Specifically: 

- Long Term Support Releases  
  Long Term Support releases will be supported until one year after the following Long Term Support release. 
  - 1.3 is declared as a the first Long Term Support Release
  - The next Long Term Support release will be 2.0 at the earliest
- Standard releases  
  Minor releases will be maintained (i.e. patch releases will be made available) for a fixed period after the release of the next minor release (or next major release if there is no further minor release on that branch).  As an example, 1.4 will be maintained for 1 year after the next (non-patch) release, which could be 1.5 or 2.0. The specific support period for a standard release will be announced when the production release of that version is made.

OpenDNSSEC 1.3 released on July 2011 was an LTS release and supported 1 year after the last LTS release.  
OpenDNSSEC 1.4 released on April as a standard release 2013 was supported for 1 year after the last minor/major release.  
SoftHSM 1.3 released on August 2011 was supported until 1 year after its last LTS release.

#### Versioning scheme used for early versions of OpenDNSSEC (pre 1.3.14)

Numbering of binaries: Releases were numbered using the following scheme: <name>-<major>.<minor>.<patch>

- <name> - The name of the software, e.g. opendnssec or softhsm.
- <major> - Indicate changes in the overall system design.
- <minor> - Indicate changes in the components.
- <patch> - Indicate bug fixes. 

### Release check list

This is a description of what should be done before a release.  Most steps are not that relevant for 2.x.
1. Decide to prepare a release  
   Team decides they are ready to prepare a release based on review of feature development and open bugs.
2. Update documentation  
   All developers update the content of the documentation pages.
3. Check the database migration tool is up to date  
   If any changes to the schema have been made the scripts may need updating
4. Agree the release type and support period  
   Is this a LTS or standard release? Update the Release policy page with a new minor/major release if needed.
5. Complete all testing  
   All developers ensure that their code passes all existing jenkins tests (and any new added for the feature). Does it compile on all known platforms? Are there no warnings? Does the test suite verify?
6. Code Reviews  
   Team performs reviews of the changes made between the latest and upcoming release. This might result in new changes. If so, iterate from step 3 until 'done'.
7. Run code integrity tools  
   Someone (TBA) run Coverity on the code base. This might result in new changes. If so, iterate from step 3 until 'done'.
8. Agree to release  
   Team decision that code is ready to release. 

### Release process

This is a description on how to do a release. To be done by the 'Project manager' unless otherwise stated.  Most steps should be updated.

Step 1.  Update the documentation

- Verify if the documentation it is up to date, correct and relevant. This includes the files README and KNOWN_ISSUES.
- Verify the NEWS file and check if all important feature additions, modifications and bugfixes are listed there. Check if all planned changes are present. 
- (partially outdated) For a major release only update the wiki space by creating a new version: Update the documentation, following the Document management process. 
  - Grab the new versions of the documentation in PDF and HTML format and add to the source in a 'docs' directory

Step 2: Testing

-     Double check the revision to be released passes all Jenkins tests. Run the daily/weekly tests manually if needed.

> :memo:
> All stable releases should have a release candidate made available to the maintainers for at least 1 week before the full release is performed.
> Note that no code freeze is put in place on the branch following the first release candidate so for the actual release (and any release candidates after the first one) the release should be based on the rc1 tag NOT the branch.
> Any fixes in the branch that are required in a subsequent release candidate must be manually merged with the rc1 code base.  

Step 3: Generate the release

- Create a tarball using the Release Engineering Process (Release engineer)
- Verify the tarball: Is it complete? Does it contain no unnecessary things (e.g. build files)? Does it work (build/install)? (Release engineer)

For a Release candidate

- Update the versions in JIRA
  - Create a rc version for next release (if it doesn't already exist) in OpenDNSSEC project
  - Mark this rc version as 'released' in the OpenDNSSEC project - JIRA should prompt you to move all open issues to the next release. Choose yes and do this.
  - Add the equivalent of the rc version to the SUPPORT project and mark it as released so that issues can be raised against it.
- Send an announcement to the maintainers list and try to include the planned date of the full release. Give maintainers at least a week for testing creating packages/updating ports. (PM)
  - If issues are found, a new release candidate has to be made and sent out again. 

For a Production release

- Add the new tarball to the Wordpress Download page.
- Update the versions in JIRA again
  - Change the name of the rc versions in both the OpenDNSSEC and SUPPORT projects to be a production version and update the release date.
  - Update any 'standard' JIRA queries to include the new version (the team meeting agenda also contains links to the current release)

Step 4: Announcements 

For a Release candidate

- Send an announcement to the maintainers list and try to include the planned date of the full release. Give maintainers at least a week for testing creating packages/updating ports. (PM)
  - If issues are found, a new release candidate has to be made and sent out again. 

For a Production release

- Announce the new version:
  - Add a new post on the Wordpress site
  - Send an e-mail to the announce mailing-list
  - Twitter message
  - Add a message to the Facebook page
  - New version on Freshmeat.net

### Release Engineering

1. Merge develop in master  
   Straight up Git, no flow. Example with 1.4 branch.

    - Make sure develop is up to date, NEWS edited, version.m4 updated (append rc1 to version)
    - get latest master and develop commits
        - `git fetch`
        - `git checkout origin/1.4/master`
    - Merge and tag. `--no-ff` to force git to create a merge commit
        - `git merge --no-ff origin/1.4/develop`
        - `git tag -a 1.4.8rc1`
    - push! Make sure you are a release manager in the github repo settings
        - https://github.com/orgs/opendnssec/teams/opendnssec-release-managers
        - `git push`
        - `git push --tags`

2. Create and sign tarball

    - Make sure you have access to the corp and dist repository. 'svn update' if necessary.
        - `svn co svn+ssh://www.opendnssec.org/svn/odscorp #read access`
        - `svn co svn+ssh://www.opendnssec.org/svn/odsdist #write access`
    - Make sure we have a complete clean copy of master, new clone is effective
        - `git clone https://github.com/opendnssec/opendnssec.git ods-release1.4.8`
        - `cd ods-release1.4.8/`
        - `git checkout origin/1.4/master`
    - Create tarball
        - `source prepdist.sh`
        - `make dist`
    - Copy tarball to dist:
        - use `~/odsdist/source/` for final release
        - `cp opendnssec-1.4.8rc1.tar.gz ~/odsdist/source/testing/`
    - Sign the tarball. The passphrase needs to be unlocked by Benno, Jakob or Yuri.
        - See `odscorp/development/pgp/README` for (hardly any) details
        - `cd ~/odscorp/development/pgp`
        - `sh sign-distfile.sh ~/repo/odsdist/source/testing/opendnssec-1.4.8rc1.tar.gz`
        - `cd ~/odsdist/source/testing/`
        - `gpg --verify opendnssec-1.4.8rc1.tar.gz.sig opendnssec-1.4.8rc1.tar.gz`
    - Add files to repository.
        - `svn add opendnssec-1.4.8rc1.tar.gz*`
        - `svn ci -m "OpenDNSSEC 1.4.8rc1"`

3. Announce rc1 on maintainers and announce list

4. Put release on website.

## Development process

This section is outdated.

The development process is divided into 8 steps. It is a life cycle that covers the major releases. 

![assets/developmentprocess](assets/developmentprocess.png)

Requirements

The formal requirements are gathered from the user community, but also from the OpenDNSSEC Architechture Board (OAB). These needs to be written down, sanitized, and prioritized. Not all of the requirements can be fulfilled in one release.

Specification

A set of functional specifications can be derived from the requirements. It does not describe the inner workings of the system, but are focusing more on the interaction between the system and outside world.

Architecture

The architecture is the overall design of the system. It is based on the requirements and the specification. The development team prepare a draft that is submitted to the OAB. The OAB will then help with the process of creating the architecture. This is to keep the development inline with general strategy and requirements.

Design

A more detailed design can be created once the architecture has been set. This is done within the development team. The aim is to describe each component and its internal structures.

Implementation

![assets/implementationprocess](assets/implementationprocess.png)

There are four different parts of the implementation phase. 

- Backlog  
  The backlog contains all features that need to be implemented and bugs that need to be fixed. The work items are created based on the design of the system. Each item has an estimated workload. The sum of the workload is then output as estimation for the release plan.
- Release backlog  
  Not all features can be implemented in one release, but must be split up into smaller minor releases. The OAB can help with this decision. The release backlog is a subset of the backlog and contains only the features needed for this release.
- Iterations  
  A normal iteration within an agile development process contains all parts of a software development cycle. But the iteration in our case is only the period between each telephone conference meeting where we have the chance to plan the release, give updates on our progress, and discuss current issues. We try to have a meeting every second week.
- Release  
  The developer team can initiate the release process when they e.g. want to get feedback or have implemented all features. The software then goes on to the next step, QA.


QA

The changes in this release are tested against the requirements and the specification.
- The developer does sufficient developer testing to ensure that the functionality is complete and the major use cases pass
- Ideally a second developer or tester then does manual testing of the features to include success and failure cases and corner cases. Any issues found are recorded on the JIRA issue for the development for the original developer to fix before re-testing. For major developments a separate testing issue may be created. 
- Regression tests should then also be put in place to cover the functionality in jenkins.

Release

The software is released to user community according to the Release Management process.

Maintenance

See the Release Management process for details of release maintenance.

