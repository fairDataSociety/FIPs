- FIP: 15
- title: P2P Marketplaces with feeds
- author: @molekilla (Rogelio Morrell)
- status: draft
- created: 2022-06-22

# Summary

NFT marketplaces are mostly public, anyone can auction NFTs to buyers. Minting NFTs in certain use cases might required privacy. This draft document explains how a P2P Marketplace can be developed using Swarm technological features like feeds and messaging.

# Context and motivation
In a traditional NFT marketplaces, the user flow starts with the creator publishing assets to a decentralised content network, eg IPFS, Swarm, Arweave, etc. These assets are unprotected, and are linked only through the ERC-721 metadata schema (usually a JSON representation) to a tokenized representation in a specific chain (the most common is the ERC-721 token).

Many problems arise with common use cases like dynamic publishing, eg that depends on how rare a NFT asset is or that depends on a games outcome. Many obfuscation techniques are design and available as Solidity best practices or design patterns. These strategies might be difficult for developers to apply in practice, including pros and cons in smart contract security.

What if we could in ad-hoc way, create a Dapp using Swarm, that publishes feeds which the dapp "discovers" and makes a P2P auction between parties?


# Guide-level explanation

A  P2P Marketplace for NFT as the simplest use case can be built with the following:

- Publish NFT assets: Publish to Swarm as files
- Publish metadata: Add metadata to either as JSON or Beeson, these metadata must be BMT verifiable offchain.
- Invite users to P2P Marketplace using PSS Messaging
- Load Auction dapp from shared topic with FDP contracts

# Reference-level explanation
Technical explanation that explains the design in sufficient detail that interaction with other systems is clear and it is reasonably clear how it would get implemented. Corner case should be covered with examples.

------

This is the technical portion of the RFC. Explain the design in sufficient detail that:

- Its interaction with other features is clear.
- It is reasonably clear how the feature would be implemented.
- Corner cases are dissected by example.

The section should return to the examples given in the previous section, and explain more fully how the detailed proposal makes those examples work.

# Drawbacks
What are the drawbacks of the design?

# Rationale and alternatives
Why was the design chosen from all possible designs, despite drawbacks and why were some others not chosen?

# Prior art
Discuss what prior art exists in relation to the FIP. This shows the author has studied the field and also informs the readers of the state of affairs.


# Unresolved questions
Address what:

- parts of design need to be resolved as part of the FIP process,
- parts of design need to be resolved through implementation process,
- parts are out of scope of the FIP to be resolved.

# Future possibilities
Explain how you see the proposal could change in the future. You can list things that might be out of scope of the proposal but you see might be needed in the future or are just in the idea phase at the moment.

This section could also be left blank. 

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
