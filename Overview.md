## Overview
----

Below describes a user Bob who wants to publish a message, and Alice who wants to subscribe to it.
In the general case, it is assumed that channels have one publisher. 
If Alice and Bob wish to exchange messages, Alice will open a second channel to share with Bob.
None of this describes the method of exchanging keys.

![Protocol](https://iotaledger.github.io/mam.client.js/doc/mam-diagram.svg)


### Signatures

Each message contains the root of the next merkle tree, indicating a path of forward identity, and the sibling hashes in the merkle tree. For example, if the key for B was used, Hashes A and CD would be included after the signature, terminated by a null hash string to indicate the end of the tree.
The merkle trees are created by calculating the hash of their children. I,e. The parent of keys A and B is Hash(AB).

![Merkle](https://iotaledger.github.io/mam.client.js/doc/serial-merkle.svg)

Signing messages is done using the standard IOTA one-time signature scheme from the hash of the message, 
with serial merkle trees to allow for temporary extension of identity,
but doesn't require the full signature size necessary with compound merkle trees.

The organization of the output message before encryption is [Signature - Sibling Hashes - Null Hash - Next Merkle Root - Message]:

![layout](https://iotaledger.github.io/mam.client.js/doc/mam-layout.svg)

Included in the tag of the transactions of the MAM is the index of the key used to allow for correct verification of the merkle root.

### Encryption
Messages published with MAM are encrypted by one-time symmetric key encryption as described by:

![Encryption](https://iotaledger.github.io/mam.client.js/doc/encryption.svg)

A trinary additive cipher is used to generate the cipher text.

The next message key seed is generated by incrementing the current seed by at least one, 
and then taking the hash  of that value. 
This ensures that no part of the current message leaks the key to the next message unintentially.
A message chain is thus created, which a user can join at any point, but can never look backward.

A message chain chain may be forked by incrementing more than once before taking the message hash.

The ID published to the tangle is the hash of the hash of the message key seed.