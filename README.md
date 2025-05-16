# Aptos Nostr Inprovment Proposals

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
