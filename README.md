# Apostille documentation.

<em>Diagram 1: The basic transaction flow for an Apostille transaction</em>

![Apostille 2 0 presenation Tx Flow](https://user-images.githubusercontent.com/88449840/128221403-b7a93346-92db-45a7-b19f-7492cfebd286.png)


## How to derive a directory account (Steps 1-3)

Uploading a file to IPFS and then using it create a dedicated Symbol address to query more information about that file creates what we call in the Apostille systeam a "Directory Account". Let's review the process how to create a file and link it to a Directory Account. 

Let’s first make a text file. This will be a file that has an ending `.txt`. In this case, we will write a simple message of the following:

`Hello Apostille. Test2.` 

It is important to include the same capitalization and grammar. If the message is changed by a single space or period that is different, it can result in a completely different hash as a result. Hashing this text with sha256 creates the following hash:

`8BD07920FD4921384B1417B35418D098CFC17BE487DFA2F3909CCB436F074166` 

This easily verified on a home computer hashing app, command line, or various free online sites like [coding tools](https://coding.tools/sha256). 

This hash is then used to create an [IPFS](https://ipfs.io/) address via the normal [IPFS cryptographic processes](https://docs.ipfs.io/concepts/content-addressing/#cid-conversion) resulting IPFS hash in the following hash: 

`QmU3p5a8evEFLuSXM1Ls112aWrEgMXE3BvGSs5obTGsFPr` 

One can alternately sign up for a free IPFS account with a service like [Pinata](https://www.pinata.cloud/) and by dragging and dropping a file, the hash will be created automatically and the [file](https://gateway.pinata.cloud/ipfs/QmU3p5a8evEFLuSXM1Ls112aWrEgMXE3BvGSs5obTGsFPr) hosted on IPFS. 

Next we take the IPFS hash of `QmU3p5a8evEFLuSXM1Ls112aWrEgMXE3BvGSs5obTGsFPr` and hash it again with sha256. This gives us a new hash of  

`F528456FD0C857BC4A1B4E997D3FA741587E9711093D754E1B3B92C3603B2EF7` 

This hash is used as a private key on the Symbol testnet network.  The subsequent address is 

`TBVH5MAU63DM3534QI6JDVSVXN3RB4SDCXJLREY` 

This can be verified by downloading a [Symbol Wallet](https://github.com/symbol/desktop-wallet/releases) and importing this private key for an account on the testnet or can be viewed on the [testnet explorer](http://explorer.testnet.symboldev.network/accounts/TBVH5MAU63DM3534QI6JDVSVXN3RB4SDCXJLREY). 

The power of this method is quite interesting. We have now created a [file](https://gateway.pinata.cloud/ipfs/QmU3p5a8evEFLuSXM1Ls112aWrEgMXE3BvGSs5obTGsFPr) and then using cryptography were able to assign a deterministic address [TBVH5MAU63DM3534QI6JDVSVXN3RB4SDCXJLREY](http://explorer.testnet.symboldev.network/accounts/TBVH5MAU63DM3534QI6JDVSVXN3RB4SDCXJLREY) on the Symbol testnet blockchain that represents the starting place for users looking for more information related to that file. 

Next up for the Directory Account, the address`TBVH5MAU63DM3534QI6JDVSVXN3RB4SDCXJLREY`, will be given basic metadata via a json in a metadata tx that follows the W3C guidelines for [Decentralized Identity](https://www.w3.org/TR/did-core/) or DID. As a part of this, it will also be put under a multisig contract with the Apostille account as its 1-of-1 owner, but that is step 5 and before that can be done, the Apostille account must be derived and created. So we will cover deriving the Apostille account first.

## How to derive an Apostille account (Step 4) 

WHile the cryptographical DNA of a Directory Account is derived entirely from an original file, an Apostille account's crytpographical origins come from both the file and the initiator's private key. This is to mean that the Apostille account could have only been created by a user that had access to both the file and the private key of the initiator which is clearly demonstrating authorship. 

This is done via a complicated crytpographical process where the hash of the file is signed by the initiator and then converted into a private key. 

In the following case, the account initiating the transaction to create an Apostille account is the address of 

`TALXYP6W5VKJXOCNBJTNJ3EY4IP2BHMI5WHW55Y` 

with the public key of 

`AA5D5439522E666085B720D58E60649124790603AC4C0EB292A116643D593A82` 

which can be viewed on the exlorer [here](http://explorer.testnet.symboldev.network/accounts/TALXYP6W5VKJXOCNBJTNJ3EY4IP2BHMI5WHW55Y). 

The code can be reviewed in Github for more details. But includes the following which can be calculated in a Google Chrome Development tool. 

First do
var privateKey = CryptoJS.SHA256(ipfs).toString(CryptoJS.enc.Hex)
After that, you truncate that string to get 32 bytes private key
("0000000000000000000000000000000000000000000000000000000000000000" + privateKey.replace(/^00/, '')).slice(-64);

Applying th above cryptography, we have derived the following address which is the Apostille account! 

`TCDSPTLZL6OUSPJUIMDDMH4EW5KIFFIOXOMOZCY`

Which can be viewed on the explorer [here](http://explorer.testnet.symboldev.network/accounts/TCDSPTLZL6OUSPJUIMDDMH4EW5KIFFIOXOMOZCY)

Now that the Apostille account has been derived, it can be designated as the controller of the Directory Account with a 1-of-1 multisig contract which will be discussed next. 

## Attaching metadata and multisigging the Directory account (Step 5) 

For the metadata formatting, we follow the W3C guidelines for [Decentralized Identity](https://www.w3.org/TR/did-core/) (DID). We will use the following format: 

{ 
"Id": "did:apost:Directory_Account/dir/Apostille_Account”, 
"Controller": "did:apost:Multisig_Owner", 
}

This results in the following example for this Directory Account. 


{ 
"Id": "did:apost:TBVH5MAU63DM3534QI6JDVSVXN3RB4SDCXJLREY/dir/TCDSPTLZL6OUSPJUIMDDMH4EW5KIFFIOXOMOZCY”, 
"Controller": "did:apost:TALXYP6W5VKJXOCNBJTNJ3EY4IP2BHMI5WHW55Y", 
}

Lets take a looks at the first line 

`"Id": "did:apost:TBVH5MAU63DM3534QI6JDVSVXN3RB4SDCXJLREY/dir/TCDSPTLZL6OUSPJUIMDDMH4EW5KIFFIOXOMOZCY”`

The `Id` explains this is the identification. 

The `did` explains we are applying a decentralized ID for the ID. 

The `apost` explains we are following the guidelines laid out in version 2.0.1 of Apostille being explained in this document. 

The `TBVH5MAU63DM3534QI6JDVSVXN3RB4SDCXJLREY` is indicating this is the DID ID for the account TBVH5MAU63DM3534QI6JDVSVXN3RB4SDCXJLREY

The `dir` expains that this account is a Directory Account. 

The `TCDSPTLZL6OUSPJUIMDDMH4EW5KIFFIOXOMOZCY` explains where that this directory account (TBVH5MAU63DM3534QI6JDVSVXN3RB4SDCXJLREY) directs to TCDSPTLZL6OUSPJUIMDDMH4EW5KIFFIOXOMOZCY which is the Apostille account. 


Next, let's review the second line: 

`"Controller": "did:apost:TALXYP6W5VKJXOCNBJTNJ3EY4IP2BHMI5WHW55Y",`

The `Controller` let's us know who the controller/operator of this account should be. 

The `did` and `apost` are the same as above indicating we use the "apost" methond (short for Apostille) for interpreting how to read and processes DIDs. 

The `TALXYP6W5VKJXOCNBJTNJ3EY4IP2BHMI5WHW55Y` let's us know that this address should be the controller and operator of the directory account. 

To enforce our controller claim made in the DID metadata above, we will need to bind this Directory Account in a 1-of-1 multisig account. Normally that would mean just applying the contract to Alice's account, but in this case we will have a small work around of actually applying it to the Apostille account address we derived in step 4. The benefits of this will be made clear and explained later. 

Using the Symbol network [Typescript SDK](https://github.com/symbol/sdk-typescript), the metadata and multisig edit transactions can be applied in an aggregate transaction. Once can alternately use the [Symbol-CLI](https://github.com/symbol/symbol-cli) wallet if they are performing this manually. Using the Symbol desktop wallet to add metadata at this time will not make a metadata entry that is compatible with the explorer. 

## Attaching metadata and multisigging the Apostille account (Step 6) 

Much like what was done with the Directory Account above, we will follow the same process. The differences will be the content of the metadata transaction and the controller/operator of the multisig contract. 

The new metadata JSON looks like the following: 


{ 
"id": "did:apost:Apostille_account/nfa/IPFS_hash",
“file_name”: ”example.txt”,
“Sha256”: ”sha hash of the file”,
"file_size": "xx bytes", 
"controller": "did:apost:Multisig_Owner",
}


In our example, it is the following:

{ 
"id": "did:apost:TCDSPTLZL6OUSPJUIMDDMH4EW5KIFFIOXOMOZCY/nfa/QmU3p5a8evEFLuSXM1Ls112aWrEgMXE3BvGSs5obTGsFPr",
“file_name”: ”hello apostille test2.txt”,
“Sha256”: ”8BD07920FD4921384B1417B35418D098CFC17BE487DFA2F3909CCB436F074166”,
"file_size": "23 bytes", 
"controller": "did:apost:TALXYP6W5VKJXOCNBJTNJ3EY4IP2BHMI5WHW55Y",
}


The first 3 parts of the the first line folllow the same convention explained above but the last 2 parts of `"id": "did:apost:TCDSPTLZL6OUSPJUIMDDMH4EW5KIFFIOXOMOZCY/nfa/QmU3p5a8evEFLuSXM1Ls112aWrEgMXE3BvGSs5obTGsFPr"` need an introduction. "id" means this is an ID. "did" means this is the type of id. And "post" is the methody for decoding and encoding the "did". 

The `nfa` explains that this is the apostille account; a non-fungible asset. 

The `QmU3p5a8evEFLuSXM1Ls112aWrEgMXE3BvGSs5obTGsFPr` is the IPFS hash we derived in step 2, and strung together with a "nfa" proclaim that this Apostille account is the Non-fungible asset on the blockchain representing the file hosted at QmU3p5a8evEFLuSXM1Ls112aWrEgMXE3BvGSs5obTGsFPr. 

The `file_name` represents the file name we saved our file as. 

The `sha256` indicates the file hash we derived in step 2. 

Once again `controller` explains who the owner/operator of this NFT is supposed to be. 

`TALXYP6W5VKJXOCNBJTNJ3EY4IP2BHMI5WHW55Y` is the controller's address. 

Once again to enforce the DID claim about the controller of this asset, the account is put in a 1-of-1 multisig contract enforcing which user can control/update/transfer/sign for this asset interally within the Symbol public ledger and network. 

Once again, utilizing Symbol's aggregate transaction feature, the mutlisig edit transaction and metadata update transaction can be done at one time. In fact, all the transactions for both the directory account and the Apostille account can be done at the same time in one aggregate transaction that has collected Alice's signature, the Directory Account signature, and the Apostille Account signature. 

Since the Apostille account is also the 1-of-1 signer of the Directory account, operator rights and priveledges trickle up to Alice. 

## Conclusion

In this system that utilizing IPFS, deterministic hashes, multisignature contracts, metadata transactions, DID as a JSON, and more, we have been able to create a unique and power holistic notarization system on the Symbol blockchain. 
