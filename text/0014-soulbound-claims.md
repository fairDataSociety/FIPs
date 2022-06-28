- FIP: 14
- title: Soulbound tokens for identity claims
- author: @molekilla (Rogelio Morrell)
- status: draft
- created: 2022-06-27

# Summary

Soulbound tokens for identity claims allows for a better user and developer experience by using existing Web3 technologies based on NFT token standards. In this FIP we described our findings and recommendations for FDP identity claims.

# Context and motivation
Decentralized Society is the next paradigm in the Ethereum ecosystem. Its focus is for `social use cases` where data is the main transferrable asset. A `Soul` represents an entity either a company, a group of people or a single person, which posses `Soulbound Tokens` or `SBTs`. These SBTs represent claims given to a Soul, these claims are `non-transferrable` and can be used as a `Verifiable Credential` or VC. 

SBTs also allows for metadata to be either private (eg privacy-enabled with ZKP) or public. 


# Guide-level explanation

FDP claims can be upgraded to work with Soulbound tokens. This feature gives holders a more Web3 way, to verify provenance and trust. 

- As an issuer, I need to grant claims for one or more assets / data items
- As an issuer, I need to revoke claims
- As a holder, I need to read my issued claims
- As a holder, I need to share my issued claims with a verifier
- As a verifier, I need to ask a holder for verification 
- As a verifier, I need to verify holder proof / presentation

# Reference-level explanation

## Milestones

## Milestone 1 - Issuer

A Dapp with issuer capabilities can grant and revoke SBT to holders. The claims that the issuers creates are stored in Swarm, with the hash in the onchain SBT token.

Depending on the use case, these claims can be revoked or renewed.

The implementation must be able to:

- Have SBT issuer smart contracts
- JavaScripts / Typescript library
- Worked well with existing FDP libraries and smart contracts
- Be able to support FDP registries

## Milestone 2 - Holder

A holder in the context of SBTs is the one that holds the claims, in this case a driving license, university degree or allowance to enter a facility. These SBTs as claims required a wallet, where the holder can list, read and shared these claims. A claim that is shared is a proof presentation. Depending on the dapp requirements (eg must be older than 18 and have a driving license), if the dapp supports SBTs, it will ask for claims to the dapp, where the user in his device or browser receives a request to share a proof presentation.


FDP implementation for this milestone impacts the following:

- Wallet or extension must support SBTs
- JavaScripts / Typescript library to manage holder actions like list, read and share
- Worked well with existing FDP libraries and smart contracts
- Be able to support FDP registries


## Milestone 3 - Verifier

The verifier attests a given proof presentation, that an issue claim to a holder of a SBT, is indeed valid. 
How the proof presentation does works is not specified in the SBT papers, but we can assume it must have privacy feature and be verifiable onchain/offchain.

We propose to the implementer use of BMT for proof presentation for public data, and a combination of open source zero knowledge libraries and BMT for data that requires privacy features (eg electronic invoices, medical records)

Additionally: 

- Update wallet or extension for proof presentation user flow
- Update to javascript libraries to support proof presentations

# Drawbacks

- SBTs are similar to Verified Credentials and they might be an overlap of ideas and effort.
- Zero Knowledge Proofs (ZKP) have a steep learning curve, the implementer should research existing ZKP  tooling as well if  non-zero knowledge proofs work well.

# Rationale and alternatives

[Verifiable Credentials Data Model v1.1](https://www.w3.org/TR/vc-data-model/)


# Prior art

[Verifiable Credentials Data Model v1.1](https://www.w3.org/TR/vc-data-model/)

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
