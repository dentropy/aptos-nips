#### ANIP-02

Nostr Verified Aptos Payments
----

The use case of ANIP-02,

* Proof of Meme
* Tipping nostr users for specific content
* Payments for access to communities (See [NIP-29](https://github.com/nostr-protocol/nips/blob/master/29.md) for more info on Relay-based groups)


This NIP defines a new event kind for proposing and validating payments between users specifically leaveraging the nostr event tagging system and Apto's Account Model.

Making Payments Flow
----

1. A user get's the address of their aptos account
2. Create a nostr event

``` json
{
  "kind": 99734,
  "content": "Aptos Payment",
  "tags": [
    ["amount", "21000"],
    ["aptos_account", "0x12341234"],
    ["p", "Pubkey you are sending to"]
    ["contract_address", "0x1234"]
  ],
  "pubkey": "97c70a44366a6535c145b333f973ea86dfdc2d7a99da618c40c64705ad98e322",
  "created_at": 1679673265,
  "id": "30efed56a035b2549fcaeec0bf2c1595f9a9b3bb4b1a38abaf8ee9041c4b7d93",
  "sig": "f2cb581a84ed10e4dc84937bd98e27acac71ab057255f6aa8dfa561808c981fe8870f4a03c1e3666784d82a9c802d3704e174371aa13d63e2aeaf24ff5374d9d"
}
```

3. Make Payment to Aptos Memo Provider, make sure the aptosaccount in the nostr event tag is the same as the account your are writing from. You must provide the event id, `30efed56a035b2549fcaeec0bf2c1595f9a9b3bb4b1a38abaf8ee9041c4b7d93` to the memo smart contract for this event to be validated by clients. It is very important that the aptosaccount mentioned in the nostr event match your aptos account otherwise clients will not be able to look up your nostr maypment
4. Publish the nostr event

Verifying Payments Flow
----

We use the following Flow smart contract to attach memos to transactions.


Already Deployed on mainnet at,

function-id 0xb30ca52fed02524c89b5305863fc037c11186a98e98ff1d06a37fb2df002411e::transfer_with_memo::transfer_with_memo
``` rust
module transfer_with_memo::transfer_with_memo {
    use std::signer;
    use std::vector;
    use aptos_framework::coin;
    use aptos_framework::aptos_coin::AptosCoin;
    use aptos_framework::event;

    const E_INVALID_MEMO_LENGTH: u64 = 1;

    #[event]
    struct TransferEvent has drop, store {
        sender: address,
        to: address,
        memo: vector<u8>,
    }

    public entry fun transfer_with_memo(
        sender: &signer,
        to: address,
        amount: u64,
        memo: vector<u8>
    ) {
        assert!(vector::length(&memo) == 64, E_INVALID_MEMO_LENGTH);
        let sender_addr = signer::address_of(sender);
        coin::transfer<AptosCoin>(sender, to, amount);
        event::emit(TransferEvent {
            sender: sender_addr,
            to,
            memo,
        });
    }

    #[view]
    public fun get_memo_length(memo: vector<u8>): u64 {
        vector::length(&memo)
    }
}
```
- [Transaction 2727288673 (0xbe2ab6ab50351ec6e1a55bb781246569c4a6510b46c9c8c6e4a37ef0fe783b22) | Aptos Explorer](https://explorer.aptoslabs.com/txn/0xbe2ab6ab50351ec6e1a55bb781246569c4a6510b46c9c8c6e4a37ef0fe783b22?network=mainnet)
- [Transaction 2727296582 (0x2336bee1751253013f76807257c59b7164211735d2a27504833509b630b560a1) | Aptos Explorer](https://explorer.aptoslabs.com/txn/0x2336bee1751253013f76807257c59b7164211735d2a27504833509b630b560a1?network=mainnet)

1. Get the aptosaccount and aptosaccount
2. List the transactions from the account as such 
3. Look for event, `0xb30ca52fed02524c89b5305863fc037c11186a98e98ff1d06a37fb2df002411e::transfer_with_memo::TransferEvent` in their transactions
4. Validate the nostr event id and the amount matches


Verifying Payments Validation Example
----

``` js
import { generateSecretKey, getPublicKey, finalizeEvent, verifyEvent } from 'nostr-tools/pure'
import * as nip19 from 'nostr-tools/nip19'
const wsListen = new WebSocket("wss://mememaps.net")

let sk = generateSecretKey() // `sk` is a Uint8Array
let pk = getPublicKey(sk) // `pk` is a hex string
let new_event = await finalizeEvent({
    kind: 1,
    created_at: Math.floor(Date.now() / 1000),
    tags: [
        ["amount", "69420"],
        ["aptos_account", "0xb30ca52fed02524c89b5305863fc037c11186a98e98ff1d06a37fb2df002411e"],
        ["to_aid", "newatlantis"]
        ["p", "d582ea4464058383af89dbc571f72aaaaffd103aab3bbc8ddd2342e66d7e6b56"]
        ["contract_address", "0xb30ca52fed02524c89b5305863fc037c11186a98e98ff1d06a37fb2df002411e"]
    ],
    content: 'You can say whatever you want here',
}, sk)
await new Promise(resolve => setTimeout(resolve, default_server_timeout_ms));

console.log(new_event)
wsListen.send(JSON.stringify([
    "EVENT",
    new_event,
]));

wsListen.close()
let npub = nip19.npubEncode(pk)
let nsec= nip19.nsecEncode(sk)
console.log(nsec)
console.log(npub)

```

``` bash
aptos move run \
  --function-id b30ca52fed02524c89b5305863fc037c11186a98e98ff1d06a37fb2df002411e::transfer_with_memo::transfer_with_memo \
  --args address:1d7d5e84b6b76b1cc780b4fae185e84bc3fb5be452faafde4d5fcadcb06fcf37 u64:69420 string:d582ea4464058383af89dbc571f72aaaaffd103aab3bbc8ddd2342e66d7e6b56  --profile mainnet02

```

``` js

async function getAndLoopTransactions() {
  try {
    // Replace with your actual API key
    const apiKey = process.env.APTOS_API_KEY || 'YOUR_API_KEY';
    const accountAddress = '0xb30ca52fed02524c89b5305863fc037c11186a98e98ff1d06a37fb2df002411e';
    const url = `https://api.mainnet.aptoslabs.com/v1/accounts/${accountAddress}/transactions`;

    // Make API request
    const response = await fetch(url, {
      headers: {
        Authorization: `Bearer ${apiKey}`
      }
    });

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    // Get transactions array
    const transactions = await response.json();

    nostr_payments = []

    // Loop through transactions
    for (const tx of transactions) {
      if(Object.keys(tx).includes("payload")){
        if(Object.keys(tx.payload).includes("function")){
            if(tx.payload.function == "0xb30ca52fed02524c89b5305863fc037c11186a98e98ff1d06a37fb2df002411e::transfer_with_memo::transfer_with_memo") {
                nostr_payments.push({
                    to_aptos_address: tx.payload.arguments[0],
                    amount: tx.payload.arguments[1],
                    pubkey: tx.payload.arguments[2]
                })
            }
        }
      }
      // Add your processing logic here
    }
    console.log(nostr_payments)
    return nostr_payments;
  } catch (error) {
    console.error('Error fetching transactions:', error.message);
    throw error;
  }
}

// Example usage
getAndLoopTransactions()
  .then(() => console.log('Done processing transactions'))
  .catch(err => console.error('Failed:', err));

```

#### Similar Nostr Implimentation Possiblities

* [nips/57.md at master · Bitcoin Lightning Network Zaps](https://github.com/nostr-protocol/nips/blob/master/57.md)
