- FIP: 64
- title: Store file-system permissions in file and dir matadata
- author: Sabyasachi Patra, @asabya
- status: draft
- created: 2023-01-17

# Summary
File system permission is a set of rules that determine who can access and perform certain actions on a file or directory in a computer's file system. These permissions can be applied to users and groups, and can include the ability to read, write, execute, or delete files.
Permissions can be set at the individual file or directory level, or inherited from parent directories.

# Context
With Fairdrive Desktop App (FDA), user can now access files and directories in userspace.
User can mount pods and access files through file manager and terminal. Currently FDA assigns a generic set of permissions to files and directories. This allows basic read, write and delete functionality for files and directories.

The idea behind adding and storing file system permissions to is that it will allow user to specify if they want to store any executable file in their pods and be able to run it from their mounts  

This can potentially be the stepping stone for file system ACL aswell.

# Modifications
We add `mode` fields in file and directory metadata

```go
// file metadata
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
	Mode             uint32 `json:"mode"` // stores file-permissions
}
```

```go
// directory metadata
type MetaData struct {
	Version          uint8  `json:"version"`
	Path             string `json:"path"`
	Name             string `json:"name"`
	CreationTime     int64  `json:"creationTime"`
	AccessTime       int64  `json:"accessTime"`
	ModificationTime int64  `json:"modificationTime"`
	Mode             uint32 `json:"mode"` // stores file-permissions
}
```

These modes needs to be stored in octal with leading `0`, i.e. 0777, 0644, 0755. 

Default mode for files should be 0600 and folder 0700.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
