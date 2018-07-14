# “A Better QR”
![betterqr](https://cloud.githubusercontent.com/assets/8044211/26782217/c320c982-49f2-11e7-90ac-348677374ba3.png)

## Background
Using QR codes on the Web together with mobile phones acting as _Identity Tokens_ or _Wallets_ has been a huge success.

There is essentially only one but rather obvious snag; you need to start a specific QR- or QR-enabled application in order to use such a system.

However, if you scratch a bit under the surface of these systems you will find that they suffer from a fairly ugly security flaw as well: _There is no secure binding between the page showing the QR-code and the QR-code itself_. This fact has been successfully exploited by criminals who with simple phishing scams have lured people logging in to their bank for "Important Information" giving the phisher access to the account rather than the user. There is currently no publicly documented workaround for this vulnerability.

Some systems only use a short identification rather than QR-code making them susceptible to "Social Engineering" scams over the phone.

Note: even the most advanced systems out there using _Security Elements_ and _Asymmetric Key Cryptography_ exhibit these problems.

This is how most QR based schemes work today:

1. The user wants to login or have reached a Web page asking for a payment.  Note: using a **_PC_**
2. The service offers a (preferably one-time) QR code in a Web page
3. The user starts a QR enabled application. Note: using a **_Phone_**
4. The application scans the QR code aided by the user doing the focusing
5. The QR enabled application performs the authentication or transaction using OOB (Out Of Band) communication based on the read QR data

## Proposal
This repository contains a proposal for a _Dedicated_, _Write-only_ Web-invoked NFC system intended as a replacement for inconvenient and security-broken QR schemes. The primary applications are authentication and payments.

## NFC in PCs
It is correct that _integrated NFC support_ in PCs is quite uncommon.  OTOH, why would vendors maintain support for NFC if it doesn't have any important task to perform?  Unfortunately NFC has in spite of nice characteristics, gotten serious competition from QR even for payments in physical shops.

## Possible "Side Effects"
Although uniting payment protocols is not a part of this proposal,
it is related since the idea is that somewhere down the line, it should be
possible using the same payment protocols in physical shops as on-line.
NFC would in such a use case only be used to setup the
communication channel while the actual payment protocol would use HTTP or WebSocket.

![united](https://user-images.githubusercontent.com/8044211/42658603-1bf6c870-8626-11e8-95b5-a6d7cc444f4c.png)
In such a setup there is no need for a traditional payment terminal since the entire client side of the
UI and security is catered for by the mobile device.

## Architectural Overview
https://cyberphone.github.io/doc/research/nfc-based-qr-replacement2.pdf

Note: this proposal outlines a pure _Security Protocol_ which means that it presumes that the software running in Servers, PCs and Phones is operating correctly.

## Attack Vectors and their Mitigation
The following analysis may indeed be incorrect.  This is why I have requested a security review 😀.

### Intercepting NFC
Intercepting NFC over quite long distances has been reported as feasible.
However, the only thing it buys you is stealing the user's login attempt, not the login itself.

### Phishing
The user clicks on a Web link received in an email or chat, or is encountered on a Web site.
The link opens a malicious Web site like `yourbank.business.f6s4f.com` typically masquerading as a bank or similar.
The user is encouraged logging in.  Before responding to the user with an NFC-using Web page,
the malicious site calls the target site in order to start a session of its own.
Then the malicious site returns a Web page for the user calling NFC with the received data.
Unfortunately (for the attacker) the NFC driver will not produce any output since the Web page displayed to the
user has another host than the target site.  See point \#4 in the [architectural overview](#architectural-overview).

If a malicious site rather tries to _reuse_ an authentic login to itself at another site, the check performed at \#10.1
in the [architectural overview](#architectural-overview) should thwart such efforts even if the session information is correct.

### Social Engineering Scams
There is (AFAICT) no way a person calling you on the phone, asking you to login somewhere,
could take or reuse that login.

### Unresolved: Dynamic NFC Data R/W
The most sophisticated attack I have come up with so far requires the attacker to:
1. Create a session with the target site
2. Intercept and rewrite NFC data on the fly by replacing the user's URL with the attacker's URL

There doesn't seem to be any real mitigation to this attack.  OTOH, it seems quite
complex to succeed with. If we are talking public computers, hacking the browser would
be a much more workable solution since it defeats pretty much all security solutions!

## Security Considerations
This proposal depends on an updated browser API which means that possible
side-effects with respect to security and privacy must be considered.
AFAICT, the system does not introduce issues beyond what QR already do,
but _it might be useful disabling the NFC API on hidden pages_.

One could imagine having a special HTML object but I believe this would
be overkill; it should suffice that the NFC-using page features an
icon.

A component that does require specific security measures is the mobile device.
However, these are application specific.  For an authentication scheme like
shown in the Architectural Overview, an alert message `"this is your first login to 'xyz.com', do you want to continue"`
seems appropriate.
