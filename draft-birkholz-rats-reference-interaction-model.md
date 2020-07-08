---
title: Reference Interaction Models for Remote Attestation Procedures
abbrev: REIM
docname: draft-birkholz-rats-reference-interaction-model-latest
wg: RATS Working Group
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
  org: Fraunhofer SIT
  abbrev: Fraunhofer SIT
  email: michael.eckel@sit.fraunhofer.de
  street: Rheinstrasse 75
  code: '64295'
  city: Darmstadt
  country: Germany
- ins: C. Newton
  name: Christopher Newton
  org: University of Surrey
  email: cn0016@surrey.ac.uk
- ins: L. Chen
  name: Liqun Chen
  org: University of Surrey
  email: liqun.chen@surrey.ac.uk

normative:
  RFC2119:
  RFC3161: TSA
  RFC5280:
  RFC7049: CBOR
  RFC7252: COAP
  RFC8174:
  BCP205: RFC7942
  RFC8610:

informative:
  I-D.ietf-rats-architecture: RATS
  I-D.birkholz-rats-tuda: TUDA
  DAA:
    title: Direct Anonymous Attestation
    author:
    - ins: E. Brickell
      name: Ernie Brickell
    - ins: J. Camenisch
      name: Jan Camenisch
    - ins: L. Chen
      name: Liqun Chen
    seriesinfo:
      ACM: >
        Proceedings of the 11rd ACM conference on Computer and Communications Security
      page: 132-145
    date: 2004
  turtles:
    title: "Turrles all the way down"
    target: https://en.wikipedia.org/wiki/Turtles_all_the_way_down
    author:
      org: Wikipedia
    date: 2020-07-06
    
--- abstract

This document describes interaction models for remote attestation procedures (RATS).
Three conveying mechanisms -- Challenge/Response, Uni-Directional, and Streaming Remote Attestation  -- are illustrated and defined.
Analogously, a general overview about the information elements typically used by corresponding conveyance protocols are highlighted. Privacy preserving conveyance of Evidence via Direct Anonymous Attestation is elaborated on for each interaction model, individually.

--- middle

# Introduction

Remote ATtestation procedureS (RATS, {{-RATS}}) are workflows composed of roles and interactions, in which Verifiers create Attestation Results about the trustworthiness of an Attester's system component characteristics.
The Verifier's assessment in the form of Attestation Results is created based on Attestation Policies and Evidence -- trustable and tamper-evident Claims Sets about an Attester's system component characteristics -- created by an Attester.
The roles *Attester* and *Verifier*, as well as the Conceptual Messages *Evidence* and *Attestation Results* are terms defined by the RATS Architecture {{-RATS}}.
This documents captures interaction models that can be used in specific RATS-related solution documents.
The primary focus of this document is the conveyance of attestation Evidence.
Specific goals of this document are to:

* prevent inconsistencies in descriptions of these interaction models in other documents (due to text cloning over time),
* enable to highlight an exact delta/divergence between the core set of characteristics captured here in this document and variants of these interaction models used in other specifications or solutions, and to
* illustrate the application of Direct Anonymous Attestation (DAA) for each of the interaction models described.

In summary, this document enables the specification and design of trustworthy and privacy preserving conveyance methods for attestation Evidence from an Attester to a Verifier.
While the conveyance of other Conceptual Messages is out-of-scope the methods described can also be applied to the conveyance of Endorsements or Attestation Results.

# Terminology

This document uses the terms, roles, and concepts defined in {{-RATS}}:

Attester, Verifier, Relying Party, Conceptual Message, Evidence, Endorsement, Attestation Result, Appraisal Policy, Attesting Environment, Target Environment

A PKIX Certificate is an X.509v3 format certificate as specified by {{RFC5280}}.

{::boilerplate bcp14}

# Disambiguation

The term "Remote Attestation" is a common expression and often associated or connoted with certain properties.
The term "Remote" in this context does not necessarily refer to a remote entity in the scope of network topologies or the Internet.
It rather refers to a decoupled system or entities that exchange the payload of the Conceptual Message type called Evidence {{-RATS}}.
This conveyance can also be "local", if the Verifier is part of the same entity as the Attester, e.g., separate system components of a Composite Device (a single RATS Entity).
Examples of these types of co-located environments include: a Trusted Execution Environment (TEE), Baseboard Management Controllers (BMCs), as well as other physical or logical protected/isolated/shielded Computing Environments (e.g. embedded Secure Elements (eSE) or Trusted Platform Modules (TPM)).

# Scope and Intent

This document focuses on generic interaction models between Attesters and Verifiers in order to convey Evidence.
Complementary procedures, functions, or services that are required for a complete semantic binding of the concepts defined in {{-RATS}} are out-of-scope of this document.
Examples include: identity establishment, key distribution and enrollment, time synchronization, as well as certificate revocation.

Furthermore, any processes and duties that go beyond carrying out remote attestation procedures are out-of-scope.
For instance, using the results of a remote attestation that are created by the Verifier, e.g., how to triggering remediation actions or recovery processes, as well as such remediation actions and recovery processes themselves, are also out-of-scope.

The interaction models illustrated in this document are intended to provide a stable basis and reference for other solutions documents inside or outside the IETF.
Solution documents of any kind can reference the interaction models in order to avoid text clones and to avoid the danger of subtle discrepancies.
Analogously, deviations from the generic model descriptions in this document can be illustrated in solutions documents to highlight distinct contributions.

# Direct Anonymous Attestation

DAA {{DAA}} is a signature scheme used in RATS that allows preservation of the privacy of users that are associated with an Attester (e.g. its owner).
Essentially, DAA can be seen as a group signature scheme with the feature that given a DAA signature no-one can find out who the signer is, i.e., the anonymity is not revocable.
To be able to sign anonymously an Attester has to obtain a credential from a DAA Issuer.
The DAA Issuer uses a private/public key pair to generate a credential for an Attester and makes the public key (in the form of a public key certificate) available to the verifier to enable them to validate the DAA signature obtained as part of the Evidence. 

In order to support these DAA signatures, the DAA Issuer MUST associate a single key pair with each group of Attesters and use the same key pair when creating the credentials for all of the Attesters in this group.
The DAA Issuer’s public key certificate for the group replaces the Attester Identity documents in the verification of the Evidence (instead of unique Attester Identity documents).
This is in contrast to intuition that there has to be a unique Attester Identity per device.

This document extends the duties of the Endorser role as defined by the RATS architecture with  respect to the provision of these Attester Identity documents to Attesters.
The existing duties of the Endorser role and the duties of a DAA Issuer are quite similar as illustrated in the following subsections.

## Endorsers

Via its Attesting Environments, an Attester can only create Evidence about its Target Environments.
After being appraised to be trustworthy, a Target Environment may become a new Attesting Environment in charge of creating Evidence for further Target Environments.
{{-RATS}} explains this as Layered Attestation.
Layered Attestation has to start with an initial Attesting Environment (i.e., there cannot be turtles all the way down {{turtles}}).
At this rock bottom of Layered Attestation, the Attesting Environments are called Roots of Trust (RoT).
An Attester cannot create Evidence about its own RoTs by design.
As a consequence, a Verifier requires trustable statements about this subset of Attesting Environments from a different source than the Attester itself.
The corresponding trustable statements are called Endorsements and originate from external, trustable entities that take on the role of an Endorser (e.g., supply chain entities).

## Endorsers for Direct Anonymous Attestation

In order to enable DAA to be used, an Endorser role takes on the duties of a DAA Issuer in addition to its already defined duties.
DAA Issuers offer zero-knowledge proofs based on public key certificates used for a group of Attesters {{DAA}}.
Effectively, these certificates share the semantics of Endorsements. The differences are:

* The associated private keys are used by the DAA Issuer to provide an Attester with a credential that it can use to convince the Verifier that its Evidence is valid.
To keep their anonymity the Attester randomises this credential each time that it is used.
* The Verifier can use the DAA Issuer's public key certificate, together with the randomised credential from the Attester, to confirm that the Evidence comes from a valid Attester.
* A credential is conveyed from an Endorser to an Attester together with the transfer of the public key certificates from Endorser to Verifier.

The zero-knowledge proofs required cannot be created by an Attester alone -- like the Endorsements of RoTs -- and have to be created by a trustable third entity -- like an Endorser.
Due to that vast semantic overlap (XXX-mcr:explain), an Endorser in this document can convey trustable third party statements both to a Verifier and an Attester.

# Normative Prerequisites

In order to ensure an appropriate conveyance of Evidence, the following set of prerequisites MUST be in place to support the implementation of interaction models:

Attester Identity:

: The provenance of Evidence with respect to a distinguishable Attesting Environment MUST be correct and unambiguous.

: An Attester Identity MAY be a unique identity, it MAY be included in a zero-knowledge proof (ZKP), or it MAY be part of a group signature, or it MAY be a randomised DAA credential.

Attestation Evidence Authenticity:

: Attestation Evidence MUST be correct and authentic.

: In order to provide proofs of authenticity, Attestation Evidence SHOULD be cryptographically associated with an identity document (e.g. an PKIX certificate or trusted key material, or a randomised DAA credential), or SHOULD include a correct and unambiguous and stable reference to an accessible identity document.

Authentication Secret:

: An Authentication Secret MUST be available exclusively to an Attester's Attesting Environment.

: The Attester MUST protect Claims with that Authentication Secret, thereby proving the authenticity of the Claims included in Evidence.
The Authentication Secret MUST be established before RATS can take place.

Evidence Freshness:

: Evidence MUST include an indicator about its Freshness that can be understood by a Verifier. Analogously, interaction models MUST support the conveyance of proofs of freshness in a way that is useful to Verifiers and their appraisal procedures.

Evidence Protection:

: Evidence MUST be a set of well-formatted and well-protected Claims that an Attester can create and convey to a Verifier in a tamper-evident manner.

# Generic Information Elements

This section defines the information elements that are vital to all kinds interaction models.
Varying from solution to solution, generic information elements can be either included in the scope of protocol messages or can be included in their payload.
Ultimately, the following information elements are required by any kind of scalable remote attestation procedure using one or more of the interaction models provided.

Attester Identity ('attesterIdentity'):

: *mandatory*

: A statement about a distinguishable Attester made by an Endorser without accompanying evidence about its validity - used as proof of identity.

: In DAA the Attester's identity is not revealed to the verifier.
The Attester is issued with a credential by the Endorser that is randomised and then used to anonymously confirm the validity of their evidence.
The evidence is verified using the Endorser’s public key.

Authentication Secret IDs ('authSecID'):

: *mandatory*

: A statement representing an identifier list that MUST be associated with corresponding Authentication Secrets used to protect Evidence.
In DAA, Authentication Secret IDs are represented by the Endorser (DAA issuer)’s public key that MUST be used to create DAA credentials for the corresponding Authentication Secrets used to protect Evidence.

: Each Authentication Secret is uniquely associated with a distinguishable Attesting Environment. Consequently, an Authentication Secret ID also identifies an Attesting Environment.
In DAA an Authentication Secret ID does not identify a unique Attesting Environment but associated with a group of Attesting Environments.
This is because an Attesting Environment should not be distinguishable and the DAA credential which represents the Attesting Environment is randomised each time it used.

Handle ('handle'):

: *mandatory*

: A statement that is intended to uniquely distinguish received Evidence and/or determine the Freshness of Evidence.

: A Verifier can also use a Handle as an indicator for authenticity or attestation provenance, as only Attesters and Verifiers that are intended to exchange Evidence should have knowledge of the corresponding Handles. Examples include Nonces or signed timestamps.

Claims ('claims'):

: *mandatory*

: Claims are assertions that represent characteristics of an Attester's Target Environment.

: Claims are part Conceptual Message and are, for example, used to appraise the integrity of Attesters via a Verifiers. The other information elements in this section can be expressed as Claims in any type of Conceptional Messages.

Reference Claims ('refClaims')

: *mandatory*

: Reference Claims are a specific subset of Appraisal Policies as defined in {{-RATS}}.

: Reference Claims are used to appraise the Claims received from an Attester via appraisal by direct comparison. For example, Reference Claims MAY be Reference Integrity Measurements (RIM) or assertions that are implicitly trusted because they are signed by a trusted authority (see Endorsements in {{-RATS}}). Reference Claims typically represent (trusted) Claim sets about an Attester's intended platform operational state.

Claim Selection ('claimSelection'):

: *optional*

: A statement that represents a (sub-)set of Claims that can be created by an Attester.

: Claim Selections can act as filters that can specify the exact set of Claims to be included in Evidence. An Attester MAY decide whether or not to provide all Claims as requested via a Claim Selection.

Evidence ('signedAttestationEvidence'):

: *mandatory*

: A set of Claims that consists of a list of Authentication Secret IDs that each identifies an Authentication Secret in a single Attesting Environment, the Attester Identity, Claims, and a Handle. Attestation Evidence MUST cryptographically bind all of these information elements. The Evidence MUST be protected via the Authentication Secret. The Authentication Secret MUST be trusted by the Verifier as authoritative.

Attestation Result ('attestationResult'):

: *mandatory*

: An Attestation Result is produced by the Verifier as the output of the appraisal of Evidence. Attestation Results include condensed assertions about integrity or other characteristics of the corresponding Attester.

# Interaction Models

The following subsections introduce and illustrate the interaction models:

1. Challenge/Response Remote Attestation
2. Uni-Directional Remote Attestation
3. Streaming Remote Attestation

Each section starts with a sequence diagram illustrating the interactions between Attester and Verifier.
The other roles RATS roles -- mainly Relying Parties and Endorsers -- are not relevant for this interaction model.
While the interaction models presented focus on the conveyance of Evidence, future work could apply this to the conveyance of other Conceptual Messages, namely Attestation Results, Endorsements, or Appraisal Policies.

All interaction model have a strong focus on the use of a handle to incorporate a proof of freshness.
The ways these handles are processed is the most prominent difference between the three interaction models.

## Challenge/Response Remote Attestation

~~~~
.----------.                                                .----------.
| Attester |                                                | Verifier |
'----------'                                                '----------'
     |                                                            |
     |                                                            |
valueGeneration(targetEnvironment)                                |
     | => claims                                                  |
     |                                                            |  
     | <------requestEvidence(handle, authSecIDs, claimSelection) |
     |                                                            |
claimsCollection(claimSelection)                                  |
     | => collectedClaims                                         |
     |                                                            |
evidenceGeneration(handle, authSecIDs, collectedClaims)           |
     | => evidence                                                |
     |                                                            |
     | returnEvidence-------------------------------------------> |
     | returnEventLog-------------------------------------------> |
     |                                                            |
     |                  evidenceAppraisal(evidence, eventLog, refClaims)
     |                                       attestationResult <= |
     |                                                            |
~~~~

This Challenge/Response Remote Attestation procedure is initiated by the Verifier, by sending a remote attestation request to the Attester.
A request includes a Handle, a list of Authentication Secret IDs, and a Claim Selection.

In the Challenge/Response model, the handle is composed of qualifying data in the form of a cryptographically strongly randomly generated, and therefore unpredictable, nonce.
The Verifier-generated nonce is intended to guarantee Evidence freshness.

The list of Authentication Secret IDs selects the attestation keys with which the Attester is requested to sign the Attestation Evidence.
Each selected key is uniquely associated with an Attesting Environment of the Attester.
As a result, a single Authentication Secret ID identifies a single Attesting Environment.

Analogously, a particular set of Evidence originating from a particular Attesting Environments in a composite device can be requested via multiple Authentication Secret IDs.
Methods to acquire Authentication Secret IDs or mappings between Attesting Environments to Authentication Secret IDs are out-of-scope of this document.

The Claim Selection narrows down the set of Claims collected and used to create Evidence to those that the Verifier requires.
If the Claim Selection is omitted, then by default all Claims that are known and available on the Attester MUST be used to create corresponding Evidence.
For example when performing a boot integrity evaluation, a Verifier may only be requesting a particular subset of claims about the Attester, such as Evidence about BIOS and firmware the Attester booted up, and not include information about all currently running software.

While it is crucial that Claims, the Handle, as well as the Attester Identity information MUST be cryptographically bound to the signature of Evidence, they may be presented in an encrypted form.

Cryptographic blinding MAY be used at this point. For further reference see section {{security-and-privacy-considerations}}.

As soon as the Verifier receives signed Evidence, it validates the signature, the Attester Identity, as well as the Nonce, and appraises the Claims.
Appraisal procedures are application-specific and can be conducted via comparison of the Claims with corresponding Reference Claims, such as Reference Integrity Measurements.
The final output of the Verifier are Attestation Results. Attestation Results constitute new Claims Sets about an Attester's properties and characteristics that enables Relying Parties, for example, to assess an Attester's trustworthiness.

## Uni-Directional Remote Attestation

~~~~
.----------.                                                .----------.
| Attester |                                                | Verifier |
'----------'                                                '----------'
     |                                                            |
valueGeneration(targetEnvironment)                                |
     | => claims                                                  |
     |                                                            |
     |                   .--------------------.                   |
     | <----------handle |                    |                   |
     |                   | Handle Distributor |                   |
     |                   |                    | handle----------> |
     |                   '--------------------'                   |
     |                                                            |
evidenceGeneration(handle, authSecIDs, collectedClaims)           |
     | => evidence                                                |
     |                                                            |
     | pushEventLog---------------------------------------------> |
     | pushEvidence---------------------------------------------> |
     |                                                            |
     |                   appraiseEvidence(evidence, eventLog, refClaims)
     |                            evidenceAppraisal(evidence, refClaims)
     |                                       attestationResult <= |
     ~                                                            ~
     |                                                            | 
valueGeneration(targetEnvironment)                                |
     | => claimsDelta                                             |
     |                                                            |
evidenceGeneration(handle, authSecIDs, collectedClaims)           |
     | => evidence                                                |
     |                                                            |
     | pushEventLogDelta----------------------------------------> |
     | pushEvidence---------------------------------------------> |
     |                                                            |
     |              appraiseEvidence(evidence, eventLogDelta, refClaims)
     |                            evidenceAppraisal(evidence, refClaims)
     |                                       attestationResult <= |
     |                                                            |
~~~~

Uni-Directional Remote Attestation procedures can be initiated both by the Attester and by the Verifier.
Initiation by the Attester can result in unsolicited pushes of Evidence to the Verifier.
Initiation by the Verifier always results in solicited pushes to the Verifier.
The Uni-Directional model uses the same information elements as the Challenge/Response model.
In the sequence diagram above, the Attester initiates the conveyance of Evidence (comparable with a RESTful POST operation or the emission of a beacon).
While a request of evidence from the Verifier would result in a sequence diagram more similar to the Challenge/Response model (comparable with a RESTful GET operation), the specific manner how handles are created and used always remains as the distinguishing quality of this model.
In the Uni-Directional model, handles are composed of trustable signed timestamps as shown in {{-TUDA}}, potentially including other qualifying data.
The handles are created by an external 3rd entity -- the Handle Distributor -- that includes a trustworthy source of time and takes on the role of a Time Stamping Authority (TSA, as initially defined in {{-TSA}}).
Timstamps created from local clocks (absolute clocks using a global timescale, as well as relative clocks, such as tick-counters) of Attesters and Verifiers MUST be cryptographically bound to fresh Handles received from the Handle Distributor.
This binding provides a proof of synchronization that MUST be included in every evidence created.
Correspondingly, evidence created for conveyance via this model provides a proof that it was fresh at a certain point in time.
Effectively, this allows for series of evidence to be pushed to multiple Verifiers, simultaniously. 
Methods to detect excessive time drift that would mandate a fresh Handle to be received by the Handle Distributor, as well as timing of handle distribution are out-of-scope of this document.

## Streaming Remote Attestation

~~~~
.----------.                                                .----------.
| Attester |                                                | Verifier |
'----------'                                                '----------'
     |                                                            |
valueGeneration(targetEnvironment)                                |
     | => claims                                                  |
     |                                                            |
     | <----subscribeEvidence(handle, authSecIDs, claimSelection) |
     | subscriptionResult --------------------------------------> |
     |                                                            |
evidenceGeneration(handle, authSecIDs, collectedClaims)           |
     | => evidence                                                |
     |                                                            |
     | pushEventLog---------------------------------------------> |
     | pushEvidence---------------------------------------------> |
     |                                                            |
     |                  evidenceAppraisal(evidence, eventLog, refClaims)
     |                                       attestationResult <= |
     ~                                                            ~
     |                                                            |
valueGeneration(targetEnvironment)                                |
     | => claimsDelta                                             |
     |                                                            |
evidenceGeneration(handle, authSecIDs, collectedClaims)           |
     | => evidence                                                |
     |                                                            |
     | pushEventLogDelta----------------------------------------> |
     | pushEvidence---------------------------------------------> |
     |                                                            |
     |             evidenceAppraisal(evidence, eventLogDelta, refClaims)
     |                                       attestationResult <= |
     |                                                            |
~~~~

Streaming Remote Attestation procedures require the setup of subscription state.
Setting up subscription state between a Verifier and an Attester is conducted via a subscribe operation.
This subscribe operation is used to convey the handles required for Evidence generation.
Effectively, this allows for series of evidence to be pushed to a Verifier similar to the Uni-Directional model.
While a Handle Distributor is not required in this model, it is also limited to bi-lateral subscription relationships, in which each Verifier has to create and provide its individual handle.
Handles provided by a specific subscribing Verifier MUST be used in Evidence generation for that specific Verifier.
The Streaming model uses the same information elements as the Challenge/Response and the Uni-Directional model.
Methods to detect excessive time drift that would mandate a refreshed Handle to be conveyed via another subscribe operation are out-of-scope of this document.


# Additional Application-Specific Requirements

Depending on the use cases covered, there can be additional requirements. An exemplary subset is illustrated in this section.

## Confidentiality

Confidentiality of exchanged attestation information may be desirable. This requirement usually is present when communication takes place over insecure channels, such as the public Internet. In such cases, TLS may be uses as a suitable communication protocol that preserves confidentiality. In private networks, such as carrier management networks, it must be evaluated whether or not the transport medium is considered confidential.

## Mutual Authentication

In particular use cases mutual authentication may be desirable in such a way that a Verifier also needs to prove its identity to the Attester, instead of only the Attester proving its identity to the Verifier.

## Hardware-Enforcement/Support

Depending on given usage scenarios, hardware support for secure storage of cryptographic keys, crypto accelerators, as well as protected or isolated execution environments can be mandatory requirements. Well-known technologies in support of these requirements are roots of trusts, such as Hardware Security Modules (HSM), Physically Unclonable Functions (PUFs), Shielded Secrets, or Trusted Executions Environments (TEEs).

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

Olaf Bergmann, Michael Richardson, and Ned Smith

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

- Changes from version 02 to version 00 RATS related document
  - update of the challenge/response diagram
  - minor rephrasing of Prerequisites section
  - rephrasing to information elements and interaction model section

- Changes from version 00 to version 01
  - added Attestation Authenticity, updated Identity and Secret
  - relabeled Secret ID to Authentication Secret ID + rephrasing
  - relabeled Claim Selection to Assertion Selection + rephrasing
  - relabeled Evidence to (Signed) Attestation Evidence
  - Added Attestation Result and Reference Assertions
  - update of the challenge/response diagram and expositional text
  - added CDDL spec for CoAP FETCH operation proof-of-concept

- Changes from version 01 to version 02
  - prepared the inclusion of additional reference models
  - update to Introduction and Scope section
  - major update to (Normative) Prerequisites
  - relabeled Attestation Authenticity to Att. Evidence Authenticity
  - relabeled Assertion term back to Claim terms
  - added BCP205 Implementation Status section related to Appendix CDDL

- Changes from version 02 to version 03
  - major refactoring to now accommodate three interaction models
  - updated existing and added two new diagrams for models
  - major refactoring of existing and adding of new diagram description
  - incorporated content about Direct Anonymous Attestation
  - integrated comments from Michael Richardson
  - updated roster

--- back

{: #coap-fetch-bodies}
# CDDL Specification for a simple CoAP Challenge/Response Interaction

The following CDDL specification is an exemplary proof-of-concept to illustrate a potential implementation of the Challenge/Response Interaction Model. The transfer protocol used is CoAP using the FETCH operation. The actual resource operated on can be empty. Both the Challenge Message and the Response Message are exchanged via the FETCH operation and corresponding FETCH Request and FETCH Response body.

In this example, evidence is created via the root-of-trust for reporting primitive operation "quote" that is provided by a TPM 2.0.

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
