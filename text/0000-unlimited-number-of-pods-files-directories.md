- FIP: (Number, to be assigned)
- title: Create unlimited number of pods
- author: (Sabyasachi Patra, @asabya)
- status: draft
- created: (2023-11-17)

# Summary
This proposal is to create unlimited number of pods.

# Context, motivation and guide level explanation
We store pod list in a single chunk. This limits the number of pods as a single chunk size is 4096 bytes. 

This proposal solves that by storing pod list as a file. This will allow us to create unlimited number of pods.

We already have a working file uploading mechanism for both fairOS-dfs and fdp-storage. We can use that to upload the pod list file in a new topic `PodsV2`.

# Reference-level explanation
Technical explanation that explains the design in sufficient detail that interaction with other systems is clear and it is reasonably clear how it would get implemented. Corner case should be covered with examples.

- we create a new topic `PodsV2`.
- we create a new file object with user's root account and no podname.
- we upload the pod list content with `PodsV2` as filename, `/` as path and user root account private key as podPassword.
- we set the min block size to `1000000` and `gzip` as compression type

Pseudo code:

```
f2 := f.NewFile(beeClient, podRootFeed, rootAccountAddress)
privKey := pod.getAccount().GetPrivateKey()
f2.Upload(podList, "PodsV2", 1000000, "gzip", privKey)
```

### Why such approach?
In fairOS-dfs we can update file content given an offset. This allows us to update the pod list file without having to upload the entire file again.

We do not break anything. We read the previous list and update with this proposed solution at runtime with whatever logic is required.
## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
