- FIP: 60
- title: A discoverable, indexable snark storage content (dissc) protocol
- author: @molekilla (Rogelio Morrell)
- status: draft
- created: 2022-10-26

# Summary
A discoverable, indexable snark storage content (dissc) protocol

# Abstract

We propose a smart contract EVM based protocol where  data is stored  as Swarm streaming feeds, kept indexable by smart contracts, using EIP-5559 for  data  mutations and data driver agnostic.

# Motivation

## dissc protocol in a nutshell

### Discoverable
Each dissc is a repository of data or database, similar to a git repository. There is dissc registry where a dissc is created by an user that will be owner and admin of that new dissc.

A dissc is compatible as an ENS register, so to access a dissc it must be address or name resolvable.

### Indexable
A dissc contains one or more swarm streaming feeds references. A streaming feed is a dynamic or random access feed, compared to the default sequential feed, it gives us a way to access a particular feed entry by a time period. 

In the dissc protocol, there are two structs: a `DatabaseRegistry`, and `Chapters` struct, which defined a streaming feed time based period sequences:


```
DatabaseRegistry
=============
createdAt
id
```

```
Chapters
=============
createdAt
disscId
updatedPeriod
feedReference
feedTopic
feedOwner
```

Chapters are similar to how Compact Disc (CD) or DVD layouts data when you burn to a disc. You can skip, rewind or forward given the existing chapters for a feed.

The secondary indexing approach is used with `addChapter`, which takes a recently updated feed (a data snapshot at a specific time), and a calldata parameter is sent where it contains a blob for indexing the data as smart contract event.

Using existing Web3 tools, each chapter relevant content is indexable by using existing smart contract tools.


### JSON-RPC as default API protocol
With the existence of EIP-5559, we can model dissc protocol to be 100% JSON-RPC based, with that in mind we keep developers using the same tooling as before (ethers.js, web3.js, etc). 

The smart contract API must have the following calls:

`function downloadData(address dissc, bytes32 reference, bytes32 topic)`

With response
```
StorageHandledByOffChainDatabase(
    (
        "DisscResolver", 
        "1", 
        1, 
         0x32f94e75cde5fa48b6469323742e6004d701409b
    ), 
    "https://example.com/r/{sender}", 
    (
        0xd5fa2b00, 
        0x727f366727d3c9cc87f05d549ee2068f254b267c, 
        [
            ("bytes", "<Uint8Array or Blob or ReadableStream>), 
           ], 
        181
    )
)
```

`function uploadData(address dissc, bytes32 reference, bytes32 topic, bytes calldata args)`

With response

```
StorageHandledByOffChainDatabase(
    (
        "DisscResolver", 
        "1", 
        1, 
         0x32f94e75cde5fa48b6469323742e6004d701409b
    ), 
    "https://example.com/r/{sender}", 
    (
        0xd5fa2b00, 
        0x727f366727d3c9cc87f05d549ee2068f254b267c, 
        [
            ("reference", "0x418ae76a9d04818c7a8001095ad01a78b9cd173ee66fe33af2d289b5dc5f4cba"), 
            ("topic", "database"), 
            ("dissc", "0x727f366727d3c9cc87f05d549ee2068f254b267c")
        ], 
        181
    )
)
```

`function hasReference(address dissc, bytes32 reference)`

With response

```
StorageHandledByOffChainDatabase(
    (
        "DisscResolver", 
        "1", 
        1, 
         0x32f94e75cde5fa48b6469323742e6004d701409b
    ), 
    "https://example.com/r/{sender}", 
    (
        0xd5fa2b00, 
        0x727f366727d3c9cc87f05d549ee2068f254b267c, 
        [
            ("found", "true"), 
        ], 
        181
    )
)
```

Underlying API is integrated with `fdp-storage` API

### KEM-DEM ZK Snarks
To enabled data privacy, we propose a novel usage of KEM-DEM found in Polygon Nightfall, (see https://github.com/EYBlockchain/nightfall_3/blob/65d9911f27d9f062d58e71f857baf3ca1f55113f/wallet/src/nightfall-browser/services/kem-dem.ts).

This allows data to be encrypted for one or more recipients and these keys are proved with `BabyJubJub/Poseidon`.

The underlying storage can be Uint8Array and accepts batches, this could also be used for selective disclosure of data, allowing indexing of public data.

To query privacy data entries, KEM-DEM decrypts with recipients keys.  Additionally, Secret Shamir's Sharing can be used for multiparty secret sharing.

### Data drivers
By storing data as BeeSon/JSON, we can abstract the database driver, in this initial proposal we described three data drivers approaches.

#### IPLD Blockstore
https://github.com/fairDataSociety/fdp-storage-blockstore/tree/development

For integration with IPLD /  multiformats, the existing `fdp-storage-blockstore` can be used with dissc protocol. Query language supported is IPLD path traversal.

#### LokiJS
https://github.com/fairDataSociety/fdp-lokijs-adapter

The LokiJS DB Adapter allows MongoDB queries in an in-memory database, previously loaded from a JSON storage.

#### SQLite.js 
https://sql.js.org/#/

SQL.js allows loading data in-memory and query using SQL syntax. 

### Data driver interface specification

  ```
constructor(web3, dataPrivacyOptions, useDataDriver={Default, IPLD, Loki, SQLite})
load(ref, decryptSettings)
save(ref, data, encryptSettings)
export
query(options)
```
  
### Introducing Swarm Feed Linkable path specification
We can map IPLD, BeeSon, and JSON-LD using techniques and design patterns found in WakuConnect.

In Waku Connect, you defined topics as path with information that is used to understand the data or message being broadcasted.

Eg `/{dapp-name}/{version}/{content-topic-name}/{encoding}`

A Swarm Feed Linkable Topic can be in the form of:

`/{dissc}/{api-version}/{reference}/{topic}/{data-driver}`

When encoding this to a BeeSon structure, it generates a SwarmFeedLinkable composite type.

When encoded to IPLD, it becomes a Link type appended with `dissc://`

When encoded to JSON-LD, it creates a @graph node type.

## Use Cases
A bookstore recommendation engine where certain data is selectively disclosed to the 3rd party, where users control their data with Fair Drive Protocol and with an OAuth-like mechanism approves or rejects allowing usage of their dissc based data with the dapp requesting it.



# Drawbacks
None
  
# Rationale and alternatives

  - EdgeDB

# Prior art
TODO
  
## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
