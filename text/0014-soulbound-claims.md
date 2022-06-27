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

FDP claims can be upgraded to work with Soulbound tokens. This feature gives holders a more Web3 way, to verify provenance. 

- As an issuer, I need to grant claims for one or more assets / data items
- As an issuer, I need to revoke claims
- As a holder, I need to read my issued claims
- As a holder, I need to share my issued claims with a verifier
- As a verifier, I need to ask a holder for verification 
- As a verifier, I need to verify holder proof / presentation

# Reference-level explanation

[TODO Implement Solidity / JavaScript]

# Drawbacks

Zero Knowledge Proofs (ZKP) have a steep learning curve, the implementer should research existing ZKP  tooling as well if  non-zero knowledge proofs work well.

# Rationale and alternatives

TBD

# Prior art

TBD

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
