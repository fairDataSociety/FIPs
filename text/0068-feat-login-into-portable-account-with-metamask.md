- FIP: 68
- title: Login into Portable account with metamask 
- author: Sabyasachi Patra, @asabya
- status: draft
- created: 2023-03-01

# Summary
Addressing web3 login workflow to portable account where users can retrieve their wallet in a decentralized manner from browser with metamask.

# Context, motivation and guide level explanation

The solution worked out here allows to log in to the user account from any browser into the same account described in [FIP-59](https://github.com/fairDataSociety/FIPs/blob/master/text/0059-portable-account.md) created with
[fdp-create-account](https://github.com/fairDataSociety/fdp-create-account) project.

# Reference-level explanation

For the login to work with metamask, user should already have a portable account created with [fdp-create-account](https://github.com/fairDataSociety/fdp-create-account).

As we know while creating a portable account users can provide a 12 word mnemonic phrase for the portable wallet or a 12 word mnemonic will be generated for them.

This solution is a two-step process

## Step 1: 
```go
ConnectPortableAccountWithWallet(userName, passPhrase, portableAccountAddress, signature string) error
```

This function will get the portable wallet (wallet seed) and re-upload an alternate socTopic described later. `Signature` can be any unique string signed by Metamask or any other wallet.

## Step 2: 
```go
LoginWithWallet(portableAccountAddress, signature string) (*user.Info, error) 
```

This function will get the encrypted portable wallet (wallet seed) and username from the alternate socTopic and decrypt it with the signature provided by the user. User needs to provide the same signature which was used in `ConnectPortableAccountWithWallet` function.

## Uploading portable wallet (wallet seed) to Ethereum Swarm to an alternate socTopic

In FIP-59 we have discussed, about the socTopic which is a topic in the swarm where the portable account is stored. In this solution we will be using an alternate socTopic, but with the same logic, to store the wallet seed.
```
socTopic = H(fdpLoginVersion + portableAccountAddress + signature)
```

Where `portableAccountAddress` and the `signature` will be provided by the user. We can get the `portableAccountAddress` my importing the portable wallet into metamask or by logging into fairOS-dfs with username and password and string the `portableAccountAddress` in the dApp scope.

## Wallet seed encryption
In FIP-59 we have discussed, about the encryption of wallet seed and storing it in a chunk. 

- We will first generate FIP-59 socTopic with username and password provided by the user.
- Then download the wallet seed chunk from FIP-59 socTopic and decrypt it with the password provided by the user.

We have the seed now. We will store the username with the seed for identifying the user while logging in with metamask.

### Creating the chunk

The content of the chunk will look something lite this

```
SEED + USERNAME_SIZE + USERNAME + RANDOM_DATA
```

We store the username length in 1 (`USERNAME_LENGTH_SIZE`) byte, then the username and then the random data.

The `SEED + USERNAME_LENGTH_SIZE + USERNAME` will be padded with random data with byte length `CHUNK_SIZE - SEED_SIZE - IV_LENGTH - USERNAME_LENGTH - USERNAME_LENGTH_SIZE`.

*** `USERNAME_LENGTH_SIZE` is 1 byte, `USERNAME_LENGTH` is the length of the username provided by the user.

The resulted data will be encrypted with AES, where the encryption key is the SHA256 hash of the signature.

```go
func (*Info) PadSeedName(seed []byte, username string, passphrase string) ([]byte, error) {
    usernameLength := len(username)
    endIndexBytes := make([]byte, nameSize)
    binary.LittleEndian.PutUint64(endIndexBytes, uint64(usernameLength))
    paddingLength := utils.MaxChunkLength - aes.BlockSize - seedSize - nameSize - usernameLength
    randomBytes, err := utils.GetRandBytes(paddingLength)
    if err != nil { 
        return nil, err
    }
    chunkData := make([]byte, 0, utils.MaxChunkLength)
    chunkData = append(chunkData, seed...)
    chunkData = append(chunkData, endIndexBytes...)
    chunkData = append(chunkData, []byte(username)...)
    chunkData = append(chunkData, randomBytes...)
    encryptedBytes, err := utils.EncryptBytes([]byte(passphrase), chunkData)
    if err != nil {
        return nil, fmt.Errorf("seed padding failed: %w", err)
    }
    return encryptedBytes, nil
}
```

### Getting seed back from the chunk

We decrypt the chunk with the same signature and then get the seed and username from the decrypted data.

```go
func (*Info) RemovePadFromSeedName(paddedSeed []byte, passphrase string) ([]byte, string, error) {
	decryptedBytes, err := utils.DecryptBytes([]byte(passphrase), paddedSeed)
	if err != nil {
		return nil, "", fmt.Errorf("seed decryption failed: %w", err)
	}
	usernameLength := int(binary.LittleEndian.Uint64(decryptedBytes[seedSize : seedSize+usernameLengthSize]))
	return decryptedBytes[:seedSize], string(decryptedBytes[seedSize+usernameLengthSize : seedSize+usernameLengthSize+usernameLength]), nil
}
```

Implementation can be found [here](https://github.com/fairDataSociety/fairOS-dfs/blob/feat/podSubscription.0/pkg/account/account.go) 

# Prior art
[FIP-59](https://github.com/fairDataSociety/FIPs/blob/master/text/0059-portable-account.md)

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
