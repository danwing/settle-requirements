---
title: "Requirements for HTTPS for Local Domains"
abbrev: "Requirements for HTTPS for Local Domains"
category: info

docname: draft-wing-settle-requirements-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
# area: AREA
# workgroup: WG Working Group
keyword:
 - https
 - local domains
 - pki
 - pkix
 - certificate authority
 - tls
 - identity
 - authentication
venue:
  group: SETTLE
  type: ""
  mail: settle@ietf.org
  arch: https://mailarchive.ietf.org/arch/browse/settle/
  github: "danwing/settle-requirements"
  latest: "https://danwing.github.io/settle-requirements/draft-wing-settle-requirements.html"

author:
 -
    ins: D. Wing
    name: Dan Wing
    organization: Cloud Software Group, Inc.
    abbrev: Citrix
    email: danwing@gmail.com
 -
    fullname: Erik Nygren
    organization: Akamai Technologies
    email: erik+ietf@nygren.org
    uri: http://erik.nygren.org/
 -
    ins: M. Richardson
    name: Michael Richardson
    org: Sandelman Software Works
    email: mcr+ietf@sandelman.ca

normative:

informative:
  thomson-hld:
    title: "HTTPS for Local Domains"
    author:
      name: Martin Thomson
    date: September 2017
    target: https://docs.google.com/document/u/0/d/170rFC91jqvpFrKIqG4K8Vox8AL4LeQXzfikBQXYPmzU/edit


  Safari-ext:
    title: "Discovery - DNS-SD Browser"
    author:
      name: Lily Ballard
    date: 2022
    target: https://apps.apple.com/ca/app/discovery-dns-sd-browser/id1381004916?mt=12

  Firefox-ext:
    title: "mDNS Discover"
    author:
      name: Dale Smith
    date: December 2020
    target: https://addons.mozilla.org/en-US/firefox/addon/mdns-discover/

  tpac:
    title: "HTTPS for Local Networks"
    author:
      org: W3C
      name: Carlos IL
    date: September 2024
    target:  https://github.com/w3c/tpac2024-breakouts/issues/78

  phb-mesh:
     title: "Mathematical Mesh"
     author:
       org:
       name: Phillip Hallam-Baker
     date: 2022
     target: https://github.com/hallambaker/Mathematical-Mesh

  zookotriangle:
     title: "Zooko's triangle"
     author:
       org: Wikipedia
     target: "https://en.wikipedia.org/wiki/Zooko%27s_triangle"
     date: March 2025

  iotops-suib-prezo:
     title: "SUIB: Browsing local web resources in a secure usable manner"
     author:
       -
         name: Jan Geertsma
       -
         name: Christian Amsüss
       -
         name: Micheal Richardson
       -
         name: Nick Allott
     date: November 2021
     ann: "Presentation of IOT Security Foundation SUIB to IETF112 IOTOPS working group"
     target: https://datatracker.ietf.org/meeting/112/materials/slides-112-iotops-suib-browsing-local-web-resources-in-a-secure-usable-manner-iot-device-configuration-as-a-special-case-00.pdf

  iotops-suib:
     title: "SUIB: Router and IoT Vulnerabilities: Insecure by Design"
     author:
       org: IOT Security Foundation
     date: August 2021
     target: https://iotsecurityfoundation.org/wp-content/uploads/2021/08/ManySecured-SUIB-White-Paper.pdf

  iotsf:
     title: "IOT Security Foundation"
     date: September 2015
     target: https://iotsecurityfoundation.org

  stark:
     title: "When a web PKI certificate won't cut it"
     date: December 2021
     author:
       name: Emily M. Stark
     target: https://emilymstark.com/2021/12/24/when-a-web-pki-certificate-wont-cut-it.html

  shared:
     title: "APPROACH-2: Using Shared Secret"
     date: September 2019
     author:
       org: W3C
     target: https://httpslocal.github.io/proposals/#approach-2

  plex:
     title: How Plex Is Doing Https for All Its Users
     date: June 2015
     author:
       name: Filippo Valsorda
     target: https://words.filippo.io/how-plex-is-doing-https-for-all-its-users/

  w3c-httpslocal:
     title: "HTTPS in Local Network Community Group"
     date: 2019
     author:
       org: W3C
     target: https://github.com/httpslocal

  w3c-pna:
     title: Private Network Access
     date: September 2024
     author:
       org: W3C
     target: https://wicg.github.io/private-network-access/

  sec-context:
     title: "Secure Contexts"
     date: 2023
     author:
       org: W3C
     target: https://w3c.github.io/webappsec-secure-contexts/

  not-secure:
     title: "A secure web is here to stay"
     date: 2018
     author:
       org: Google
     target: https://blog.chromium.org/2018/02/a-secure-web-is-here-to-stay.html

  smb-quic:
     title: "SMB over QUIC"
     date: December 2024
     author:
       org: Microsoft
     target: https://learn.microsoft.com/en-us/windows-server/storage/file-server/smb-over-quic

  everywhere:
     title: "HTTPS Everywhere"
     date: March 2025
     author:
       org: EFF
     target: "https://www.eff.org/https-everywhere"

--- abstract

When connecting to servers on their local network, users are
surprised to encounter user interfaces that display errors,
show insecure connections, and block some HTTP features
when missing a secure context.  However, obtaining PKIX
certificates for those servers is difficult for a variety
of reasons.

This document explores requirements for authenticating local servers.

--- middle

# Introduction

Servers on local networks have historically settled for
unencrypted communications -- printers, routers, network attached
storage (NAS).
However, with the advent of HTTPS everywhere {{everywhere}}, browsers disadvantage unencrypted
communications (e.g., {{not-secure}}, {{sec-context}}).
This increases importance of a secure context (HTTPS) to local domains.

In addition, it is recognized that home networks are not (and perhaps have never been) the idyllic secure gardens that many think they are.
There are persistent threats in the home due to malware on devices within the home, as well as malware that might arrive on guest devices.
Most home networks have little protection against various kinds of (layer-2) spoofing attacks, which means that active on-path attacks (MITM) must be assumed.
Securing the administrative and regular connections within the home network would result in significant security gains for all devices in the home.

Today, a secure context is obtained with a PKIX certificate ({{?RFC5280}})
signed by a Certification Authority (CA) that is trusted by the client.

However, servers on a local network cannot easily get PKIX
certificates signed by a Certification Authority because: they are not directly reachable from the outside (due to firewall or NAPT), lack of domain name delegation, and need for ongoing certificate renewal.

The problem has been well recognized since about 2010 and several
proposals have been suggested to solve this problem, each with their
own drawbacks.  This document is not intended to summarize these proposals
or their drawbacks; for that detail see the pointers to previous work
in {{related}}.  At a high level, the proposals have involved
solutions such as:

  * pre-shared secrets (scanned, printed, or displayed by the server)

  * Public DNS pointing at local domain's IP address (e.g., {{plex}})

  * Local Certification Authority, where a CA is
    added to client's certificate trust list and that CA signs
    certificates for devices within the local network

  * Trust On First Use (TOFU), where a user verifies the first
    connection to a server and the client remembers that verification,
    similar to common use of ssh

  * WebRTC and WebTransport, where a PKI-signed server provides a
    public key fingerprint of another server that it has previously
    bootstrapped

  * Encoding server's public key into the hostname {{thomson-hld}}

This document explores IETF requirements for an alternative server
authentication system for local hosts.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Technical Requirements

The goal is to work out the engineering tradeoff around {{zookotriangle}}.
Specifically it says there are three aspects that must be traded off:

* Human-meaningful
* Secure
* Decentralized

## Naming

PKIX certificates are a centralized naming scheme derived from DNS.
These names have (the possibility of) having human-readable names.
But the most significant property is uniqueness -- each name has its own
identity and that identity can be proven.

A system that does not rely on centralized naming lacks this inherient
uniqueness property.

Name collisions can be engineered by attackers for nefarious purposes.
For example, if a victim is configured to use the (likely) unique
name "printer-12ab34cd56ef.local" (containing the printer's full or
partial MAC address), an attacker can respond to connections to that
name, potentially stealing the user's authentication credentials
to that printer or seeing the content the user sent to that printer.
Similar attacks are possible with file shares.  This problem is
exacerbated if non-unique names are used (e.g., simply "printer.local"
or "router.local"), as it reduces the attacker's effort.
Humans prefer simple, human-readable
names, but a strong identity cannot be created with such names.

> R-UNIQUE-NAME: The system MUST have a way to uniquely identify
  servers.


## Cryptographic Binding

A server's name has to be mapped to its cryptographic identity.

> R-BINDING: The Web Origin MUST be cryptographically bound to one or
  more key pairs, where the private keying material is on the service
  endpoint and where an attacker without the private key(s) is unable
  to access any state associated with the Web Origin.

A client has to be able to validate the name maps to the cryptographic
identity.

> R-VALIDATE: Clients MUST be able to cryptographically validate that
  the authenticating server matches the identity in the URI / Web
  Origin.

Web browsers and modern users both expect a URI.

> R-URI: It MUST be possible to construct a URI that encapsulates a
  Web Origin and its cryptographically-bound identity information.


## Abstract Naming

Using IP addresses in names is problematic if the server's IP address
changes due to ISP renumbering or internal network DHCP server
reconfiguration.

Given common NAT44 (NAPT), many many networks will share the same IPv4 addresses.

> R-ABSTRACT: The solution SHOULD abstract names from IP addresses.

Any given name should be resolvable to a mixture of IPv4, IPv6
Link-Local (on an Interface), IPv6 ULA, and IPv6 Globally-Routable
addresses. Operating a local DNS is beyond the scope of many
administrators, so being able to advertise the server using
{{?DNS-SD=RFC6763}} is necessary.

> R-DNS-SD: The name MUST be advertisable using {{?DNS-SD=RFC6763}}



## Avoid Central Authority

A solution needs to be self-contained and not use the central
authority of PKIX.  Being self-contained also removes reliance
on a device vendor (to operate a centralized service).

> R-AVOID-CENTRAL: A solution MUST NOT rely on central
  trust hierarchy.

Vendors go out of business or lose interest in continuing to service
old products. The products may still be operational.

> R-AVOID-VENDOR: A solution SHOULD NOT (MUST NOT?) have continued
  reliance on a service operated by a vendor, including if the device
  is reset to factory defaults (e.g., reset for troubleshotting or
  because sold).


## Multiple Application Protocols

A solution has to support HTTPS because it is frequently used for
device management.

> R-HTTPS: A solution MUST support HTTPS.

Other encrypted protocols are also frequently used on local networks
for DNS, file sharing, mail, and telephony.

> R-MULT-APP: A solution SHOULD support other application-level
  protocols such as DoT {{?RFC7858}}, SMB over
  QUIC {{smb-quic}}, IMAP {{?RFC8314}}, and SIP {{?RFC3261}}, as
  those protocols are routinely served within a local domain.


## Cryptographic Agility

A solution has to support moving to new cryptographic functions.

> R-AGILITY: A solution SHOULD support cryptographic agility (such as
  supporting more than one active key type and different hashes).

## TLS Server Name Indication

TLS servers frequently use the TLS SNI {{?RFC6066}} extension to
support multiple identities on a single server.

> R-TLS-SNI: A solution SHOULD support TLS SNI so a server knows which
  key pair/cert is expected.



## W3C Private Network Access

To prevent various attacks, W3C has constrained how browsers operate
on private networks

> R-PNA: A solution SHOULD integrate well with an evolution of
{{w3c-pna}} and both allow for an improved model there but should also
provide more robust solutions to vulnerabilities that it tries to
address

## Operate with Local Resources

The TLDs ".local" and ".internal" are defined local domains and
enterprise networks usually have a site domain
("internal.example.com").  A solution that scales from a home
network to an enterprise network is desirable.

> R-LOCAL: A solution MUST operate with  .local and .internal, and SHOULD
operate with an administratively-defined zone (e.g., internal.example.com).

> Discuss: MAY constrain to the DHCP domain-search value??  Should we
also allow any arbitrary name if the IP address is local (RFC1918
address), too?

## Operate Standalone

The system needs to operate without a connection to the Internet.
This is necessary because Internet connectivity is sometimes flaky or
unavailable (e.g., cabin in the woods, lengthy ISP outage).

> R-STANDALONE: MUST NOT require Internet connectivity to operate
securely, for its initial configuration, or to add or remove a device
from list of authorized devices in the system.

> Discuss: perhaps want to refine wording of this requirement, or
split into separate requirements.


## Web Origin {#web-origin}

The Web Origin is comprised of the scheme (e.g., "https:"), hostname, and port.  Today,
when a key rotation occurs the Web Origin remains the same.  In this way, things like
stored web data (forms, passwords, cookies) can be used even after a key rotation.

> R-WEB-ORIGIN:  The Web Origin MUST be retained during key rotation.


## Miscellaneous

1. It SHOULD be possible to have a way to represent a URI that
includes a single specific IP address and the cryptographic identity
of the service endpoint.

> Discuss: the above requirement needs to be re-written.


1. SHOULD support key rotation (even if via 301 redirect)

* Q: is it acceptable to state to be lost here?  Note: likely cannot
do 301 if doing TLS (HTTPS).  Is this suggestion to start HTTP and
upgrade to HTTPS?  Could be useful for HTTPS but redirect unavailable
for IPP, SMB, DoH.

> Discuss: the above requirement needs to be re-written.


1. SHOULD support building trust relationships within devices in the
local environment

> Discuss: the above requirement needs to be re-written.


1. Could this help with HTTPS access to Wi-Fi login portals
({{?RFC8952}}, {{?RFC8910}})?

> Discuss: the above requirement needs to be re-written.

# Human Factors Requirements


## Discoverable

Most local networks, especially home networks, do not operate their
own DNS server.  Many clients already support listening for DNS-SD
broadcasts.

> R-DISCOVER: A solution SHOULD have a way to do discovery of
endpoints and their identities (for example, via {{?DNS-SD=RFC6763}}).


## Easy to Use

Successful solutions are usually also easy to deploy.

> R-EASY: A solution SHOULD have human factors and adversarial testing
on proposed solutions to make sure that this solution provides a
reasonable experience to average and novice end-users and does not
introduce new security exploitation vectors

## Bookmarkable

Names (or aliases to those names) should be simple for users -- ideally,
user-defined so that if the underlying name is complex the user can
create an alias that is meaningful to them.

> R-BOOKMARK: A solution SHOULD have a URI that users can Bookmark to create an association
to a friendly name.

> Discussion: Can URL bar of the browser honor mDNS/DNSSD advertised
names, or give a pull-down of them similar to how the "add printer"
dialog does for printers?  This would help ease the use of long FQDN
so it's almost as easy as router.local.  Especially if it could show a
nickname that is configured by the printer.  Browser extensions
exist for DNS-SD and mDNS ({{Safari-ext}}, {{Firefox-ext}}).

## Human-friendly Name

Names (or aliases to those names) should be simple for users -- ideally,
user-defined so that if the underlying name is complex the user can
create an alias that is meaningful to them.

> R-CONSISTENT: A solution SHOULD represent these URIs to humans in a
consistent, readable, and non-confusing fashion.  (In a browser,
users shouldn't see the key fingerprint by default but rather a
representation of its presence)


# Big Open Questions



## Trust on First Use (TOFU)

As evidenced by web browser behavior over the years with self-signed certificates
and their (increased) warnings, TOFU will not be acceptable.


## User Experience

For a solution, what is the User Experience for any trust relationship / web-of-trust?

## Trust Relationship

For a solution, what is the nature of the trust relationship?

   * Peer trust web?

   * Central CA within the local environment / trust clearing house?

   * Client establishes its own trust to the server

## Interaction with Matter/Thread

How does a solution tie into systems like Matter/Thread that have their own trust establishment frameworks?



# Use Cases

For the below, "Secure communications" means being able to make a TLS
connection to a service such that the service is able to authenticate
itself in a way to prevent MitM attacks.  The security model must be
TOFU at a minimum, but when the identity of a service is none it
should be possible to send it as a URI in such as a way to present a
secure association rooted in the connection that sent it:

* Secure communications via HTTPS to admin interfaces on CPEs for
both initial and ongoing configuration tasks of various servers
(router, printer, NAS, etc.).

* Secure communications to DoH/DoT servers on CPEs

* Secure communications to printers (IPPS {{?RFC7472}} printing)

* Secure communications to other local services (SMB over QUIC to
  another workstation or a NAS) and IoT devices

* Secure communications to localhost processes from a browser (e.g.,
  admin tools)


# Related {#related}

Martin Thomson wrote HTTPS for Local Domains {{thomson-hld}} which
covers requirements, discusses several solutions and their tradeoffs,
and suggests a solution where the client extends the Web Origin to
include the server's public key. It does not allow the server to
rotate its public key as that would change the extended Web Origin (see
{{web-origin}}).

Dan Wing has proposed a Referee system {{?I-D.wing-settle-referee}}
which uses a new HTTPS-based server to authorize servers public keys
(akin to an allowlist or to OCSP stapling) and encoding the server's
public key into its hostname.  Like {{thomson-hld}}, it does not allow
a server to rotate its key (see {{web-origin}}).

Michael Sweet has proposed a locally-deployed Certification Authority
{{?I-D.sweet-iot-acme}} that can be incrementally deployed.

W3C worked on this problem from 2017 through 2021 {{w3c-httpslocal}}. More recently,
W3C had a workshop on the problem in September 2024 {{tpac}}.

The boundaries of a limited domain -- such as the local domain described in this
document -- are explored in {{Section 6 of ?RFC8799}}.

The IOTOPS working group and the associated IOT Security Foundation
{{iotsf}} discussed the problem and some requirements in their white
paper {{iotops-suib}} and presentation to IOTOPS working group at
IETF112 {{iotops-suib-prezo}}.

A threshold key system is described and implemented at {{phb-mesh}} with the following
description:

> The Mesh is designed to provide users with the highest level of
  security that is possible without asking them to do anything at
  all. For this to become possible, the Mesh will have to be shipped
  to users as part of the machine Operating System.

A summary of the problem and analysis of several solutions
(Locally-installed CAs, Plex, WebRTC and WebTransport, TOFU, shared
secrets) and some drawbacks of those solutions is at {{stark}}.

A method using PAKE and a shared secret (displayed on the server) is
explained at {{shared}}.

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

Thanks to Michael Sweet for his review and feedback.


