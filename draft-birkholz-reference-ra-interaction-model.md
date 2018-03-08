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
#  RFC4949:

informative:
  I-D.birkholz-attestation-terminology: rats
#  I-D.ietf-sacm-terminology: sacmterm
#  RFC7519: jwt 

--- abstract 

This document is intended to illustrate and remediate the impedance mismatch of terms related to
remote attestation procedures used in different domains today. New terms defined by this document
provide a consolidated basis to support future work on attestation procedures in the IETF and
beyond.

--- middle

# Introduction

Remote attestation procedures (RATS) are a combination of activities, in which a Verifier creates assertions about claims of integrity about the characteristics of other system entities by the appraisal of corresponding signed claim sets (evidence). In this document, a reference interaction model for a generic challenge-response-based remote attestation procedure is provided. The minimum set of components, roles and information elements that have to be conveyed between Verifier and Attestor are defined as a standard reference to derive more complex RATS from.

## Requirements notation

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and
"OPTIONAL" in this document are to be interpreted as described in RFC
2119, BCP 14 {{RFC2119}}.

# Disambiguation

The term "Remote Attestation" is a common expression and often associated with certain properties. The term "Remote" in this context does not necessarily refer to a remote system entitiy in the scope of network topologies or the Internet. It rather refers to a decoupled system or different computing contexts, which also could be present localy as components of a composite device. Examples include: a Trusted Execution Environment (TEE), Baseboard Management Controllers (BMCs), as well as other phyiscal or logical protected execution environements.

# Scope

This document focusses on a generic interaction model between Verifiers and Attestors. Complementary processes, functions and activities that are required for a complete semantic binding of RATS are not in scope. Examples include: Identity establishment or key enrollment/revocation.

# Component Roles

The Reference Interaction Model for Challenge-Response-based Remote Attestation is based on the standard roles defined in {{-rats}}:

Attestor:

: The role that designates the subject of the remote attestation. A system entity that is the provider of evidence takes on the role of an Attestor.

Verifier:

: The role that designates the system entity that is the appraiser of the evidence provided by the Attestor. A system entity that is the consumer of evidence takes on the role of a Verifier.

# Prerequisites

Identity:

: An Attestor must have a unique Identity in such a way that a Verifier can uniquely identify an Attestor. This Identity MUST be part of the signed claim set (attestation evidence) that the Attestor conveys to the Verifier.

Shared Secret:

: A Shared Secret between the Verifier and the Attestor. This secret MUST be established between the two entities before a RA procedure can take place. How this shared secret is established is out of scope of this reference model.

# Remote Attestation Interaction Model

This section defines the information elements that have to be conveyed via a protocol enabling the conveyance of evidence between Verifier and Attestor, as well as the in the interaction model of a generic challange/response scheme.

## Information Elements

Nonce:

: mandatory

: The Nonce (number used once) is a number intended to be unique and intended to be effectivly infeasible to guess. In this reference interaction model it MUST be provided by the Verifier and MUST be used as a proof of freshness wrt to conveyed evidence ensuring that the result of an attestation activity was created recently (i.e. triggered by the challange emitted by the Verifier). As such the Nonce MUST be part of the signed claimed set that composes the attestation evidence send by the Attestor to the Verifier.

Shared Secret Identifier:

: mandatory

: An identifier that MUST be associated with the shared secret, which is used to sign the attestation evidence claim set.

Attestation Evidence:

: mandatory

: The signed minimal claim set that is required to euaable integrity proofing of the corresponding Attestor's characteritics. Examples of claims included in attestation evidence are claims about sensor data, policies that are active on the system entity, versions of a platform's composite firmware, currently running software instance, routing tables, or information about a local time source. Attestation evidence must be cryptographically bound to the Nonce provided by the
Verifier, the Identity of the Attestee, as well as to the Shared
Secret identified by the Shared Secret ID.

Requested Evidence:

: optional

: An Attestor MAY provide several additional information or metadata that are relevant to the apraisal conducted be the Verifier in order to proof correctness of the integrity claims created by the Attestor. Usually, all information associated with the signed claim set that is available to the Attestor SHOULD be conveyed if additional evidence is requested. This additional information can be composed as complementary signed claim sets or can be encapsulated claim sets in the signed claim set that composes the evidence about integrity. This information element is option to enable a minimum size of evidence that MUST be conveyed to the Verifier.

Identity:

: mandatory

: A statement about a distinguishable Attestor made by an entity without accompanying evidence of its validity, used as proof of the identity of the Attestor.

# Interaction Model

The following sequence diagram illustrates the reference remote attestation procedure defined by this document.

~~~~

  [Attestor]                                               [Verifier]
      |                                                         |
      | <-------- Nonce, Shared Secret ID, [Requested Evidence] |
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

The remote attestation procedure is initiated by the Verifier, sending an attestation request to the Attestor. The attestion request consists of a Nonce, a Shared Secret ID, and an optional Requested Evidence part. The Nonce guarantees attestation freshness. The Shared Secret ID selects the shared secret the Attestor is requested to sign its response with. The Requested Evidence part is optional to down the scope of received evidence, if required. E.g., the Verifier is only interested in particular information about the Attestee.

The Attestee, after receiving the attestation request, collects the corresponding integrity claimes to compose the evidence the Verfier requested — or, in case the verifier did not include Requested Evidence information, the Attestor collects all information that can be used as complementary claims in the scope of the semantics of the remote attestation procedure. After that, the Attestor signes th Evidence with the Shared Secret including the Nonce and the Identity information, and sends the output back to the Verifier. Important at this point is that the Nonce as well as the Identity information must be cryptographically bound to the signature, i.e. it is not required for them to be present in plaintext. Furthermore, those information can be part of the signature after a one-way function (e.g. a hash function) was applied to them. There is also a possibility to scramble the Nonce or Identity with other known information to both the Verifier and Attestee. A prominent example is the IP address of the Attestor that usually is known by the Attestor as well as the Verifier. This extra information can be used to scramble the Nonce in order to counter a certain type of relay attacks. As soon as the Verifier receives the attestation evidence data, it appraises the signed evidence, the Identity as well as the claims in the claim set, e.g. by comparing it to known (good), expected reference claims (e.g. a Reference Integrity Measurement Manifest), or evaluating it in other ways. The final output, also referred to as Attestation Result, is a new claim about properties of the Attestor, i.e. whether it is a trusted system or not.

# Further Context

Depending on the use cases to cover there may be additional requirements.

## Confidentiality

Use confidential communication to exchange attestation information. This requirement usually is present when communication happens over insecure channels, such as the public Internet. Speaking of a suitable communcation protocol, TLS is a good candidate. In private networks, such as carrier management networks, it must be evaluated that.... [to be continued]

## Mutual Authentication

In particular use cases mutual authentication may be desireable in such a way that a Verifier also needs to proof its identity to the Attestor instead of only the Attestor proving its identity to the Verifier.

## Hardware-Enforcement/Support

HSM, Shielded Secrets, TEE, etc.

#  Security Considerations

There are always some.

#  Acknowledgements

Maybe.

#  Change Log

No changes yet.

--- back
