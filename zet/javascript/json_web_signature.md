# JSON Web Sgnature (JWS)

## Abstract

JSON Web Signature (JWS) represents content secured with digital
signatures or Message Authentication Codes (MACs) using JSON-based data
structures. Cryptographic algorithms and identifiers for use with this
specification are described in the separate JSON Web Algorithms (JWA)
specification and an IANA registry defined by that specification.
Related encryption capabilities are described in the separate JSON Web
Encryption (JWE) specification.

## Status of This Memo

This is an Internet Standards Track document.

This document is a product of the Internet Engineering Task Force
(IETF). It represents the consensus of the IETF community. It has
received public review and has been approved for publication by the
Internet Engineering Steering Group (IESG). Further information on
Internet Standards is available in [Section 2 of RFC
5741](https://www.rfc-editor.org/rfc/rfc5741#section-2).

Information about the current status of this document, any errata and how to provide feedback on it may be obtained at [Information on RFC 7515 » RFC Editor](https://www.rfc-editor.org/info/rfc7515)

## JSON Web Signature (JWS)

> May 2015

### Table Of Contents

1. [Introduction](#introduction)
  1.1. [Notational Conventions](#notational-conventions)
2. [Terminology](#terminology)
3. [JSON Web Signature (JWS) Overview](#json-web-signature-jws-overview)
  3.1. [JWS Compact Serialization Overview](#jws-compact-serialization-overview)
  3.2. [JWS JSON Serialization Overview](#jws-json-serialization-overview)
  3.3. [Example JWS](#example-jws)
4. [JOSE Header](#jose-header)
  4.1. [Registered Header Parameter Names](#registered-header-parameter-names)
    4.1.1. ["alg" (Algorithm) Header Parameter](#alg-algorithm-header-parameter)
    4.1.2. ["jku" (JWK Set URL) Header Parameter](#jku-jwk-set-url-header-parameter)
    4.1.3. ["jwk" (JSON Web Key) Header Parameter](#jwk-json-web-key-header-parameter)
    4.1.4. ["kid" (Key ID) Header Parameter](#kid-key-id-header-parameter)
    4.1.5. ["x5u" (X.509 URL) Header Parameter](#x5u-x-509-url-header-parameter)
    4.1.6. ["x5c" (X.509 Certificate Chain) Header Parameter](#x5c-x-509-certificate-chain-header-parameter)
    4.1.7. ["x5t" (X.509 Certificate SHA-1 Thumbprint) Header Parameter](#x5t-x-509-certificate-sha-1-thumbprint-header-parameter)
    4.1.8. ["x5t#S256" (X.509 Certificate SHA-256 Thumbprint) Header Parameter](#x5t-s256-x-509-certificate-sha-256-thumbprint-header-parameter)
    4.1.9. ["typ" (Type) Header Parameter](#typ-type-header-parameter)
    4.1.10. ["cty" (Content Type) Header Parameter](#cty-content-type-header-parameter)
    4.1.11. ["crit" (Critical) Header Parameter](#crit-critical-header-parameter)
  4.2. [Public Header Parameter Names](#public-header-parameter-names)
  4.3. [Private Header Parameter Names](#private-header-parameter-names)
5. [Producing and Consuming JWSs](#producing-and-consuming-jwss)
  5.1. [Message Signature or MAC Computation](#message-signature-or-mac-computation)
  5.2. [Message Signature or MAC Validation](#message-signature-or-mac-validation)
  5.3. [String Comparison Rules](#string-comparison-rules)
6. [Key Identification](#key-identification)
7. [Serializations](#serializations)
  7.1. [JWS Compact Serialization](#jws-compact-serialization)
  7.2. [JWS JSON Serialization](#jws-json-serialization)
    7.2.1. [General JWS JSON Serialization Syntax](#general-jws-json-serialization-syntax)
    7.2.2. [Flattened JWS JSON Serialization Syntax](#flattened-jws-json-serialization-syntax)
8. [TLS Requirments](#tls-requirments)
9. [IANA Considerations](#iana-considerations)
  9.1. [JSON Web Signature and Encryption Header Parameters Registry](#json-web-signature-and-encryption-header-parameters-registry)
    9.1.1. [Registration Template](#registration-template)
    9.1.2. [Initial Registry Contents](#initial-registry-contents)
  9.2. [Media Type Registration](#media-type-registration)
    9.2.1. [Registry Contents](#registry-contents)
10. [Security Considerations](#security-considerations)
  10.1. [Key Entropy and Random Values](#key-entropy-and-random-values)
  10.2. [Key Protection](#key-protection)
  10.3. [Key Origin Authentication](#key-origin-authentication)
  10.4. [Cryptographic Agility](#cryptographic-agility)
  10.5. [Differences between Digital Signatures and MACs](#differences-between-digital-signatures-and-macs)
  10.6. [Algorithm Validation](#algorithm-validation)
  10.7. [Algorithm Protection](#algorithm-protection)
  10.8. [Chosen Plaintext Attacks](#chosen-plaintext-attacks)
  10.9. [Timing Attacks](#timing-attacks)
  10.10. [Replay Protection](#replay-protection)
  10.11. [SHA-1 Certificate Thumbprints](#sha-1-certificate-thumbprints)
  10.12. [JSON Security Considerations](#json-security-considerations)
  10.13. [Unicode Comparison Security Considerations](#unicode-comparison-security-considerations)
11. [References](#references)
  11.1. [Normative References](#normative-references)
  11.2. [Informative References](#informative-references)
Appendix A. [JWS Examples](#jws-examples)
  A.1. [Example JWS Using HMAC SHA-256](#example-jws-using-hmac-sha-256)
    A.1.1. Encoding
    A.1.2. Validating
  A.2. [Example JWS Using RSASSA-PKCS1-v1_5 SHA-256](#example-jws-using-rsassa-pkcs1-v1-5-sha-256)
    A.2.1. Encoding
    A.2.2. Validating
  A.3. [Example JWS Using ECDSA P-256 SHA-256](#example-jws-using-ecdsa-p-256-sha-256)
    A.3.1. Encoding
    A.3.2. Validating
  A.4. [Example JWS Using ECDSA P-521 SHA-512](#example-jws-using-ecdsa-p-521-sha-512)
    A.4.1. Encoding
    A.4.2. Validating
  A.5. [Example Unsecured JWS](#example-unsecured-jws)
  A.6. [Example JWS Using General JWS JSON Serialization](#example-jws-using-general-jws-json-serialization)
    A.6.1. [JWS Per-Signature Protected Headers](#jws-per-signature-protected-headers)
    A.6.2. [JWS Per-Signature Unprotected Headers](#jws-per-signature-unprotected-headers)
    A.6.3. [Complete JOSE Header Values](#complete-jose-header-values)
    A.6.4 [Complete JWS JSON Serialization Representation](#complete-jws-json-serialization-representation)
  A.7. [Example JWS Using Flattened JWS JSON Serialization](#example-jws-using-flattened-jws-json-serialization)
Appendix B. ["x5c" (X.509 Certificate Chain) Example](#x5c-x-509-certificate-chain-example)
Appendix C. [Notes on Implementing base64url Encoding without Padding](#notes-on-implementing-base64url-encoding-without-padding)
Appendix D. [Notes on Key Selection](#notes-on-key-selection)
Appendix E. [Negative Test Case for "crit" Header Parameter](#negative-test-case-for-crit-header-parameter)
Appendix F. [Detached Content](#detached-content)
[Acknowledgements](#acknowledgements)
[Authors' Addresses](#authors-addresses)

## Introduction

JSON Web Signature (JWS) represents content secured wit digital Signatures or Message Authentication Codes (MACs) using JSON-based [RFC7159](https://www.rfc-editor.org/rfc/rfc7159) data structures. The JWS cryptographic mechanisms provide integrity protection for an arbitrary sequence of octets. See [Section 10.5](https://www.rfc-editor.org/rfc/rfc7515#section-10.5) for a discussion of the differences between digital signatures and MACs.

Two closely related serializations for JWSs are defined. The JWS Compact Serialization is a compact, URL-safe representation intended for space-constrained environments such as HTTP Authorization headers and URI query parameters. The JWS JSON Serialization represents JWSs and JSON objects and enables multiple signatures and/or MACs to be applied to the same content. Both share and same cryptographic underpinnings.

Cryptographic algorithms and identifiers for use with this specification are described in the separate JSON Web Algorithms (JWA) [JWA](https://www.rfc-editor.org/rfc/rfc7515#ref-JWA) specification and an IANA registy defined by that specification. Related encryption capabilities are described in the separate JSON Web Encryption (JWE) [JWE](https://www.rfc-editor.org/rfc/rfc7515#ref-JWE) specification.

Names defined by this specification are short because a core goal is for the resulting representations to be compact.

### Notational Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in "Key words for use in RFCs to Indicate Requirement Levels" [RFC2119](https://www.rfc-editor.org/rfc/rfc2119). The interpretation should only be applied when the terms appear in all capital letters.

BASE64URL(OCTETS) denotes the base64url encoding of OCTETS, per [Section 2](https://www.rfc-editor.org/rfc/rfc7515#section-2).

UTF8(STRING) denotes the octets of the UTF-8 [RFC3629](https://www.rfc-editor.org/rfc/rfc3629) representation of STRING, where STRING is a sequence of zero or more Unicode [UNICODE](https://www.rfc-editor.org/rfc/rfc7515#ref-UNICODE) characters.

ASCII(STRING) denotes the octets of the ASCII [RFC20](https://www.rfc-editor.org/rfc/rfc20) representation of STRING, where STRING is a sequence of zero of more ASCII characters.

The concatenation of two values A and B is denoted as A || B.

## Terminology

These terms are defined by this specification:

* `JSON Web Signature (JWS)`: A data structure representing a digitally signed or MACed message.
* `JOSE Header`: JSON object containing the parametrs describing the cryptographic operations and parameters employed. The JOSE (JSON Object Signing and Encryption) Header is comprised of a set of Header Parameters.
* `JWS Payload`: The sequence of octets to be secured -- a.k.a. the message. The payload can contain an arbitrary sequence of octets.
* `JWS Signature`: Digital signature of MAC over the JWS Protected Header and the JWS Payload.
* `Header Parameter`: A name/value pair that is member of the JOSE Header.
* `JWS Protected Header`: JSON object that contains the Header Parameters that are integrity protected by the JWS Signature digital signature or MAC operation. For the JWS Compact Serialization, this comprises the entire JOSE Header. For the JWS JSON Serialization, this is one component of the JOSE Header.
* `Base64url Encoding`: Base64 encoding using the URL - and filename-safe character set defined in [Section 5 of RFC 4648](https://www.rfc-editor.org/rfc/rfc4648#section-5) [RFC4648](https://www.rfc-editor.org/rfc/rfc4648), with all trailing '=' characters omitted (as permitted by [Section 3.2](https://www.rfc-editor.org/rfc/rfc7515#section-3.2)) and without the inclusion of any line breaks, white space, or other additional characters. Note that the base64url encoding of the empty octet sequence its the empty string. (See `Appendix C` for notes on implementing base64url encoding without padding.)
* `JWS Signing Input`: The input to the digital signature or MAC computation. Its value is ASCII(BASE64URL(UTF8(JWS Protected Header)) || '.' || BASE64URL(JWS Payload)).
* `JWS Compact Serialization`: A representation of the JWS and a compact, URL-safe string.
* `JWS JSON Serialization`: A representation of the JWS as a JSON object.



































