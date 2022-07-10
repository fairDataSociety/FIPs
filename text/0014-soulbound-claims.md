- FIP: 14
- title: Soulbound tokens for identity claims
- author: @molekilla (Rogelio Morrell)
- status: draft
- created: 2022-06-27

# Summary

Soulbound tokens for identity claims allows for a better user and developer experience by focusing on the identity space and eschewing DeFi oriented design patterns. In this FIP we described our findings and recommendations for FDP identity claims.

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


A Dapp with issuer capabilities can grant and revoke SBT to holders. The claims that the issuers create are stored on Swarm, with the hash of the on-chain SBT token.

Depending on the use case, these claims can be revoked or renewed.

A holder in the context of SBTs is the one that holds the claims, in this case a driving license, university degree or allowance to enter a facility. These SBTs as claims required a wallet, where the holder can list, read and shared these claims. A claim that is shared is a proof presentation. Depending on the dapp requirements (eg must be older than 18 and have a driving license), if the dapp supports SBTs, it will ask for claims to the dapp, where the user in his device or browser receives a request to share a proof presentation.

The verifier attests a given proof presentation, that an issue claim to a holder of a SBT, is indeed valid. 
How the proof presentation does works is not specified in the SBT papers, but we can assume it must have privacy feature and be verifiable onchain/offchain.

# Reference-level explanation

## Milestones

## beezk - SBT Smart Contract Reference Implementation

`beezk` is a soulbound token smart contract reference implementation using Consensys [gnark](https://docs.gnark.consensys.net/en/latest/), which allows us to use ZK signatures with EVM Solidity smart contracts without registering additional private keys. Taking the following [ZK-SBT](https://github.com/molekilla/ZK-SBT/tree/main) project as boilerplate, we defined these interfaces.

### Solidity - PrivateSoulMinter.sol - SBT


```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;
// Credit to @Miguel Piedrafita for the SoulBound NFT contract skeleton

import "@openzeppelin/contracts/access/Ownable.sol";

/// @title PrivateSoulMinter
/// @author Enrico Bottazzi
/// @notice Barebones contract to mint ZK SBT

contract PrivateSoulMinter is Ownable{
    /// @notice Thrown when trying to transfer a Soulbound token
    error Soulbound();

    /// @notice Emitted when minting a ZK SBT
    /// @param from Who the token comes from. Will always be address(0)
    /// @param to The token recipient
    /// @param id The ID of the minted token
    event Transfer(
        address indexed from,
        address indexed to,
        uint256 indexed id
    );

    /// @notice The symbol for the token
    string public constant symbol = "SOUL";

    /// @notice The name for the token
    string public constant name = "Soulbound NFT";

    /// @notice Get the metadata URI for a certain tokenID
    mapping(uint256 => string) public tokenURI;

    /// @notice Get the hash of the claim metadata for a certain tokenID
    mapping(uint256 => bytes32) public claimSignatureHash;

    /// @notice Get the owner of a certain tokenID
    mapping(uint256 => address) public ownerOf;

    /// @notice Get how many SoulMinter NFTs a certain user owns
    mapping(address => uint256) public balanceOf;

    /// @dev Counter for the next tokenID, defaults to 1 for better gas on first mint
    uint256 internal nextTokenId = 1;

    constructor() payable {}

    /// @notice This function was disabled to make the token Soulbound. Calling it will revert
    function approve(address, uint256) public virtual {
        revert Soulbound();
    }

    /// @notice This function was disabled to make the token Soulbound. Calling it will revert
    function isApprovedForAll(address, address) public pure {
        revert Soulbound();
    }

    /// @notice This function was disabled to make the token Soulbound. Calling it will revert
    function getApproved(uint256) public pure {
        revert Soulbound();
    }

    /// @notice This function was disabled to make the token Soulbound. Calling it will revert
    function setApprovalForAll(address, bool) public virtual {
        revert Soulbound();
    }

    /// @notice This function was disabled to make the token Soulbound. Calling it will revert
    function transferFrom(
        address,
        address,
        uint256
    ) public virtual {
        revert Soulbound();
    }

    /// @notice This function was disabled to make the token Soulbound. Calling it will revert
    function safeTransferFrom(
        address,
        address,
        uint256
    ) public virtual {
        revert Soulbound();
    }

    /// @notice This function was disabled to make the token Soulbound. Calling it will revert
    function safeTransferFrom(
        address,
        address,
        uint256,
        bytes calldata
    ) public virtual {
        revert Soulbound();
    }

    function supportsInterface(bytes4 interfaceId) public pure returns (bool) {
        return
            interfaceId == 0x01ffc9a7 || // ERC165 Interface ID for ERC165
            interfaceId == 0x80ac58cd || // ERC165 Interface ID for ERC721
            interfaceId == 0x5b5e139f; // ERC165 Interface ID for ERC721Metadata
    }

    /// @notice Mint a new Soulbound NFT to `to`
    /// @param to The recipient of the NFT
    /// @param metaURI The URL to the token metadata
    function mint(address to, string calldata metaURI, bytes32 claimHashMetadata) public onlyOwner {

        require(balanceOf[to] < 1, "You can only have one token associated to your soul");

        unchecked {
            balanceOf[to]++;
        }

        ownerOf[nextTokenId] = to;
        tokenURI[nextTokenId] = metaURI;
        claimSignatureHash[nextTokenId] = claimHashMetadata;

        emit Transfer(address(0), to, nextTokenId++);
    }
}
```

Implementers should read about using [signature verifier from EIP-1271](https://v2-docs.zksync.io/dev/zksync-v2/aa.html#aa-signature-checker)


# Drawbacks

- SBTs are similar to Verified Credentials and they might be an overlap of ideas and effort.

# Rationale and alternatives

[Verifiable Credentials Data Model v1.1](https://www.w3.org/TR/vc-data-model/)


# Prior art

[Verifiable Credentials Data Model v1.1](https://www.w3.org/TR/vc-data-model/)

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
