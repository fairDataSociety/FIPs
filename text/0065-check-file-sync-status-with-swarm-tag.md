- FIP: 65
- title: Check file sync Status
- author: Sabyasachi Patra, @asabya
- status: draft
- created: 2023-01-18

# Summary
It is beneficial for the user to be informed when an upload of a file is finished, 
meaning that all newly created chunks have been successfully sent to the network and are accessible in 
the designated location for retrieval.

# Context

Currently, fairOS-dfs and fdp-storage does not have a way to check file sync status in the network. 

We can create a blank tag before starting file upload and send the tag in the header for each block upload request and
save the tag in the file metadata. That way we can use the tag later to check the sync status of the file.

## Modifications
We add `tag` field in file matadata
```go
type MetaData struct {
	Version          uint8  `json:"version"`
	Path             string `json:"filePath"`
	Name             string `json:"fileName"`
	Size             uint64 `json:"fileSize"`
	BlockSize        uint32 `json:"blockSize"`
	ContentType      string `json:"contentType"`
	Compression      string `json:"compression"`
	CreationTime     int64  `json:"creationTime"`
	AccessTime       int64  `json:"accessTime"`
	ModificationTime int64  `json:"modificationTime"`
	InodeAddress     []byte `json:"fileInodeReference"`
	Tag              uint32 `json:"tag"`
	Mode             uint32 `json:"mode"`
}
```

In the upload request we create a `tag` with `nil` address.

```go
tag, err := client.CreateTag(nil)
if err != nil {
    return err
}
```

We add the `tag` in the request header for each block upload request
```go
req.Header.Set("Swarm-Tag", fmt.Sprintf("%d", tag))
```

We save the `tag` in file metadata
```go
metadata.Tag = tag
```

We add api to check sync status with `podName` and `filePath`. To do this we get the file metadata and use bee's tag api to display  
```go
type StatusResponse struct {
	Total     int64 `json:"total"` // Total number of chunks
	Processed int64 `json:"processed"` // Number of distinct chunks processed
	Synced    int64 `json:"synced"` // Number of distinct chunks for which the statement of custody arrived
}
```

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
