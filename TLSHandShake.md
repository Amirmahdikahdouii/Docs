# TLS Protocol

**Sources:**

- [CloudFlare - What happens in a TLS Handshake?](https://www.cloudflare.com/learning/ssl/what-happens-in-a-tls-handshake/)
- [CloudFlare - What is TLS?](https://www.cloudflare.com/learning/ssl/transport-layer-security-tls/)

## What is a TLS Handshake?

TLS or **Transport Security Protocol** is a secure protocol that designed to facilitate privacy and data security for communications over internet.
TLS could be used for encrypt data between servers and web applications, it also could be useful in email communications, messaging and [Voice over IP(VoIP)](https://www.cloudflare.com/learning/video/what-is-voip/).

> [!NOTE]
> TLS protocol has different versions but the one that is most used nowadays is Version 1.3

## TLS vs SSL

### What is SSL?

Secure Socket Layer or **SSL** is an encryption based internet security protocol, developed by Netscape.

> [!IMPORTANT]
> SSL is the predecessor to the modern TLS encryption used today.
> A website that implements **SSL/TLS**  has **HTTPS** in it's URL instead of **HTTP**

### What is the differences between TLS and SSL?

TLS evolved from SSL, TLS version 1.0 actually began development as SSL version 3.1, but the name of the protocol was changed before publication in order to indicate that it is no longer associated with Netscape.

## TLS vs HTTPS

HTTPS is an implementation of TLS encryption on top of the `HTTP` protocol, so any website that uses HTTPS is therefore employing TLS encryption.

> [!NOTE]
> Businesses and web applications should use HTTPS instead of HTTP because of it is more secure and reliable.
> Some browsers like `Google Chrome` block or warn websites that use **HTTP** instead of **HTTPS**

## What does TLS do?

In fact, TLS in separated in 3 main sections:

1. **Encryption**: Hide the data being transferred from third parties
2. **Authentication**: ensures that the parties exchanging information are who they claim to be
3. **Integrity**: verifies that the data has not been forged or tampered with

A TLS connection is initiated using a sequence known as the **TLS Handshake**.
During the TLS Handshake, the client and server:

- Specify which version of TLS they will use.
- Decide on which cipher suites they will use
- Authenticate the identity of the server using the server TLS's certificate
- Generate session keys for encrypting messages between them after the handshake is complete

> [!IMPORTANT]
> The TLS Handshake create a cipher suite for each communication session. The cipher suite is a set of algorithms that specifies details such as which shared [encryption keys](https://www.cloudflare.com/learning/ssl/what-is-a-cryptographic-key/), or [session keys](https://www.cloudflare.com/learning/ssl/what-is-a-session-key/) will be used for that particular session.
> TLS is able to set the matching session keys over an unencrypted channel thanks to a technology known as [public key cryptography](https://www.cloudflare.com/learning/ssl/how-does-public-key-encryption-work/)

The TLS handshake also check the server identity for proving to client. This is done using **public keys**. The server public key is a part of its TLS certificate.

Once data is encrypted and authenticated, it is then signed with a message authentication code (MAC). The recipient can then verify the MAC to ensure the integrity of the data.

![TLS Handshake](/assets/images/tls-ssl-handshake.png)

> [!IMPORTANT]
> For better understanding of how is actually TLS Handshake working with details, you can see this [Youtube video](https://youtu.be/ZkL10eoG1PY?si=BmxB9-irZVozjVfa)