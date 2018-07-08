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
It is correct that _integrated NFC support_ in PCs is quite uncommon.  OTOH, why would vendors maintain support for NFC if it doesn't have any important task to fulfill?  Unfortunately NFC has in spite of nice characteristics, gotten serious competition from QR even for payments in physical shops.

Although the latter is not a part of this proposal, it is related since the idea is that somewhere down the line, you should be
able to use the same payment protocols in physical shops as on-line.

## Architectural Overwiew
https://cyberphone.github.io/doc/research/nfc-based-qr-replacement2.pdf

Note: this proposal outlines a pure _security protocol_ which means that it presumes that the software running in Servers, PCs and Phones is operating correctly.

## Attack Vectors and their Mitigation
The following analyses may indeed be incorrect.  This is why I have requested a security review 😀.

# Phishing
The user clicks on a Web link received in an email or chat, or is encountered on a Web site.
The link opens a malicious Web site ("yourbank.business.f6s4f.com") typically masquerading as a bank or similar.
The user is encouraged logging in.  Before showing the NFC symbol in a Web page, the malicious site calls the target site in order to get the required NFC data.  Then malicious site creates a Web page calling NFC with the received data.
Unfortunately (for the attacker) the NFC driver will not produce any output since the Web page displayed to the
user have another host than the target site.  See point \#4 in the Architectural Overwiew.

If the malicious site rather tries to reuse an authentic login to itself at another site
