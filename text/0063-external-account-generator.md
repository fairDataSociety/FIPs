- FIP: 63
- title: External Account Generator
- author: Igor Shadurin, @IgorShadurin
- status: draft
- created: 2023-03-20

# Summary
An External Account Generator is a mechanism for the deterministic creation of FDP accounts based on the user's existing wallet without intermediate storage of the account in the data storage.

# Context, motivation and guide level explanation

Storing an FDP account means storing a mnemonic or seed in an open or encrypted form in any way: a sheet of paper, data storage, etc.

This FIP describes how to create an FDP account with an external signer without storing the FDP account on third party storage and without exposing the external signer's private key or wallet address.

An external signer is a secure Ethereum Wallet that does not expose the private key/mnemonic to third-party APIs and each data signature must be confirmed by the user. Metamask, Ledger, Trezor, etc. can act as such signers.

On the one hand, such external signers provide the greatest security, but on the other hand, if they are used as a wallet for an FDP account, they create a huge flow of data signature confirmations from the user. Also, the mnemonic phrase of external signers should not be exposed to third party security applications, which makes it highly undesirable to use the mnemonic phrase as an FDP account. All these criteria call into question the possibility of using external signers as an FDP account in its current form.

In order to meet the security and convenience criteria, two properties of the signed data of external signers can be used, namely, the determinism of the generated signature and the uniqueness of the external signer signature.

Since no one but the owner of the external signer can create a valid signature for a particular data set, and sharing this signature does not affect security. This signature can be used as an entropy to initialize a mnemonic which can be used for initialization of an FDP account.

There are several standards for signing data. Not every standard is supported by all external signers. One standard is supported at least by 3 popular (Metamask, Ledger, Trezor) external signers: `personal_sign` ([EIP-191](https://eips.ethereum.org/EIPS/eip-191)). This method also allows a person to see the data being signed before signing to make sure that the signature is used for the intended purpose.

To generate a deterministic signature, a string must be passed to the signing function. When a user reads a string, it should be clear what the user is giving permission for and what will happen to the user data after the data is signed. The following text will be used as the signature string:

`I am granting FULL ACCESS to the FDP account`

To get the signature of certain data, method `personal_sign` should be called in Metamask or another external signer. The result of the called method will be a hex string of 132 characters, starting with 0x. Then, this string needs to be converted into an array of bytes (65 bytes) and the first 32 bytes should be used as entropy. Entropy can be converted to BIP-39 Mnemonic and passed to fdp-storage.

Wallet generation example using Ethers.js library.

```typescript
async function getSignatureMnemonic(): Promise<string> {
  const SIGN_WALLET_DATA = 'I am granting FULL ACCESS to the FDP account';
  const MAX_ENTROPY_LENGTH_BYTES = 32; // entropy length limit in Ethers.js
  const metamask = window.ethereum;
  const accounts = await metamask.request({
    method: 'eth_requestAccounts',
  });
  const address = accounts[0];
  const signature = await metamask.request({
    method: 'personal_sign',
    params: [SIGN_WALLET_DATA, address],
  });
  const slicedSignature = utils.hexDataSlice(signature, 0, MAX_ENTROPY_LENGTH_BYTES);
  
  return utils.entropyToMnemonic(slicedSignature);
}
```

Using generated wallet in fdp-storage.

```typescript
const mnemonic = await getSignatureWallet();
fdp.account.setAccountFromMnemonic(mnemonic);
```

After that, personal storage can be used or a Portable Account can be registered.

# Fair Data Principles alignment
Is the proposal aligned with the [Fair Data Society principles](https://principles.fairdatasociety.org/)? Why is it good for the Fair Data Society? What kind of final use cases can emerge from the proposal?

# Prior art
- [Personal Storage](./0061-personal-storage.md)

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
