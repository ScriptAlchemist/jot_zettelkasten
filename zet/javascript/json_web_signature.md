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

* [Introduction](#introduction)
  * [Notational Conventions](#notational-conventions)
* [Terminology](#terminology)
* [JSON Web Signature (JWS) Overview](#json-web-signature-jws-overview)
  * [JWS Compact Serialization Overview](#jws-compact-serialization-overview)
  * [JWS JSON Serialization Overview](#jws-json-serialization-overview)
  * [Example JWS](#example-jws)
* [JOSE Header](#jose-header)
  * [Registered Header Parameter Names](#registered-header-parameter-names)
    * ["alg" (Algorithm) Header Parameter](#alg-algorithm-header-parameter)
    * ["jku" (JWK Set URL) Header Parameter](#jku-jwk-set-url-header-parameter)
    * ["jwk" (JSON Web Key) Header Parameter](#jwk-json-web-key-header-parameter)
    * ["kid" (Key ID) Header Parameter](#kid-key-id-header-parameter)
    * ["x5u" (X.509 URL) Header Parameter](#x5u-x-509-url-header-parameter)
    * ["x5c" (X.509 Certificate Chain) Header Parameter](#x5c-x-509-certificate-chain-header-parameter)
    * ["x5t" (X.509 Certificate SHA-1 Thumbprint) Header Parameter](#x5t-x-509-certificate-sha-1-thumbprint-header-parameter)
    * ["x5t#S256" (X.509 Certificate SHA-256 Thumbprint) Header Parameter](#x5t-s256-x-509-certificate-sha-256-thumbprint-header-parameter)
    * ["typ" (Type) Header Parameter](#typ-type-header-parameter)
    * ["cty" (Content Type) Header Parameter](#cty-content-type-header-parameter)
    * ["crit" (Critical) Header Parameter](#crit-critical-header-parameter)
  * [Public Header Parameter Names](#public-header-parameter-names)
  * [Private Header Parameter Names](#private-header-parameter-names)
* [Producing and Consuming JWSs](#producing-and-consuming-jwss)
  * [Message Signature or MAC Computation](#message-signature-or-mac-computation)
  * [Message Signature or MAC Validation](#message-signature-or-mac-validation)
  * [String Comparison Rules](#string-comparison-rules)
* [Key Identification](#key-identification)
* [Serializations](#serializations)
  * [JWS Compact Serialization](#jws-compact-serialization)
  * [JWS JSON Serialization](#jws-json-serialization)
    * [General JWS JSON Serialization Syntax](#general-jws-json-serialization-syntax)
    * [Flattened JWS JSON Serialization Syntax](#flattened-jws-json-serialization-syntax)
* [TLS Requirments](#tls-requirments)
* [IANA Considerations](#iana-considerations)
  * [JSON Web Signature and Encryption Header Parameters Registry](#json-web-signature-and-encryption-header-parameters-registry)
    * [Registration Template](#registration-template)
    * [Initial Registry Contents](#initial-registry-contents)
  * [Media Type Registration](#media-type-registration)
    * [Registry Contents](#registry-contents)
* [Security Considerations](#security-considerations)
  * [Key Entropy and Random Values](#key-entropy-and-random-values)
  * [Key Protection](#key-protection)
  * [Key Origin Authentication](#key-origin-authentication)
  * [Cryptographic Agility](#cryptographic-agility)
  * [Differences between Digital Signatures and MACs](#differences-between-digital-signatures-and-macs)
  * [Algorithm Validation](#algorithm-validation)
  * [Algorithm Protection](#algorithm-protection)
  * [Chosen Plaintext Attacks](#chosen-plaintext-attacks)
  * [Timing Attacks](#timing-attacks)
  * [Replay Protection](#replay-protection)
  * [SHA-1 Certificate Thumbprints](#sha-1-certificate-thumbprints)
  * [JSON Security Considerations](#json-security-considerations)
  * [Unicode Comparison Security Considerations](#unicode-comparison-security-considerations)
* [References](#references)
  * [Normative References](#normative-references)
  * [Informative References](#informative-references)
* Appendix A. [JWS Examples](#jws-examples)
  * [Example JWS Using HMAC SHA-256](#example-jws-using-hmac-sha-256)
    * Encoding
    * Validating
  * [Example JWS Using RSASSA-PKCS1-v1_5 SHA-256](#example-jws-using-rsassa-pkcs1-v1-5-sha-256)
    * Encoding
    * Validating
  * [Example JWS Using ECDSA P-256 SHA-256](#example-jws-using-ecdsa-p-256-sha-256)
    * Encoding
    * Validating
  * [Example JWS Using ECDSA P-521 SHA-512](#example-jws-using-ecdsa-p-521-sha-512)
    * Encoding
    * Validating
  * [Example Unsecured JWS](#example-unsecured-jws)
  * [Example JWS Using General JWS JSON Serialization](#example-jws-using-general-jws-json-serialization)
    * [JWS Per-Signature Protected Headers](#jws-per-signature-protected-headers)
    * [JWS Per-Signature Unprotected Headers](#jws-per-signature-unprotected-headers)
    * [Complete JOSE Header Values](#complete-jose-header-values)
    * [Complete JWS JSON Serialization Representation](#complete-jws-json-serialization-representation)
  * [Example JWS Using Flattened JWS JSON Serialization](#example-jws-using-flattened-jws-json-serialization)
* Appendix B. ["x5c" (X.509 Certificate Chain) Example](#x5c-x-509-certificate-chain-example)
* Appendix C. [Notes on Implementing base64url Encoding without Padding](#notes-on-implementing-base64url-encoding-without-padding)
* Appendix D. [Notes on Key Selection](#notes-on-key-selection)
* Appendix E. [Negative Test Case for "crit" Header Parameter](#negative-test-case-for-crit-header-parameter)
* Appendix F. [Detached Content](#detached-content)
* [Acknowledgements](#acknowledgements)
* [Authors' Addresses](#authors-addresses)

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
* `JWS JSON Serialization`: A representation of the JWS as a JSON
  object. Unlike the JWS Compact Serialization, the JWS JSON
  Serialization enables multiple digital signatures and/or MACs to be
  applied to the same content. This representation is neither optimized
  for compactness nor URL-safe.
* `Unsecured JWS`: A JWS that provides not integrity protection.
  Unsecured JWSs use  the "alg" value "none".
* `Collision-Resistant Name`: A name in a namespace that enables names
  to be allocated in a manner such that they are highly unlikely to
  collide with other names. Examples of collision-resistant namespaces
  include: Domain Names, Object Identifiers (OIDs) as defined in the
  ITU-T X.660 and X.670 Recommendation series, and Universally Unique
  Identifiers (UUIDs) [RFC4122](https://www.rfc-editor.org/rfc/rfc4122).
  When using an administratively delegated namespace, the definer of a
  name needs to take reasonable precautions to ensure they are in
  control of the portion of the namespace they use to define the name.
* `StringOrURI`: A JSON string value, with the additional requirement that while arbitrary string values MAY be used, any value containing a ":" character MUST be a URI [RFC3986](https://www.rfc-editor.org/rfc/rfc3986). StringOrURI values are compared as case-sensitive strings with no transformations of canonicalizations applied.

The terms "JSON Web Encryption (JWE)", "JWE Compact Serialization", and "JWE JSON Serialization" are defined by the JWE specification [JWE](https://www.rfc-editor.org/rfc/rfc7515#ref-JWE).

The terms "Digital Signature" and "Message Authentication Code (MAC)" are defined by the "Internet Security Glossary, Version 2" [RFC4949](https://www.rfc-editor.org/rfc/rfc4949).

## JSON Web Signature (JWS) Overview

JWS represents digitally signed of MACed content using JSON date structures and base64url encoding. These JSON data structures MAY contain white space and/or line break before or after any JSON value or structural characters, in accordance with [Section 2 of RFC 7159](https://www.rfc-editor.org/rfc/rfc7159#section-2) [RFC7159](https://www.rfc-editor.org/rfc/rfc7159). A JWS represents these logical values (each of which is defined in [Section 2](https://www.rfc-editor.org/rfc/rfc7515#section-2)):

* JOSE Header
* JWS Payload
* JWS Signature

For a JWS, the JOSE Header members are the union of the members of these values( each of which is defined in [Section 2](https://www.rfc-editor.org/rfc/rfc7515#section-2)

* JWS Protected Header
* JWS Unprotected Header

This document defines two serializations for JWSs: a compact, URL-safe serialization called the JWS Compact Serialization and a JSON serialization called the JWS JSON Serialization. In both serializations, the JWS Protected Header, JWS Payload, and JWS Signature are base64url encoded, since JSON lacks a way to directly represent arbitrary octet sequences.

### JWS Compact Serialization Overview

In the JWS Compact Serialization, no JWS Unprotected Header is used. The this case, the JOSE Header and the JWS Protected Header are the same.

In the JWS Compact Serialization, a JWS is represented as the concatenation:

```
BASE64URL(UTF8(JWS Protected Header)) || '.' ||
BASE64URL(JWS Payload) || '.' ||
BASE64URL(JWS Signature)
```

### JWS JSON Serialization Overview

In the JWS JSON Serialization, one of both of the JWS Protected Header and JWS Unprotected Header MUST be present. In this case, the members of the JOSE Header are the union of the members of the JWS Protected Header and the JWS Unprotected Header values that are present.

In the JWS JSON Serialization, a JWS is represented as a JSON object containing some of all of these four members.

* `protected`, with the value `BASE64URL(UTF8(JWS Protected Header))`
* `header`, with the value `JWS Uprotected Header`
* `payload`, with the value `BASE64URL(JWS Payload)`
* `signature`, with the value `BASE64URL(JWS Signature)`

The three base64url-encoded result strings and the JWS Unprotected Header value are represented as members within a JSON object. The inclusion of some of these values is OPTIONAL. The JWS JSON Serialization can also represent multiple signature and/or MAC values, rather than just one.

### Example JWS

This section provides an example of a JWS. Its computation is described in more detail in Appendix A, including specifying the exact octet sequences representing the JSON values used and the key value used.

The following example JWS Protected Header declares that the encoded object is a JSON Web Token [JWT](https://www.rfc-editor.org/rfc/rfc7515#ref-JWT) and the JWS Protected Header and the JWS Payload are secured using the HMAC SHA-256 [RFC2104](https://www.rfc-editor.org/rfc/rfc2104) [SHS](https://www.rfc-editor.org/rfc/rfc7515#ref-SHS) algorithm:

```json
{"typ":"JWT",
 "alg":"HS256"}
```

Encoding this JWS Protected Header as `BASE64URL(UTF8(JWS Protected Header))` gives this value:

```
eyJ0eXAiOiJKV1QiLA0KICJhbGciOiJIUzI1NiJ9
```

The UTF-8 representation of the following JSON object is used as the JWS Payload. (Note that the payload can be any content and need not be representation of a JSON object.

```json
{"iss":"joe",
 "exp":1300819380,
 "http://example.com/is_root":true}
```

Encoding this JWS Payload as `BASE64URL(JWS Payload)` gives this value ( with line breaks for display purposes only):

```
eyJpc3MiOiJqb2UiLA0KICJleHAiOjEzMDA4MTkzODAsDQogImh0dHA6Ly9leGFt
cGxlLmNvbS9pc19yb290Ijp0cnVlfQ
```

Computing the HMAC of the JWS Signing Input `ASCII(BASE64URL(UTF8(JWS Protected Header)) || '.' || BASE64URL(JWS Payload))` with the HMAC SHA-256 algorithm using the key specified in Appendix A and base64url-encoding the result yields this `BASE64URL(JWS Signature)` value:

```
dBjftJeZ4CVP-mB92K27uhbUJU1p1r_wW1gFWFOEjXk
```

Concatenating these value in the order `Header.Payload.Signature` with period ('.') characters between the parts yields this complete JWS representation using the JWS Compact Serialization (with line breaks for display purposes only):

```
eyJ0eXAiOiJKV1QiLA0KICJhbGciOiJIUzI1NiJ9
.
eyJpc3MiOiJqb2UiLA0KICJleHAiOjEzMDA4MTkzODAsDQogImh0dHA6Ly9leGFt
cGxlLmNvbS9pc19yb290Ijp0cnVlfQ
.
dBjftJeZ4CVP-mB92K27uhbUJU1p1r_wW1gFWFOEjXk
```

See Appendix A for additional examples, including examples using the JWS JSON Serialization.

## JOSE Header

For a JWS, the members of the JSON object(s) representing the JOSE Header describe the digital signature or MAC applied to the JWS Protected Header and the JWS Payload and optionally additional properties of the JWS. The Header Parameter names within the JOSE Header MUST be unique; JWS parsers MUST either reject JWSs with duplicate Header Parameter names or use a JSON parser that returns only the lexically last duplicate member name, as specified in [Section 15.12](https://www.rfc-editor.org/rfc/rfc7515#section-15.12) ("The JSON Object") of ECMAScript 5.1 [ECMAScript](https://www.rfc-editor.org/rfc/rfc7515#ref-ECMAScript).

Implementations are required to understand the specific Header Parameters defined by this specification that are designated as "MUST be understood" and process them in the manner defined in this specification. All other Header Parameters defined by this specification that are not so designated MUST be ignored when not understood. Unless listed as a critical Header Parameter. All Header Parameters not defined by this specification MUST be ignored when not understood.

There are three classes of Header Parameter names: Registered Header Parameter names, Public Header Parameter names, and Private Header Parameter names.

### Registered Header Parameter Names

The following Header Parameter names for use in JWSs are registered in the IANA "JSON Web Signature and Encryption Header Parameters" registry established by [Section 9.1](https://www.rfc-editor.org/rfc/rfc7515#section-9.1), with meanings as defined in the subsections below.

As indicated by the common registry, JWSs and JWEs share a common Header Parameter space; when a parameter is used by both specifications, its usage must be compatible between the specifications.

### "alg" (Algorithm) Header Parameter

The "alg" (algorithm) Header Parameter identifies the cryptographic
algorithm used to secure the JWS. The JWS Signature value is not valid
if the "alg" value does not represent a supported algorithm or if there
is not a key for use with that algorithm associated with the party that
digitally signed of MACed the content. "alg" values should either be
registered in the IANA "JSON Web Signature and Encryption Algorithms"
registry established by
[JWA](https://www.rfc-editor.org/rfc/rfc7515#ref-JWA) or be a value that
contains a Collision-Resistant Name. The "alg" value is a case-sensitive
ASCII string containing a StringOrURI value. This Header Parameter MUST
be present and MUST be understood and processed by implementations.

A list of defined "alg" avlues for this use can be found in the IANA
"JSON Web Signature and Encryption Algorithms" registry established by
[JWA](https://www.rfc-editor.org/rfc/rfc7515#ref-JWA); the initial
contents of this registy are the values defined in Sections 3.1 of
[JWA](https://www.rfc-editor.org/rfc/rfc7515#ref-JWA).

### "jku" (JWK Set URL) Header Parameter

The "jku" (JWK Set URL) Header Parameter is a URI
[RFC3986](https://www.rfc-editor.org/rfc/rfc3986) that refers to a
resource for a set of JSON-encoded public Keys, one of which corresponds
to the key used to digitally sign the JWS. The keys MUST be encoded as a
JWK Set [JWK](https://www.rfc-editor.org/rfc/rfc7515#ref-JWK). The
protocol used to acquire the resource MUST provide integrity
protection; an HTTP GET request to retrieve the JWK Set MUST use
Transport Layer Security (TLS)
[RFC2818](https://www.rfc-editor.org/rfc/rfc2818)
[RFC5246](https://www.rfc-editor.org/rfc/rfc5246); and the identity of
the server MUST be validated, as per [Section 6 of RFC
6125](https://www.rfc-editor.org/rfc/rfc6125#section-6)
[RFC6125](https://www.rfc-editor.org/rfc/rfc6125). Also, see [Section
8](https://www.rfc-editor.org/rfc/rfc7515#section-8) on TLS
requirements. Use of this Header Parameter is OPTIONAL.

### "jwk" (JSON Web Key) Header Parameter

The "jwk" (JSON Web Key) Header Parameter is the public key that
corresponds to the key used to digitally sign the JWS. This key is
represented as a JSON Web Key
[JWK](https://www.rfc-editor.org/rfc/rfc7515#ref-JWK). Use of this
Header Parameter is OPTIONAL.

### "kid" (Key ID) Header Parameter

The "kid" (key ID) Header Parameter is a hint indication which key was used to secure the JWS. 























