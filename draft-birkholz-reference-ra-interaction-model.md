---
title: Reference Interaction Model for Challenge-Response-based Remote Attestation
abbrev: RAIM
docname: draft-birkholz-reference-ra-interaction-model-latest
wg: TBD
stand_alone: true
ipr: trust200902
area: Security
kw: Internet-Draft
cat: info
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
  org: Huawei Technologies
  abbrev: Huawei
  email: michael.eckel@huawei.com
  street: Feldbergstrasse 78
  code: '64293'
  city: Darmstadt
  country: Germany

normative:
  RFC2119:

informative:
  I-D.birkholz-attestation-terminology: rats

--- abstract

This document defines an interaction model for a basic remote attestation procedure.
Additionally, the required information elements are illustrated.

--- middle

# Introduction

Remote attestation procedures (RATS) are a combination of activities, in which a Verifier creates assertions about claims of integrity and about the characteristics of other system entities by the appraisal of corresponding signed claim sets (evidence). In this document, a reference interaction model for a generic challenge-response-based remote attestation procedure is provided. The minimum set of components, roles and information elements that have to be conveyed between Verifier and Attestor are defined as a standard reference to derive more complex RATS from.

## Requirements notation

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and
"OPTIONAL" in this document are to be interpreted as described in RFC
2119, BCP 14 {{RFC2119}}.

# Disambiguation

The term "Remote Attestation" is a common expression and often associated with certain properties. The term "Remote" in this context does not necessarily refer to a remote system entity in the scope of network topologies or the Internet. It rather refers to a decoupled system or different computing context, which also could be present locally as components of a composite device. Examples include: a Trusted Execution Environment (TEE), Baseboard Management Controllers (BMCs), as well as other physical or logical protected/isolated execution environments.

# Scope

This document focuses on a generic interaction model between Verifiers and Attestors. Complementary processes, functions and activities that are required for a complete semantic binding of RATS are not in scope. Examples include: identity establishment, key enrollment, and revocation. Furthermore, any processes and activities that go beyond carrying out the remote attestation process are out of scope. For instance, using the result or claim of a remote attestation that is emitted by the Verifier, such as triggering remediation actions and recovery processes, as well as the remediation actions and recovery processes themselves, are out of scope.

# Component Roles

The Reference Interaction Model for Challenge-Response-based Remote Attestation is based on the standard roles defined in {{-rats}}:

Attestor:

: The role that designates the subject of the remote attestation. A system entity that is the provider of evidence takes on the role of an Attestor.

Verifier:

: The role that designates the system entity and that is the appraiser of evidence provided by the Attestor. A system entity that is the consumer of evidence takes on the role of a Verifier.

# Prerequisites

Identity:

: An Attestor must have a unique Identity in such a way that a Verifier can uniquely identify an Attestor. This Identity MUST be part of the signed claim set (attestation evidence) that the Attestor conveys to the Verifier.

Shared Secret:

: A Shared Secret between the Verifier and the Attestor. This secret MUST be established between the two entities before a remote attestation procedure can take place. How this Shared Secret is established is out of scope for this reference model.

# Remote Attestation Interaction Model

This section defines the information elements that have to be conveyed via a protocol, enabling the conveyance of evidence between Verifier and Attestor, as well as the interaction model for a generic challenge-response scheme.

## Information Elements

Nonce:

: mandatory

: The Nonce (number used once) is a number intended to be unique and intended to be effectively infeasible to guess. In this reference interaction model it MUST be provided by the Verifier and MUST be used as a proof of freshness, with respect to conveyed evidence ensuring that the result of an attestation activity was created recently (i.e. triggered by the challenge emitted by the Verifier). As such, the Nonce MUST be part of the signed claim set that composes the attestation evidence sent by the Attestor to the Verifier.

Shared Secret ID:

: mandatory

: An identifier that MUST be associated with the Shared Secret which is used to sign the attestation evidence claim set.

Attestation Evidence:

: mandatory

: The signed minimal claim set that is required to enable integrity proving of the corresponding characteristics of the Attestor. Examples of claims included in attestation evidence are claims about sensor data, policies that are active on the system entity, versions of a platform's composite firmware, running software, routing tables, or information about a local time source. Attestation evidence must be cryptographically bound to the Verifier-provided Nonce, the Identity of the Attestor, as well as to the Shared Secret identified by the Shared Secret ID.

Additional Info:

: optional

: An Attestor MAY provide additional information or metadata that are relevant to the appraisal conducted by the Verifier in order to prove correctness of the integrity claims created by the Attestor. Usually, all information associated with the signed claim set that is available to the Attestor SHOULD be conveyed. This additional information can be composed as complementary signed claim sets or can be encapsulated claim sets in the signed claim set that composes the evidence about integrity. This information element is optional in order to enable a Verifier to request additional information. An Attestor MAY decide whether or not to provide that additional information or not. In either case, all additional information MUST be cryptographically bound to the signed claim set. An example for additional information that a Verifier can request from an Attestor are (signed) Reference Integrity Measurements (RIMs).

Identity:

: mandatory

: A statement about a distinguishable Attestor made by an entity without accompanying evidence of its validity, used as proof of identity.

# Interaction Model

The following sequence diagram illustrates the reference remote attestation procedure defined by this document.

~~~~

  [Attestor]                                               [Verifier]
      |                                                         |
      | <------------ Nonce, Shared Secret ID, [Additonal Info] |
      |                                                         |
    Collect Integrity Claims                                    |
      |                                                         |
    Sign Claim Set (Evidence)                                   |
      |                                                         |
      | Evidence, Identity, Nonce, Signature -----------------> |
      |                                                         |
      |                                            Appraise Evidence
      |                                                         |

~~~~

The remote attestation procedure is initiated by the Verifier, sending an attestation request to the Attestor. The attestation request consists of a Nonce, a Shared Secret ID, and an optional Additional Info part. The Nonce guarantees attestation freshness. The Shared Secret ID selects the shared secret the Attestor is requested to sign its response with. The Additional Info part is optional in order to narrow down or increase the scope of received evidence, if required. For example, the Verifier is only interested in particular information about the Attestor, such as whether the device booted up in a known state, and not include information about all currently running software.

The Attestor, after receiving the attestation request, collects the corresponding integrity claims to compose the evidence the Verifier requested—or, in case the Verifier did not include any Additional Info, the Attestor collects all information that can be used as complementary claims in the scope of the semantics of the remote attestation procedure. After that, the Attestor signs the Evidence with the Shared Secret including the Nonce and the Identity information, and sends the output back to the Verifier. Important at this point is that the Nonce as well as the Identity information must be cryptographically bound to the signature, i.e. it is not required for them to be present in plain-text. For instance, those information can be part of the signature after a one-way function (e.g. a hash function) was applied to them. There is also a possibility to scramble the Nonce or Identity with other information that is known to both the Verifier and Attestor. A prominent example is the IP address of the Attestor that usually is known by the Attestor as well as the Verifier. This extra information can be used to scramble the Nonce in order to counter a certain type of relay attacks. As soon as the Verifier receives the attestation evidence data, it appraises the signed evidence, the Identity as well as the claims in the claim set. This process is application-specific and can be done by e.g. comparing the claim set to known (good), expected reference claims, such as a Reference Integrity Measurement Manifest (RIMs), or evaluating it in other ways. The final output, also referred to as Attestation Result, is a new claim about properties of the Attestor, i.e. whether or not it is compliant to the policies, or even "trusted".

# Further Context

Depending on the use cases to cover there may be additional requirements.

## Confidentiality

Use confidential communication to exchange attestation information. This requirement usually is present when communication happens over insecure channels, such as the public Internet. Speaking of a suitable communication protocol, TLS is a good candidate. In private networks, such as carrier management networks, it must be evaluated whether or not the transport medium is considered confidential.

## Mutual Authentication

In particular use cases mutual authentication may be desirable in such a way that a Verifier also needs to prove its identity to the Attestor instead of only the Attestor proving its identity to the Verifier.

## Hardware-Enforcement/Support

In particular use cases hardware support can be desirable. Depending on the requirements those can be secure storage of cryptographic keys, crypto accelerators, or protected or isolated execution environments. Well-known technologies are Hardware Security Modules (HSM), Physical Unclonable Functions (PUFs), Shielded Secrets, Trusted Executions Environments (TEEs), etc.

#  Security Considerations

There are always some.

#  Acknowledgements

Very likely.

#  Change Log

Initial draft -00

--- back
