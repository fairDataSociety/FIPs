- FIP: 56
- title: FDP Blob Header
- author: Viktor Levente Tóth, @nugaon
- status: draft
- created: 2022-09-05

# Summary
This document defines the structure of the first 4 bytes of all FDP Blobs.

# Context and motivation
There should be an exact and simple differentiation between the introduced data-structures.

All data-structures have byte-representation in order to be saved on storage or transmitted over the network. 
This can be later used for initiating the in-memory object again (serialization/deserialization).

Facilitating the work of functions dealing with FDP data-structures, the data-blobs first 4 bytes have to be verified only for identifying the _type_ and _version_ of the data-blob (e.g. the data-blob in question is a BeeSon with version 0.1.0).

This first 4 bytes of a FDP Blob is called FDP Blob Header.

# Guide-level explanation
Every FDP Data Blob type has a reserved identifier that is defined in the first byte of FDP data blobs.
The following table lists all currently active FDP Blob type with its corresponding identifier

| FDP Blob Type | Identifier |
| ------ | ---- |
| [BeeSon](https://github.com/fairDataSociety/beeson/) | 1 |

The last 3 bytes reserved for versioning the FDP Data Blob in a [Semantic Versioning](https://semver.org/) format.
The versions are registered in the corresponding FIPs and/or in the code repositories of every Data Blob.

In case of BeeSon, the Blob Header will look like this at version 0.1.0:
`1, 0, 1, 0`

# Reference-level explanation
You can submit any data-structure which has at least a JavaScript library to serialize/deserialize it and additionally it:
- does not violate FDP principles (which are written in FIP documents like this proposal)
- offers a significant feature that is not possible to reproduce or to effectively substitue with the currently active and used FDP Data Blobs

For submitting a data-structure, create a PR that extends the FDP Blob Type table above and link the open-source repository of the JS library.
The identifier can be chosen freely from the available set, ranging from 0 to 255 expect for reserved ones but that must be accepted by the PR reviewers.

# Drawbacks
The range for the FDP data-structures may seen short, but it is designed to prevent the FDP stack from increasing in complexity (see details in the section below).

# Rationale and alternatives
FDP tries to keep and maintain few of data-structures in order to keep the FDP environment and tools simple and to ensure that data-structures satisfy generic use-cases. Thereby, it cannot be a question which data-structures should be used within dApps for a particular use-case.

Because of this, 1 byte (that can have 256 values) must be enough to register every FDP Blob in the FDP stack.

The semantic versioning defines a clear method how to version the product in question.
It is widely used in different systems that can be employed for labeling changes in the data-structures as well with notation of `[majorVersion].[minorVersoin].[pathVersion]`.
For details, please read the documentation of Semantic versioning.

# Prior art
- Semantic versioning: https://semver.org/

# Unresolved questions
TBD

# Future possibilities

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
