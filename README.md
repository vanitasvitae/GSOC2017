# Google Summer of Code

## Paul Moritz Schaub - OMEMO Encrypted Jingle File Transfer in Smack

Welcome to my Project page for my GSoC 2017 project!

The goal of my project was to implement Jingle File Transfer for [Smack](https://github.com/igniterealtime/Smack/). Smack is a client library for the [XMPP](https://xmpp.org/) protocol for Java and Android.

XMPP stands for eXtensible Message and Presence Protocol. The focus lays on _extensible_. Many features are not part of the core specification, but instead are defined in so called XEPs (XMPP Extension Protocols). XEPs are numbered, so every XEP has a unique number and a name to identify it. The goal of my project was to implement XEP-0234 - Jingle File Transfer. In order to achieve this goal, I had to implement a whole number of XEPs, since there are some transitive dependencies.

### XEP-0234 - Jingle File Transfer
This XEP describes how two entities can exchange files using the Jingle Protocol. Jingle itself is another XEP which I also had to implement (see below), since Jingle File Transfer is defined on top of it. The specification for XEP-0234 can be found [here](https://xmpp.org/extensions/xep-0234.html). The code I wrote for that XEP is available in [this](https://github.com/vanitasvitae/Smack/tree/jingle3/smack-experimental/src/main/java/org/jivesoftware/smackx/jingle_filetransfer) package.

### XEP-0166 - Jingle 
The Jingle XEP defines how two entities can negotiate bytestreams between each other. This involves, which transport methods are used (examples for that down below), which parameters are used for the application of the bytestream (eg. which audio codec in a voice chat) and so on. Also security plays a role in the negotiation. The Jingle protocol consists of three main components:

 - Description: Describes the application of the session (eg. file transfer - see XEP-0234)
 - Transport: The transport method used. This might be InBandBytestreams, SOCKS5 proxies...
 - Security: How the bytestream is secured using encryption.
 
A session consists of one or more contents, which in turn consist of a description, a transport method and a security component. Those components can be composed and replaced in arbitrary ways.

Implementing the Jingle XEP was the biggest part of my project. I worked using the waterfall model to learn from previous errors I made and it took me 3 iterations, until I was happy with my code. Now I have a very modular codeset which can be easily extended and composed in any way. The specification is available [here](https://xmpp.org/extensions/xep-0166.html), while my code can be found [here](https://github.com/vanitasvitae/Smack/tree/jingle3/smack-extensions/src/main/java/org/jivesoftware/smackx/jingle).

### XEP-0261 - Jingle InBandBytestream Transport
The easiest, most basic transport method is described in XEP-261. When data is sent using InBandBytestreams, the raw data is split into blocks of typically 4096 byte, which are base64 encoded and then sent one after another via the XMPP protocol itself. This method is occasionally very slow, but since it depends only on the XMPP protocol itself, it does not require additional server infrastructure. The XEP can be found [here](https://xmpp.org/extensions/xep-0261.html), my implementation resides in [this package](https://github.com/vanitasvitae/Smack/tree/jingle3/smack-extensions/src/main/java/org/jivesoftware/smackx/jingle/transport/jingle_ibb).

### XEP-0260 - Jingle SOCKS5Bytestream Transport
A more advanced transport method is described in Jingle SOCKS5Bytestream Transports (short Jingle S5B). Here SOCKS5 proxy servers are utilized to enabled clients behind firewalls or NATs to establish connections with each other. It is possible to use either external servers (eg. proxy components of the XMPP servers), or local servers for this purpose. The XEP can be found [here](https://xmpp.org/extensions/xep-0260.html), my code [here](https://github.com/vanitasvitae/Smack/tree/jingle3/smack-extensions/src/main/java/org/jivesoftware/smackx/jingle/transport/jingle_s5b).

Luckily for me, both XEP-0261 and XEP-0260 are based on two older XEPs ([XEP-0047](https://xmpp.org/extensions/xep-0047.html) and [XEP-0065](https://xmpp.org/extensions/xep-0065.html)), so I could reuse some already existing code for those two transport methods.

### XEP-0300 - Use of Cryptographic Hash Functions
A Jingle File Transfer involves checksums to ensure the integrity of the sent file. Since computer science is a rapidly changing field, hash functions might quickly get obsolete (see SHA1 - [well done you](https://shattered.io/), Google :D), so it is desirable to be able to quickly replace hash functions. For that purpose XEP-0300 defines namespaces for hash functions, so that negotiators can use different algorithms as they wish. The specification can be found [here](https://xmpp.org/extensions/xep-0300.html), while my code lives [here](https://github.com/vanitasvitae/Smack/tree/jingle3/smack-experimental/src/main/java/org/jivesoftware/smackx/hashes).

### XEP-xxxx - Jingle Encrypted Transports (JET)
The most exciting part of my project was creating my own specification! With _JET_ I specified a way to utilize [OMEMO](https://conversations.im/omemo/xep-omemo.html) and [OX](https://xmpp.org/extensions/xep-0374.html) (OpenPGP) end-to-end encryption to secure Jingle transports. This way entities can transfer bytestreams end-to-end encrypted. The specification allows for easy extensibility with other encryption mechanisms additionally to OMEMO or OX. My plan is to propose the specification to the [XSF](https://xmpp.org/about/xmpp-standards-foundation.html) for approval, so that it might one day become an official XEP.

My draft specification can be found [here](https://geekplace.eu/xeps/xep-jet/xep-jet.html), while my prototype code can be found [here](https://github.com/vanitasvitae/Smack/tree/jingle3/smack-experimental/src/main/java/org/jivesoftware/smackx/jet) (only OMEMO encryption so far).

## Status
Not all of my code has been merged into Smacks master branch yet. This is partially due to experimental features, which need approval by the XSF.

Merged
 - XEP-0166 - Jingle
 - XEP-0260 - Jingle S5B
 - XEP-0261 - Jingle IBB
 - XEP-0300 - Hashes
 
Pending
 - XEP-0234 - Jingle File Transfer
 - XEP-xxxx - Jingle Encrypted Transports
 
## Interoperability
The whole point of XMPP is to avoid walled gardens, so there are many clients implementing many of the specified features. Ideally all clients are interoperable to one another (in reality it doesn't always go so well unfortunately).
This is why I'm very happy to announce that my file transfer code has been successfully tested against Gajim, a well established XMPP client written in python, as well as against Conversations, a popular XMPP client for Android.

## Demo Application
Since my works is mostly invisible library code, I wrote a small application to demonstrate some features.
_XMPP_SYNC_ is a command line app, which can be used to transfer files from one folder on one computer to another.

I created a seperate repository for _XMPP_SYNC_ which can be found [here](https://github.com/vanitasvitae/xmpp_sync).
