# Aptos Nostr Implimentation Possiblities

* This repo is based on [nostr-protocol/nips](https://github.com/nostr-protocol/nips)


##### List

* [ANIP-01: Mapping Nostr Keys to Aptos Name Service Identities](./anip-01.md)
* [ANIP-02: Nostr Verified Aptos Payments](./anip-02.md)

#### Description

Nostr(Nostr and Other Stuff Transmitted Through Relays) is a decentralized soverign social media protocol, it may remind you of urbit, farcaster, and lens protocol. What makes nostr unique from those blockchain social media protocols is that the blockchain integration on nostr is completely optional. It is just tags attached to existing nostr events.

Nostr's social media primative is just signed JSON structs where the tags are lists of lists of strings. Events all have a uniqe integer kind that allows clients to select what [Nostr Improvement Possibilities](https://github.com/nostr-protocol/nips) they support.

In this repo we define two nostr improvment possiblities and link to separate code repositories because we build on the shoulders of giants.

* [ANIP-01: Mapping Nostr Keys to Aptos Name Service Identities](./anip-01.md)
    * Nostr has a [fork of nostr client nostrudle](https://github.com/dentropy/nostrudel) accessable at [nostr.mememaps.net](https://nostr.mememaps.net/) that support setting aid on the `/profile` page and validating nostr event kind 0 using the method described above.
* [ANIP-02: Nostr Verified Aptos Payments](./anip-02.md)
    * [move-by-examples/memos at main · dentropy/move-by-examples](https://github.com/dentropy/move-by-examples/tree/main/memos)
    * Deployed on Mainnet at `0xb30ca52fed02524c89b5305863fc037c11186a98e98ff1d06a37fb2df002411e::transfer_with_memo::transfer_with_memo`
    * [Deployment Transaction](https://explorer.aptoslabs.com/txn/0xbe2ab6ab50351ec6e1a55bb781246569c4a6510b46c9c8c6e4a37ef0fe783b22?network=mainnet)

#### Problems This Project Solves

Aptos Nostr Implimentation Possiblities

Background: Nostr(Nostr and Other Stuff Transmitted Through Relays) is a decentralized soverign social media protocol, it may remind you of urbit, farcaster, and lens protocol. What makes nostr unique from those blockchain social media protocols is that the blockchain integration on nostr is completely optional. It is just tags attached to existing nostr events.

Nostr's social media primative is just signed JSON structs where the tags are lists of lists of strings. Events all have a uniqe integer kind that allows clients to select what [Nostr Improvement Possibilities](https://github.com/nostr-protocol/nips) they support.

* Social Soverinty on the Internet (ANIP-01)
    * By default everyone on Nostr is a publickey, public keys are hard to share and can be stolen, now you can attach your Aptos ID to your nostr account, this allows for easily adding people on nostr, provides a medium for key rotation, and if your nsotr pubkey leads you still own your decentralized identity
* Social Finance Payments (ANIP-02)
    * Since ANIP-01 hooks up nostr accounts to a Aptos ID, that Aptos ID points to an address that can be paid. This allows for publically shared nostr integrated transactions that can be used in social media feeds. Imagine being able to pay 1APT to boost your content in the algorithm.
* Proof of History Meme (ANIP-02)
    * Anyone scared of the AI rewriting human history. We should state history as it happens on the blockchain. Take nostr events, plus the context of pubkeys and other surrounding them, and add them as tags to ANIP-02 kind 99734 transactions to timestamp them in the history of the human story.

#### Short Description

Provides a standard for social finance payments on the already adopted nostr protocol. These Aptos Nostr Implimentation Possiblities allow for easy sharing and full soverigny of ones online digital identity and also provides social payments that work as timestamp proof of meme history on the blockchain stopping AI from rewriting history.

For those that don't know nostr, Nostr's social media primative is just signed JSON structs where the tags are lists of lists of strings. Nostr blockchain integration is a metadata standard based on these tags. Events all have a uniqe integer kind that allows clients to select which Nostr Improvement Possibilities they support.

https://github.com/dentropy/aptos-nips
@EasyA_App #EasyAConsensusHackatho

Integrates Aptos with Nostr to bring social finance guide rails with proof of history meme verified peer to peer payments into the Nostr Ecosystem

#### Technical Explanation

I use Aptos Name Sercice records to point at the pubkey of a nostr account, the nostr profile (event kind 0) has a unique key "aid" that points at the Aptois Name Service. These system hook together to validate one another. This solves problem of Nostr pubkey getting hacked because the true ownership of the identity is the Aptos Name not the pubkey itself