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
  RFC7252: COAP
  RFC7049: CBOR
  RFC8174:
  BCP205: RFC7942
  RFC8610:

informative:
  I-D.ietf-rats-architecture: RATS

--- abstract

This document describes interaction models for remote attestation procedures (RATS).
Three conveying mechanisms -- Challenge/Response, Uni-Directional, and Attestation Telemetry -- are illustrated and defined.
Analogously, a general overview about the information elements typically used by corresponding conveyance protocols are highlighted. Privacy preserving conveyance of Evidence via Direct Anonymous Attestation is elaborated on for each interaction model, individually.

--- middle

# Introduction

Remote ATtestation procedureS (RATS, {{-RATS}}) are workflows composed of roles and interactions, in which Verifiers create Attestation Results about the trustworthiness of an Attester's system component characteristics. The Verifier's assessment in the form of Attestation Results is created based on Attestation Policies and Evidence -- trustable and temper-evident Claims Sets about an Attester's system component characteristics -- created by an Attester.
The roles *Attester* and *Verifier*, as well as the Conceptual Messages *Evidence* and *Attestation Results* are terms defined by the RATS Architecture {{-RATS}}.
This documents captures interaction models that can be used in specific RATS-related solution documents. The primary focus of this document is the conveyance of attestation Evidence. Specific goals of this document re to:

* prevent inconsistencies in descriptions of these interaction models in other documents (due to text cloning over time),
* enable to highlight an exact delta/divergence between the core set of characteristics captured here in this document and variants of these interaction models used in other specifications or solutions, and to
* illustrate the application of Direct Anonymous Attestation (DAA) for each of the interaction models described.

In summary, this document enables the specification and design of trustworthy and privacy preserving conveyance methods for attestation Evidence from an Attester to a Verifier. While the conveyance of other Conceptual Messages is out-of-scope [note: may still change] the methods described can also be applied to the conveyance of Endorsements or Attestation Results.

# Terminology

This document uses the terms, roles, and concepts defined in {{-RATS}}.

{::boilerplate bcp14}

# Disambiguation

The term "Remote Attestation" is a common expression and often associated with certain properties. The term "Remote" in this context does not necessarily refer to a remote entity in the scope of network topologies or the Internet.
It rather refers to a decoupled system or entities that exchange the payload of the Conceptual Message type called Evidence {{-RATS}}. This conveyance can also be "local", if the Verifier is part of the same entity as the Attester, e.g., separate system components of a Composite Device (a single RATS Entity).
Examples of these types of co-located environments include: a Trusted Execution Environment (TEE), Baseboard Management Controllers (BMCs), as well as other physical or logical protected/isolated/shielded Computing Environments (e.g. embedded Secure Elements (eSE) or Trusted Platform Modules (TPM)).

# Scope

This document focuses on generic interaction models between Attesters and Verifiers.
Complementary procedures, functions, or services that are required for a complete semantic binding of the concepts defined in {{-RATS}} are not in scope.
Examples include: identity establishment, key distribution and enrollment, as well as certificate revocation.

Furthermore, any processes and duties that go beyond carrying out remote attestation procedures are out-of-scope.
For instance, using the results of a remote attestation that are created by the Verifier, e.g., triggering remediation actions or recovery processes, as well as the remediation actions and recovery processes themselves, are also out-of-scope.

# Normative Prerequisites

In order to ensure an appropriate conveyance of Evidence, the following set of prerequisites must be in place to support the implementation of interaction models:

Attester Identity:

: The provenance of Evidence with respect to a distinguishable Attesting Environment MUST be correct and unambiguous.

: An Attester Identity MAY be a unique identity, it MAY be included in a zero-knowledge proof (ZKP), or it MAY be part of a group signature.

Attestation Evidence:

: Evidence MUST be a set of well-formatted and well-protected Claims that an Attester can create and convey to a Verifier in a temper-evident manner.

: Evidence MUST include an indicator about its Freshness that can be understood by a Verifier.

Attestation Evidence Authenticity:

: Attestation Evidence MUST be correct and authentic.

: Attestation Evidence, in order to provide proof of authenticity, SHOULD be cryptographically associated with an identity document (e.g. an X.509 certificate or trusted key material), or SHOULD include a correct and unambiguous and stable reference to an accessible identity document.

Authentication Secret:

: An Authentication Secret MUST be available exclusively to an Attester's Attesting Environment.

: The Attester MUST protect Claims with that Authentication Secret, thereby proving the authenticity of the Claims included in Evidence.
The Authentication Secret MUST be established before RATS can take place.

# Generic Information Elements

This section defines the information elements that can be used in all interaction models.
Varying from solution to solution, generic information elements can be either included in the scope of protocol messages or can be included in their payload.
Ultimately, they have to be conveyed in order to enable an interaction model.

Attester Identity ('attesterIdentity'):

: *mandatory*

: A statement about a distinguishable Attester made by an Endorser without accompanying evidence of its validity - used as proof of identity.

Authentication Secret ID ('authSecID'):

: *mandatory*

: A statement representing an identifier that MUST be associated with an Authentication Secret that is used to protect Evidence.

Handle ('handle'):

: *mandatory*

: A statement that is intended to uniquely distinguish received Evidence and/or determine the Freshness of Evidence. A Verifier can also use a Handle as an indicator for authenticity or attestation provenance, as only Attesters and Verifiers that are intended to exchange Evidence should have knowledge of the corresponding Handles. Examples include Nonces or signed timestamps.

Claims ('claims'):

: *mandatory*

: Claims are assertions that represent characteristics of an Attester. Claims compose attestation Evidence and are, for example, used to appraise the integrity of an Attester by a Verifier. The statements that compose the information elements in this section can also be expressed as Claims in Evidence by some solutions.

Reference Claims ('refClaims')

: *mandatory*

: Reference Claims are a specific subset of Appraisal Policies as defined in {{-RATS}}. Reference Claims are used to appraise the Claims received from an Attester via appraisal by direct comparison. For example, Reference Claims MAY be Reference Integrity Measurements (RIMs) or assertions that are implicitly trusted because they are signed by a trusted authority (see Endorsements in {{-RATS}}). Reference Claims typically represent (trusted) Claim sets about an Attester's intended platform operational state.

Claim Selection ('claimSelection'):

: *optional*

: A statement that represents a (sub-)set of Claims that can be created by an Attester. Claim Selections can act as filters that can specify the exact set of Claims to be included in Evidence. An Attester MAY decide whether or not to provide all Claims as requested via a Claim Selection.

Attestation Evidence ('signedAttestationEvidence'):

: *mandatory*

: Evidence consists of an Authentication Secret ID that identifies an Authentication Secret, the Attester Identity, Claims, and a Handle. Attestation Evidence MUST cryptographically bind all of these information elements. The Evidence MUST be protected via the Authentication Secret. The Authentication Secret MUST be trusted by the Verifier as authoritative.

Attestation Result ('attestationResult'):

: *mandatory*

: An Attestation Result is produced by the Verifier as the output of the appraisal of Evidence. Attestation Results include condensed assertions about integrity or other characteristics of the corresponding Attester.

# Interaction Models

The following subsections introduce and illustrate the interaction models:

1. Challenge/Response Remote Attestation
2. Uni-Directional Remote Attestation
3. Attestation Telemetry

Each section starts with a sequence diagram illustrating the interactions between Attester and Verifier. Other roles involved -- mainly Relying Parties and Endorsers -- are elaborated in the following expositional text, if applicable.

## Challenge/Response Remote Attestation

~~~~
.----------.                                                .----------.
| Attester |                                                | Verifier |
'----------'                                                '----------'
     |                                                            |
     |                                                            |
generateClaims(targetEnvironment)                                 |
     | => claims                                                  |
     |                                                            |  
     | <------ requestEvidence(handle, authSecID, claimSelection) |
     |                                                            |
collectClaims(claimSelection)                                     |
     | => collectedClaims                                         |
     |                                                            |
generateEvidence(authSecID, collectedClaims, handle)              |
     | => evidence                                                |
     |                                                            |
     | conveyEvidence ------------------------------------------> |
     |                                                            |
     |                             appraiseEvidence(evidence, refClaims)
     |                                       attestationResult <= |
     |                                                            |
~~~~

The remote attestation procedure is initiated by the Verifier, sending an attestation request to the Attester. The attestation request consists of a Nonce, a Authentication Secret ID, and a Claim Selection. The Nonce guarantees attestation freshness. The Authentication Secret ID selects the secret with which the Attester is requested to sign the Attestation Evidence. The Claim Selection narrows down or increases the amount of received Claims, if required. If the Claim Selection is empty, then by default all Claims that are available on the Attester MUST be signed and returned as Attestation Evidence. For example, a Verifier may only be requesting a particular subset of information about the Attester, such as evidence about BIOS and firmware the Attester booted up with - and not include information about all currently running software.

The Attester, after receiving the attestation request, collects the corresponding Claims that have been measured beforehand to compose the Attestation Evidence that the Verifier requested. In the case that the Verifier did not provide a Claim Selection, the Attester collects all information that can be used as complementary Claims in the scope of the semantics of the remote attestation procedure. Conclusively, the Attester creates Attestation Evidence by signing the Attester Identity, the Claims, and the Nonce with the Authentication Secret identified by the Authentication Secret ID. The signed Attestation Evidence is transferred back to the Verifier.

It is crucial at this point that Claims, the Nonce, as well as the Attester Identity information MUST be cryptographically bound to the signature of the Attestation Evidence. It is not required for them to be present in plain text, though. Cryptographic blinding MAY be used at this point. For further reference see section {{security-and-privacy-considerations}}.

As soon as the Verifier receives the signed Attestation Evidence, it verifies the signature, the Attester Identity, the Nonce, and appraises the Claims. This procedure is application-specific and can be carried out by comparing the Claims with corresponding Reference Claims, e.g., Reference Integrity Measurements (RIMs), or using other appraisal policies. The final output of the Verifier are Attestation Results. Attestation Results constitute new Claims about an Attester's properties and characteristics that enables relying parties, for example, to assess an Attester's trustworthiness.

## Uni-Directional Remote Attestation

~~~~
.----------.                                                .----------.
| Attester |                                                | Verifier |
'----------'                                                '----------'
     |                                                            |
valueGeneration(targetEnvironment)                                |
     | => Claims                                                  |
     |                                                            |
     |                   .--------------------.                   |
     | <----------handle | Handle Distributor | handle----------> |
     |                   '--------------------'                   |
     |                                                            |
generateEvidence(authSecID, claims, handle)                       |
     | => evidence                                                |
     |                                                            |
     | pushEventLog---------------------------------------------> |
     | pushEvidence---------------------------------------------> |
     |                                                            |
     |                   appraiseEvidence(evidence, eventLog, refClaims)
     |                                       attestationResult <= |
     ~                                                            ~
     |                                                            | 
valueGeneration(targetEnvironment)                                |
     | => ClaimsDelta                                             |
     |                                                            |
generateEvidence(authSecID, claimsDelta, handle                   |
     | => evidence                                                |
     |                                                            |
     | pushEventLogDelta----------------------------------------> |
     | pushEvidence---------------------------------------------> |
     |                                                            |
     |              appraiseEvidence(evidence, eventLogDelta, refClaims)
     |                                       attestationResult <= |
     |                                                            |
~~~~

## Attestation Telemetry

~~~~
.----------.                                                .----------.
| Attester |                                                | Verifier |
'----------'                                                '----------'
     |                                                            |
valueGeneration(targetEnvironment)                                |
     | => Claims                                                  |
     |                                                            |
     | <-----subscribeEvidence(handle, authSecID, claimSelection) |
     |                                                            |
generateEvidence(authSecID, claimSelection, handle)               |
     | => evidence                                                |
     |                                                            |
     | pushEventLog---------------------------------------------> |
     | pushEvidence---------------------------------------------> |
     |                                                            |
     |                   appraiseEvidence(evidence, eventLog, refClaims)
     |                                       attestationResult <= |
     ~                                                            ~
     |                                                            |
valueGeneration(targetEnvironment)                                |
     | => Claims                                                  |
     |                                                            |
generateEvidence(authSecID, claimSelection, handle)               |
     | => evidence                                                |
     |                                                            |
     | pushEventLogDelta----------------------------------------> |
     | pushEvidence---------------------------------------------> |
     |                                                            |
     |              appraiseEvidence(evidence, eventLogDelta, refClaims)
     |                                       attestationResult <= |
     |                                                            |
~~~~

# Further Context

Depending on the use cases covered, there can be additional requirements. An exemplary subset is illustrated in this section.

## Confidentiality

Confidentiality of exchanged attestation information may be desirable. This requirement usually is present when communication takes place over insecure channels, such as the public Internet. In such cases, TLS may be uses as a suitable communication protocol that preserves confidentiality. In private networks, such as carrier management networks, it must be evaluated whether or not the transport medium is considered confidential.

## Mutual Authentication

In particular use cases mutual authentication may be desirable in such a way that a Verifier also needs to prove its identity to the Attester, instead of only the Attester proving its identity to the Verifier.

## Hardware-Enforcement/Support

Depending on the requirements, hardware support for secure storage of cryptographic keys, crypto accelerators, or protected or isolated execution environments may be useful. Well-known technologies are roots of trusts, such as Hardware Security Modules (HSM), Physically Unclonable Functions (PUFs), Shielded Secrets, or Trusted Executions Environments (TEEs).

# Implementation Status

Note to RFC Editor: Please remove this section as well as references to {{BCP205}} before AUTH48.

This section records the status of known implementations of the protocol defined by this specification at the time of posting of this Internet-Draft, and is based on a proposal described in {{BCP205}}.
The description of implementations in this section is intended to assist the IETF in its decision processes in progressing drafts to RFCs. 
Please note that the listing of any individual implementation here does not imply endorsement by the IETF.
Furthermore, no effort has been spent to verify the information presented here that was supplied by IETF contributors.
This is not intended as, and must not be construed to be, a catalog of available implementations or their features.
Readers are advised to note that other implementations may exist.

According to {{BCP205}},
"this will allow reviewers and working groups to assign due consideration to documents that have the benefit of running code, which may serve as evidence of valuable experimentation and feedback that have made the implemented protocols more mature.
It is up to the individual working groups to use this information as they see fit".

## Implementer

The open-source implementation was initiated and is maintained by the Fraunhofer Institute for Secure Information Technology - SIT.

## Implementation Name

The open-source implementation is named "CHAllenge-Response based Remote Attestation" or in short: CHARRA.

## Implementation URL

The open-source implementation project resource can be located via: https://github.com/Fraunhofer-SIT/charra

## Maturity

The code's level of maturity is considered to be "prototype".

## Coverage and Version Compatibility

The current version (commit '847bcde') is aligned with the exemplary specification of the CoAP FETCH bodies defined in section {{coap-fetch-bodies}} of this document.

## License

The CHARRA project and all corresponding code and data maintained on github are provided under the BSD 3-Clause "New" or "Revised" license.

## Implementation Dependencies

The implementation requires the use of the official Trusted Computing Group (TCG) open-source Trusted Software Stack (TSS) for the Trusted Platform Module (TPM) 2.0. The corresponding code and data is also maintained on github and the project resources can be located via: https://github.com/tpm2-software/tpm2-tss/

The implementation uses the Constrained Application Protocol {{-COAP}} (http://coap.technology/) and the Concise Binary Object Representation {{-CBOR}} (https://cbor.io/).

## Contact

Michael Eckel (michael.eckel@sit.fraunhofer.de)

{: #security-and-privacy-considerations}
# Security and Privacy Considerations
In a remote attestation procedure the Verifier or the Attester MAY want to cryptographically blind several attributes. For instance, information can be part of the signature after applying a one-way function (e. g. a hash function).

There is also a possibility to scramble the Nonce or Attester Identity with other information that is known to both the Verifier and Attester. A prominent example is the IP address of the Attester that usually is known by the Attester itself as well as the Verifier. This extra information can be used to scramble the Nonce in order to counter certain types of relay attacks.

# Acknowledgments

Olaf Bergmann and Ned Smith

# Change Log

- Initial draft -00

- Changes from version 00 to version 01:
  - Added details to the flow diagram
  - Integrated comments from Ned Smith (Intel)
  - Reorganized sections and
  - Updated interaction model
  - Replaced "claims" with "assertions"
  - Added proof-of-concept CDDL for CBOR via CoAP based on a TPM 2.0 quote operation

- Changes from version 01 to version 02:
  - Revised the relabeling of "claims" with "assertion" in alignment with the RATS Architecture I-D.
  - Added Implementation Status section
  - Updated interaction model
  - Text revisions based on changes in {{-RATS}} and comments provided on rats@ietf.org.

--- back

{: #coap-fetch-bodies}
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
