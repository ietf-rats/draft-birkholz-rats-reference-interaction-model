---
title: Reference Interaction Models for Remote Attestation Procedures
abbrev: REIM
docname: draft-birkholz-rats-reference-interaction-model-latest
wg: RATS Working Group
stand_alone: true
ipr: trust200902
area: Security
kw: Internet-Draft
cat: std
pi:
  toc: yes
  sortrefs: yes
  symrefs: yes

author:
- ins: H. Birkholz
  name: Henk Birkholz
  org: Fraunhofer SIT
  abbrev: Fraunhofer SIT
  email: henk.birkholz@sit.fraunhofer.de
  street: Rheinstrasse 75
  code: '64295'
  city: Darmstadt
  country: Germany
- ins: M. Eckel
  name: Michael Eckel
  org: Fraunhofer SIT
  abbrev: Fraunhofer SIT
  email: michael.eckel@sit.fraunhofer.de
  street: Rheinstrasse 75
  code: '64295'
  city: Darmstadt
  country: Germany

normative:
  RFC2119:
  RFC8174:
  BCP205: RFC7942
  RFC8610:

informative:
  I-D.birkholz-rats-architecture: RATS

--- abstract

This document defines interaction models for basic remote attestation procedures.
Different methods of conveying attestation evidence securely are defined and illustrated.
Analogously, the required information elements used by conveyance protocols are defined and illustrated.

--- middle

# Introduction

Remote ATtestation procedureS {{-RATS}} are a workflows composed of roles and interactions, in which a Verifier creates assessments based on evidence about the trustworthiness of an Attester's system component characteristics.
The RATS Roles *Attester* and *Verifier*, as well as the RATS Message *Evidence* are terms defined by the RATS Architecture.
The goal of this document is to enable the design and adoption of secure conveyance methods for attestation evidence from an Attester to a Verifier.  
This document defines three reference interaction models that describe the conveyance of evidence between Attester and Verifier in order to provide the basis for reliable and believable appraisal of evidence by a Verifier.

## Requirements notation

{::boilerplate bcp14}

# Disambiguation

The term "Remote Attestation" is a common expression and often associated with certain properties. The term "Remote" in this context does not necessarily refer to a remote principal in the scope of network topologies or the Internet.
It rather refers to a decoupled system or different Computing Environments {{-RATS}}, which also can be present locally as system components of a composite device (a single RATS Principal).
Examples include: a Trusted Execution Environment (TEE), Baseboard Management Controllers (BMCs), as well as other physical or logical protected/isolated/shielded Computing Environments.

# Scope

This document focuses on generic interaction models between Verifiers and Attesters.
Complementary procedures, duties and functions that are required for a complete semantic binding of RATS are not in scope.
Examples include: identity establishment, key distribution and enrollment, as well as certificate revocation.

Furthermore, any processes and duties that go beyond carrying out remote attestation procedures are out-of-scope.
For instance, using the result of a remote attestation that is emitted by the Verifier, such as triggering remediation actions or recovery processes, as well as the remediation actions and recovery processes themselves, are out-of-scope.

# Component Roles

The Reference Interaction Models for RATS are based on the standard RATS Roles defined in {{-RATS}}.
Relevant portions of the RATS Role definitions including additional terminology defined by the RATS Architecture are illustrated below:

Attester:

: An Attestation Function residing in an Attesting Computing Environment that creates Evidence about Attested Computing Environments by collecting, formatting and protecting (e.g., signing) Claims about them.
It presents Evidence to a Verifier using a conveyance mechanism or protocol.
An Attester includes at least one Attesting Computing Environment and one Attested Computing Environment.

Verifier:

: An Attestation Function that accepts Evidence from an Attester using a conveyance mechanism or protocol.
It verifies the protection mechanisms, parses and appraises Evidence according to good-known valid (or known-invalid) Claims and Endorsements.

# Normative Prerequisites

Attester Identity:

: The provenance of Attestation Evidence with respect to a distinguishable Attesting Computing Environment MUST be correct and unambiguous.

: An Attester Identity MAY be a unique identity, or it MAY be included in a zero-knowledge proof (ZKP), or it MAY be part of a group signature.

Attestation Evidence:

: Attestation Evidence MUST be a set of well-formatted and well-protected Claims that an Attester can create and convey to a Verifier.

Attestation Evidence Authenticity:

: Attestation Evidence MUST be correct and authentic.

: Attestation Evidence, in order to provide proof of authenticity, SHOULD be cryptographically associated with an identity document (e.g. an X.509 certificate), or SHOULD include a correct and unambiguous reference to an accessible identity document.

Authentication Secret:

: An Authentication Secret MUST be available exclusively to an Attester's Attesting Computing Environment.
The Attester MUST sign Claims with that Authentication Secret, thereby proving the authenticity of the Claims included in the signed Attestation Evidence.
The Authentication Secret MUST be established before RATS can take place. How it is established is out-of-scope for this document.

# Remote Attestation Interaction Model

This section defines the information elements that have to be conveyed via a protocol, enabling the conveyance of Evidence between Verifier and Attester, as well as the interaction model for a generic challenge-response remote attestation scheme.

## Information Elements

Attester Identity ('attesterIdentity'):

: *mandatory*

: A statement about a distinguishable Attester made by an entity without accompanying evidence of its validity, used as proof of identity.

Authentication Secret ID ('authSecID'):

: *mandatory*

: An identifier that MUST be associated with the Authentication Secret which is used to sign evidence.

Nonce ('nonce'):

: *mandatory*

: The Nonce (number used once) is intended to be unique and practically infeasible to guess. In this reference interaction model the Nonce MUST be provided by the Verifier and MUST be used as proof of freshness. With respect to conveyed evidence, it ensures the result of an attestation activity to be created recently, e. g. sent or derived by the challenge from the Verifier. As such, the Nonce MUST be part of the signed Attestation Evidence that is sent from the Attester to the Verifier.

Assertions ('assertions'):

: *mandatory*

: Assertions represent characteristics of an Attester. They are required for proving the integrity of an Attester. Examples are assertions about sensor data, policies that are active on the system entity, versions of composite firmware of a platform, running software, routing tables, or information about a local time source.

Reference Assertions ('refAssertions')

: *mandatory*

: Reference Assertions are used to verify the assertions received from an Attester in an attestation verification process. For example, Reference Assertions MAY be Reference Integrity Measurements (RIMs) or assertions that are implicitly trusted because they are signed by a trusted authority. RIMs represent (trusted) assertions about the intended platform operational state of the Attester.

Assertion Selection ('assertionSelection'):

: *optional*

: An Attester MAY provide a selection of assertions in order to reduce or increase retrieved assertions to those that are relevant to the conducted appraisal. Usually, all available assertions that are available to the Attester SHOULD be conveyed. The Assertion Selection MAY be composed as complementary signed assertions or MAY be encapsulated assertions in the signed Attestation Evidence. An Attester MAY decide whether or not to provide all requested assertions or not. An example for an Assertion Selection is a Verifier requesting (signed) RIMs from an Attester.

(Signed) Attestation Evidence ('signedAttestationEvidence'):

: *mandatory*

: Attestation Evidence consists of the Authentication Secret ID that identifies an Authentication Secret, the Attester Identity, the Assertions, and the Verifier-provided Nonce. Attestation Evidence MUST cryptographically bind all of those elements. The Attestation Evidence MUST be signed by the Authentication Secret. The Authentication Secret MUST be trusted by the Verifier as authoritative.

Attestation Result ('attestationResult'):

: *mandatory*

: An Attestation Result is produced by the Verifier as a result of a Verification of Attestation Evidence. The Attestation Result represents assertions about integrity and other characteristics of the corresponding Attester.

## Interaction Model

The following sequence diagram illustrates the reference remote attestation procedure defined by this document.

~~~~
[Attester]                                                      [Verifier]
    |                                                               |
    | <--- requestAttestation(nonce, authSecID, assertionSelection) |
    |                                                               |
collectAssertions(assertionSelection)                               |
    | => assertions                                                     |
    |                                                               |
signAttestationEvidence(authSecID, assertions, nonce)               |
    | => signedAttestationEvidence                                  |
    |                                                               |
    | signedAttestationEvidence ----------------------------------> |
    |                                                               |
    | verifyAttestationEvidence(signedAttestationEvidence, refAssertions)
    |                                          attestationResult <= |
    |                                                               |

~~~~

The remote attestation procedure is initiated by the Verifier, sending an attestation request to the Attester. The attestation request consists of a Nonce, a Authentication Secret ID, and an Assertion Selection. The Nonce guarantees attestation freshness. The Authentication Secret ID selects the secret with which the Attester is requested to sign the Attestation Evidence. The Assertions Selection narrows down or increases the amount of received Assertions, if required. If the Assertions Selection is empty, then by default all assertions that are available on the system of the Attester SHOULD be signed and returned as Attestation Evidence. For example, a Verifier may only be interested in particular information about the Attester, such as proof of with which BIOS and firmware it booted up, and not include information about all currently running software.

The Attester, after receiving the attestation request, collects the corresponding Assertions to compose the Attestation Evidence that the Verifier requested—or, in case the Verifier did not provide an Assertions Selection, the Attester collects all information that can be used as complementary Assertions in the scope of the semantics of the remote attestation procedure. After that, the Attester produces Attestation Evidence by signing the Attester Identity, the Assertions, and the Nonce with the Authentication Secret identified by the Authentication Secret ID. Then the Attester sends the signed Attestation Evidence back to the Verifier.

Important at this point is that Assertions, the Nonce as well as the Attester Identity information MUST be cryptographically bound to the signature of the Attestation Evidence. It is not required for them to be present in plain text, though. Cryptographic blinding MAY be used at this point. For further reference see [Security and Privacy Considerations](#security-and-privacy-considerations)

As soon as the Verifier receives the signed Attestation Evidence, it verifies the signature, the Attester Identity, the Nonce, and the Assertions. This process is application-specific and can be carried out by, e. g., comparing the Assertions to known (good), expected Reference Assertions, such as Reference Integrity Measurements (RIMs), or evaluating it in other ways. The final output of the Verifier is the Attestation Result. It constitutes an new assertion about properties and characteristics of the Attester, i. e. whether or not it is compliant to policies, or even can be "trusted".

# Further Context

Depending on the use cases to cover, there may be additional requirements. Some of them are mentioned in this section.

## Confidentiality

Confidentiality of exchanged attestation information may be desirable. This requirement usually is present when communication takes place over insecure channels, such as the public Internet. In such cases, TLS may be uses as a suitable communication protocol that preserves confidentiality. In private networks, such as carrier management networks, it must be evaluated whether or not the transport medium is considered confidential.

## Mutual Authentication

In particular use cases mutual authentication may be desirable in such a way that a Verifier also needs to prove its identity to the Attester, instead of only the Attester proving its identity to the Verifier.

## Hardware-Enforcement/Support

Depending on the requirements, hardware support for secure storage of cryptographic keys, crypto accelerators, or protected or isolated execution environments may be useful. Well-known technologies are Hardware Security Modules (HSM), Physically Unclonable Functions (PUFs), Shielded Secrets, and Trusted Executions Environments (TEEs).

# Security and Privacy Considerations

In a remote attestation process the Verifier or the Attester MAY want to cryptographically blind several attributes. For instance, information can be part of the signature after applying a one-way function (e. g. a hash function).

There is also a possibility to scramble the Nonce or Attester Identity with other information that is known to both the Verifier and Attester. A prominent example is the IP address of the Attester that usually is known by the Attester itself as well as the Verifier. This extra information can be used to scramble the Nonce in order to counter certain types of relay attacks.

# Acknowledgments

Very likely.

# Change Log

- Initial draft -00

- Changes from version 00 to version 01:
  - Added details to the flow diagram

- Changes from version 01 to version 02:
  - Integrated comments from Ned Smith (Intel)
  - Reorganized sections and
  - Updated interaction model

- Changes from version 02 to version 03:
  - Replaced "claims" with "assertions"
  - Added proof-of-concept CDDL for CBOR via CoAP based on a TPM 2.0 quote operation

--- back

# CDDL Specification for a simple CoAP Challenge/Response Interaction

The following CDDL specification is an examplary proof-of-concept to illustrate a potential implementation of the Reference Interaction Model. The transfer protocol used is CoAP using the FETCH operation. The actual resource operated on can be empty. Both the Challenge Message and the Response Message are exchanged via the FETCH Request and FETCH Response body.

In this example, the root-of-trust for reporting primitive operation "quote" is provided by a TPM 2.0.

~~~~ CDDL

RAIM-Bodies = CoAP-FETCH-Body / CoAP-FETCH-Response-Body

CoAP-FETCH-Body = [ hello: bool, ; if true, the AK-Cert is conveyed
                    nonce: bytes,
                    pcr-selection: [ + [ tcg-hash-alg-id: uint .size 2, ; TPM2_ALG_ID
                                         [ + pcr: uint .size 1 ],
                                       ]
                                   ],
                  ]

CoAP-FETCH-Response-Body = [ attestation-evidence: TPMS_ATTEST-quote,
                             tpm-native-signature: bytes,
                             ? ak-cert: bytes, ; attestation key certificate
                           ]

TPMS_ATTEST-quote = [ qualifiediSigner: uint .size 2, ;TPM2B_NAME
                      TPMS_CLOCK_INFO,
                      firmwareVersion: uint .size 8
                      quote-responses: [ * [ pcr: uint .size 1,
                                             + [ pcr-value: bytes,
                                                 ? hash-alg-id: uint .size 2,
                                               ],
                                           ],
                                         ? pcr-digest: bytes,
                                       ],
                    ]

TPMS_CLOCK_INFO = [ clock: uint .size 8,
                    resetCounter: uint .size 4,
                    restartCounter: uint .size 4,
                    save: bool,
                  ]

~~~~
