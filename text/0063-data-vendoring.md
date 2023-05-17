- FIP: 63
- title: Decentralized Queryable Data Vendoring using predefined data structures and models
- author: @molekilla (Rogelio Morrell)
- status: draft
- created: 2022-12-24

# Summary
Decentralized Queryable Data Vendoring applies to data providers or sellers that requires the ability to have censorship resistance and unstoppable data available as API able data structures. Here we show how to implement a Swarm/FDP backed architecture that supports Web 2.0 and Web 3.0 clients.

# Abstract
A Decentralized Queryable Data Vendoring protocol consists of several components:


- **Data Provisioning Broker**: A onchain smart contract that contains `getPriceEstimates(dataSizeToProvision)` where `dataSizeToProvision` is the size of the data to be provisioned and results in a result set with price estimates and ERC20 token to be used for purchase and `purchaseProvisionedData(dataSizeToProvision, options as calldata)` which purchases the data, in our case a call to Swarm PostageStamp contract, but the interface is generic enough to be also useful in any EVM compatible engine (eg Filecoin FVM). These smart contract brokers requires oracles to price data for each chain

- **Data Vendor Index Page**: A generic index page as HTML and RESTful that returns metadata and human readable information about the data. This is ENS resolvable and is very similar to a GitHub Repository Index page.

- **Data Vendor Query API**: An `Apache Arrow DataFusion` Rust SQL and DataFrame query engine that queries JSON (Beeson) data blocks and uses [HTTP Query method](https://httpwg.org/http-extensions/draft-ietf-httpbis-safe-method-w-body.html)

- **Data Vendor Structures Fields API**: A [Structured Field Values for HTTP](https://httpwg.org/http-extensions/draft-ietf-httpbis-sfbis.html) compatible web server that works as Redis-like database, and might either be centralized or decentralized in nature.

- **Data Vendor Schema Model API**: Similar to Data Structures API, but uses Schemas.org models.

- **Data Structures Server**: An HTTP server that implements `HTTP Query method` and `Structured Field Values` and uses Swarm as decentralized datastore.

# Motivation

## Decentralized Queryable Data Vendoring protocol in a nutshell

### One Click/Kiosk like Data Provisioning
Just as cloud computing platforms works when users provision resources, a decentralized queryable data vendoring protocol requires easy provisioning to purchase resources. The use case is similar to Amazon Cloud Computing Reserved Instances, where a decentralized content network (eg Arweave, Filecoin, Swarm) resources are properly reserve allocated inside an onchain smart contract called `Provisioning Pricing`, which abstracts the chain bidding/purchasing process.

### Web 2.0 Indexable
Every `vendor` repository contains an index.html and GET /index that resolves through HTTP and ENS. The data can either be stored in decentralized content networks, but it will be defined as a Markdown-compatible interface, so that it can be indexed by search bots, Mastodon or RSS feeds.

### Queryable Data and storage
As data is stored in data structures or schema models, the query API allows you to query the underlying data which is stored as JSON (Beeson) in Swarm with append-only Swarm Feeds.

Following `FDP Smart Data Contracts`, we add a `parent` property to the storage interface:


```typescript
/**
 * Reads the last state from the feed.
 * @param includeProofs True if the inclusion proofs should be included in the response.
 * @returns contract state
 */
async storageRead(includeProofs?: boolean): Promise<{ state: Uint8Array }> {
    const feedR = this.feed.makeFeedR(this.topic, this.signer.address)
    const last = await feedR.findLastUpdate()

    const data = await this.bee.downloadData(last.reference)
    const block = data.json() as any

    return {
       state: arrayify(block.state),
    }
}

/**
 * Writes the state to the feed.
 * @param state The state to be written.
 * @returns void
 */
async storageWrite(state: Uint8Array) {
    const feedRW = this.feed.makeFeedRW(this.topic, this.signer)

    const chunk = makeChunk(state)

    const lastUpdate = feedRW.getLastUpdate()

    const block: Block = {
        parent: lastUpdate.reference,
        state: hexlify(state),
        chunk: Buffer.from(chunk.address()).toString('hex'),
        timestamp: Date.now(),
    }

    const reference = await this.bee.uploadData(this.postageBatchId, JSON.stringify(block))

    return feedRW.setLastUpdate(this.postageBatchId, reference.reference)
}

/**
 * Serializes the contract state and writes it to the feed.
 */
async serialize(state = {}) {
    const value = new BeeSon<JsonValue>({
        json: state,
    })
    const block = await Block.encode({ value, codec, hasher })

    await this.storageWrite(block.bytes)
}

/** Deserializes the contract state from the feed. */
async deserialize() {
    const data = await this.storageRead()

    // decode a block
    const state = await Block.decode({
        bytes: data.state as Uint8Array,
        codec: codec as BlockDecoder<252, BeeSon<JsonValue>>,
        hasher,
    })

    const res = await state.value

    return res.json
}

```

Given a SQL query which requests `parent`, the wrapper backend implementation must be able to handle `linkable` queries and  can be implemented with the TableProvider trait found in Apache DataFusion. An example of using Apache DataFusion looks like the following:


```rust
use datafusion::prelude::*;

#[tokio::main]
async fn main() -> datafusion::error::Result<()> {
  // register the table
  let ctx = SessionContext::new();
  ctx.register_json("example", "tests/example.json", JsonReadOptions::new()).await?;

  // create a plan to run a SQL query
  let df = ctx.sql("SELECT a, MIN(b) FROM example GROUP BY a LIMIT 100").await?;

  // execute and print results
  df.show().await?;
  Ok(())
}
```

## Use Cases
- Decentralized database
- Personal data  markets


# Drawbacks
None
  

# References

- [Apache Arrow Ballista](https://arrow.apache.org/ballista/)
- [Apache Arrow DataFusion](https://docs.rs/crate/datafusion/latest)
- [Apache Calcite](https://calcite.apache.org/docs/powered_by.html)
- [FDP Smart Data Contracts](https://github.com/fairDataSociety/fdp-personal-sc)
- [EIP-725: General key-value store and execution standard](https://eips.ethereum.org/EIPS/eip-725)
- [BlobVM](https://morioh.com/p/f6c5a30b3db4)
- [SpacesVM](https://github.com/ava-labs/spacesvm?ref=morioh.com&utm_source=morioh.com)
- [Storage Patterns: Stacks Queues and Deques](https://programtheblockchain.com/posts/2018/03/23/storage-patterns-stacks-queues-and-deques/)
- [Storage Patterns: Doubly Linked List](https://programtheblockchain.com/posts/2018/03/30/storage-patterns-doubly-linked-list/)
- [Understanding Ethereum Smart Contract Storage](https://programtheblockchain.com/posts/2018/03/09/understanding-ethereum-smart-contract-storage/)
- [A  discoverable, indexable snark storage content (dissc) protocol](https://github.com/fairDataSociety/FIPs/blob/86b1f6909a7661cde46d1e3c20c22651703ff2c4/text/0060-dissc-protocol.md)
- [Crypto/Web3 Startup ideas 2023](https://alliancedao.notion.site/Crypto-Web3-Startup-Ideas-2023-Edition-48d40ccadeeb42a48056659fcce109b1#5900abc0529c4de28d13338dcf76d1e9)
- [HTTP Query method](https://httpwg.org/http-extensions/draft-ietf-httpbis-safe-method-w-body.html)
- [Structured Field Values for HTTP](https://httpwg.org/http-extensions/draft-ietf-httpbis-sfbis.html)

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).