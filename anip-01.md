# ANIP-01

---

Mapping Nostr Keys to Aptos Name Service Identities
---

The use case of ANIP-01 are,

* Allow people to discover your notr account via a easy to remember name [aptos ID](https://www.aptosnames.com/)
* Allow Key Rotation
* Proof of History
* Accept Payments

On events of kind 0 (`user-metadata`) one can specify the key `aid` with a aptos username of an [aptos ID](https://www.aptosnames.com/) leaveraging [aptos-labs/apt-id platform](https://github.com/aptos-labs/apt-id/tree/main) name service records resolving the key nostr to the users npub descrived in [nip-19](https://github.com/nostr-protocol/nips/blob/553c1c77c004329206a3ba851f3a68367d9edc9a/19.md?plain=1#L19).


#### Example

If a client sees an event like this:

**Nostr Event**
``` json
{
  "pubkey": "b0635d6a9851d3aed0cd6c495b282167acf761729078d975fc341b22650b07b9",
  "kind": 0,
  "content": "{\"name\": \"paul\", \"aid\": \"newatlantis\"}"
  // other fields...
}
```

It will make a GET request to `https://www.aptosnames.com/api/mainnet/v1/address/newatlantis` and get back a response that will look like

``` JSON
{
    address: "0x1d7d5e84b6b76b1cc780b4fae185e84bc3fb5be452faafde4d5fcadcb06fcf37"
}
```

Aptos Name Service Records can then be resolved to the apt-id service,

``` bash
GET https://aptid.xyz/api/profile/links?address=0x1d7d5e84b6b76b1cc780b4fae185e84bc3fb5be452faafde4d5fcadcb06fcf37 | jq

[
  {
    "id": "nostr",
    "title": "nostr",
    "url": "npub1erty6egrhakxye9ul6knjazn25zv4qzaxygcl72ghvvz9yxq0hqsry6rne"
  }
]

```

Clients can also verify name records directly on aptos rather than relying on aptid.xyz

``` ts
import { AccountAddressInput, parseTypeTag, TypeTagAddress } from "@aptos-labs/ts-sdk";
import { Aptos, AptosConfig, Network as SdkNetwork } from "@aptos-labs/ts-sdk";

export interface ProfileLink {
  id: string;
  title: string;
  url: string;
}
export type LinkTree = {
  __variant__: "SM";
  links: {
    data: {
      key: string;
      value: {
        __variant__: "UnorderedLink";
        url: string;
      };
    }[];
  };
};
export const NETWORK = "mainnet";
export const CONTRACT_ADDRESS = "0x631f344549b798ad70cb5ab1842565b082fdfe488b7c6d56a257220222f6a191";
export const APTOS_API_KEY = ""
export const client = new Aptos(
  new AptosConfig({
    network: SdkNetwork.MAINNET,
    clientConfig: { API_KEY: APTOS_API_KEY },
  }),
);
const linksAbi = {
  typeParameters: [],
  parameters: [new TypeTagAddress()],
  returnTypes: [parseTypeTag(`0x1::simple_map::SimpleMap`)],
};
export async function getLinks(address: AccountAddressInput): Promise<ProfileLink[]> {
  return client
    .view<[LinkTree]>({
      payload: {
        function: `${CONTRACT_ADDRESS}::profile::view_links`,
        functionArguments: [address],
        abi: linksAbi,
      },
    })
    .then(([data]) => {
      return (
        data?.links?.data?.map((link) => {
          const title = !link.key ? link.value.url : link.key;
          return {
            id: title,
            title,
            url: link.value.url,
          };
        }) ?? []
      );
    });
}
```

Calling getLinks will result in a response like this

``` json
[{
    "id": "nostr",
    "title": "nostr",
    "url": "npub1erty6egrhakxye9ul6knjazn25zv4qzaxygcl72ghvvz9yxq0hqsry6rne"
}]
```

#### Similar Nostr Implimentation Possiblities

* [nips/05.md - Mapping Nostr keys to DNS-based internet identifiers](https://github.com/nostr-protocol/nips/blob/master/05.md)

#### Example Implimentations

* Nostr has a [fork of nostr client nostrudle](https://github.com/dentropy/nostrudel) accessable at [nostr.mememaps.net](https://nostr.mememaps.net/) that support setting aid on the `/profile` page and validating nostr event kind 0 using the method described above.
