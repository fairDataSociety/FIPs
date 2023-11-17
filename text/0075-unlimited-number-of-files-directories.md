- FIP: (Number, to be assigned)
- title: Create unlimited number of files and directories in a parent directory
- author: (Sabyasachi Patra, @asabya)
- status: draft
- created: (2023-11-17)

# Summary
This proposal is to create unlimited number of files and directories inside a directory.

# Context, motivation and guide level explanation
We store files list in a single chunk. This limits the number of files and directories as a single chunk size is 4096 bytes.

This proposal solves that by storing file list as a file. This will allow us to create unlimited number of files and directories.

We already have a working file uploading mechanism for both fairOS-dfs and fdp-storage. We can use that to upload the list in a new topic `index.dfs`.
So for every directory we store a `/DIR_PATH/index.dfs` file. This file will contain `Metadata` of the list of files and directories of that directory.

# Reference-level explanation

- we create a new file `index.dfs`.
- We upload the list at this `/DIR_PATH/index.dfs` path, but do not add that entry in the parent directory list. 
- we set the min block size to `1000000` and `gzip` as compression type

Pseudo code:

```
d.fileObject.Upload(listData, "index.dfs", int64(len(listData)), 1000000, "/DIR_PATH", "gzip", podPassword)
```

### Why such approach?
In fairOS-dfs we can update file content given an offset. This allows us to update the pod list file without having to upload the entire file again.

We do not break anything. We read the previous list and update with this proposed solution at runtime with whatever logic is required.
## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
