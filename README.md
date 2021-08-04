# Apostille documentation.

<em>Diagram 1: The basic transaction flow for an Apostille transaction</em>
 
![Apostille 2 0 presenation Tx Flow](https://user-images.githubusercontent.com/88449840/128221403-b7a93346-92db-45a7-b19f-7492cfebd286.png)


## How to derive a directory account (Steps 1-3)

Let’s first make a text file. This will be a file that has an ending `.txt`. In this case, we will write a simple message of the following:

`Hello world. And hello Apostille.` 

It is important to include the same capitalization and grammar. If the message is changed by a single space or period that is different, it can result in a completely different hash as a result. Hashing this text with sha256 creates the hash `e5a42f7cbff2f7539ab034fa8b58a70a90830a545a36998689d75a357436fea7`. This easily done on a home computer or various free online sites like [coding tools](https://coding.tools/sha256). 

This hash is then used to create an [IPFS](https://ipfs.io/) address via the normal [IPFS cryptographic processes](https://docs.ipfs.io/concepts/content-addressing/#cid-conversion) resulting IPFS hash is `QmSN4Su3QFscpcx7t7E3KhxCyfD26UURNR5Masqn4j6LJ9`. One can alternately sign up for a free IPFS account with a service like [Pinata](https://www.pinata.cloud/) and by dragging and dropping a file, the hash will be created automatically. 

Next we take the IPFS hash of `QmSN4Su3QFscpcx7t7E3KhxCyfD26UURNR5Masqn4j6LJ9` and hash it again with sha256. This gives us a new hash of  `e39b56532dba5a8bfd4bba45ec5b5c3105e06f30148b34eae216fe907f41b897`. This hash is used as a private key on the Symbol testnet network.  The subsequent address is `TBPDT3JUNERHFDPL5RZC4WNM3F4G46LEUZXYURQ`. The can be verified by downloading a [Symbol Wallet](https://github.com/symbol/desktop-wallet/releases) and importing this private key for an account on the testnet. 

The power of this method is quite interesting. We have now created a file and then using cryptography were able to assign a deterministic address on the Symbol blockchain that represents the starting place for users looking for more information related to that file. 

Next up for the Directory Account, the address`TBPDT3JUNERHFDPL5RZC4WNM3F4G46LEUZXYURQ` is tagged with a basice metadata json in a metadata tx following the W3C guidelines for [Decentralized Identity](https://www.w3.org/TR/did-core/) (DID) and put under a multisig contract with the Apostille account as its 1-of-1 owner, but before that can be done, the Apostille account must be derived and created, so that will be covered first. 

## How to derive an Apostille account (Step 4) 

WHile the cryptographical DNA of a Directory Account is derived entirely from an original file, an Apostille account's crytpographical origins come from both the file and the initiators private key. This is to mean that the Apostille account could have only been created by a user that had access to both the file and the private key of the initiator clearly demonstrating authorship. 

This is done via a complicated crytpographical process where the hash of the file is signed by the initiator and then converted into a private key. The code can be reviewed in Github for more details. 

Now that the Apostille account has been derived, it can be designated as the controller of the Directory Account with a 1-of-1 multisig contract which will be discussed next. 

## Attaching metadata and multisigging the Directory account (Step 5) 

For the metadata formatting, we follow the W3C guidelines for [Decentralized Identity](https://www.w3.org/TR/did-core/) (DID). We will use the following format: 

{
"Id": "did:apostille2.0.1:dir#TBBCQ6GBIQ4NNXDZKVKLDQFY3WQEFC27JR77Y2Q”,
"Controller": "did:apostille2.0.1:add#TAZX3OEE2CX6ZWVOJ7XFSQBHER3XI2EGMPKKXEY",
}

Lets take a looks at the first line 

`"Id": "did:apostille2.0.1:dir#TBBCQ6GBIQ4NNXDZKVKLDQFY3WQEFC27JR77Y2Q”,`

The `Id` explains this is the identification. 

The `did` explains we are applying a decentralized ID for the ID. 

The `apostille2.0.1` explains we are following the guidelines laid out in version 2.0.1 of Apostille being explained in this document. 

The `dir` expains that this account is a Directory Account. 

The `TBBCQ6GBIQ4NNXDZKVKLDQFY3WQEFC27JR77Y2Q` explains where this directory account directs


Next, let's review the second line: 

`"Controller": "did:apostille2.0.1:add#TAZX3OEE2CX6ZWVOJ7XFSQBHER3XI2EGMPKKXEY",`

The `Controller` let's us know who the controller/operator of this account should be. 

The `did` and `apostille2.0.1` are the same as above. 

The `add` stand for an address on the Symbol blockchain

The `TAZX3OEE2CX6ZWVOJ7XFSQBHER3XI2EGMPKKXEY` let's us know that this address should be the controller and operator of this account. 

To enforce our controller claim made in the DID metadata above, we will need to bind this Directory Account in a 1-of-1 multisig account. Normally that would mean just applying the contract to Alice's account, but in this case we will have a small work around of actually applying it to the Apostille account address we derived in step 4. The benefits of this will be made clear and explained later. 

Using the Symbol network [Typescript SDK](https://github.com/symbol/sdk-typescript), the metadata and multisig edit transactions can be applied in an aggregate transaction. 

## Attaching metadata and multisigging the Apostille account (Step 6) 

Much like what was done with the Directory Account above, we will follow the same process. The differences will be the content of the metadata transaction and the controller/operator of the multisig contract. 

The new metadata JSON looks like the following: 

{
"id": "did:apostille2.0.1:apostille#QmSN4Su3QFscpcx7t7E3KhxCyfD26UURNR5Masqn4j6LJ9",

“file_name”: ”hello-apostille.txt”,

“sha256”: ”e5a42f7cbff2f7539ab034fa8b58a70a90830a545a36998689d75a357436fea7”,

"controller": "did:apostille2.0.1:add#TAZX3OEE2CX6ZWVOJ7XFSQBHER3XI2EGMPKKXEY",
}

The first 3 parts of the the first line folllow the same convention explained above but the last 2 parts of `"id": "did:apostille2.0.1:apostille#QmSN4Su3QFscpcx7t7E3KhxCyfD26UURNR5Masqn4j6LJ9"` need an introduction. 

The `apostille` explains that this is the apostille account; a non-fungible asset. 

The `QmSN4Su3QFscpcx7t7E3KhxCyfD26UURNR5Masqn4j6LJ9` is the IPFS hash we derived in step 2, and together proclaim that this Apostille account is the Non-fungible asset on the blockchain representing the file hosted at QmSN4Su3QFscpcx7t7E3KhxCyfD26UURNR5Masqn4j6LJ9. 

The `file_name` represents the file name we saved our file as. 

The `sha256` indicates the file hash we derived in step 2. 

Once again `controller` explains who the owner/operator of this NFT is supposed to be. 

`add` indicates that controllers address. 

`TAZX3OEE2CX6ZWVOJ7XFSQBHER3XI2EGMPKKXEY` is the controller's address. 

Once again to enforce the DID claim about the controller of this asset, the account is put in a 1-of-1 multisig contract enforcing which user can control/update/transfer/sign for this asset interally within the Symbol public ledger and network. 

Once again, utilizing Symbol's aggregate transaction feature, the mutlisig edit transaction and metadata update transaction can be done at one time. In fact, all the transactions for both the directory account and the Apostille account can be done at the same time in one aggregate transaction that has collected Alice's signature, the Directory Account signature, and the Apostille Account signature. 

Since the Apostille account is also the 1-of-1 signer of the Directory account, operator rights and priveledges trickle up to Alice. 

## Conclusion

In this system that utilizing IPFS, deterministic hashes, multisignature contracts, metadata transactions, DID as a JSON, and more, we have been able to create a unique and power holistic notarization system on the Symbol blockchain. 



