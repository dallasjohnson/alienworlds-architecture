---
layout: default
---

# **Shining** smart contract:
## Blockchain account: `s.federation`

This contract manages the shining of NFT tokens. The owner of particular NFTs can transfer the NFT as well as a TLM amount to this contract in order to exchange the NFT for a different one. For an NFT type to be exchangeable for another type it needs to be added to the lookup table with an associated cost for the exchange. During the process of shining the old NFT that the user submits is destroyed and a new NFT is created. The Trilium deposited for the shining is also burnt so this has the effect of decreasing the total Trilium in existence.

## Technical view of Permissions on chain
**-- Permission Name** - Requirements to satisfy  

**-- -- -- -- -- Child Permission Name** - Requirements to satisfy

**-- -- -- -- -- -- -- Linked to Contract::Action**

    owner - requires federation @active
        active - requires PUB_K1_7sCYfweZorEdVrFuXMo79pF39rFcgjtShinEnWozGJYYgahsku
            burn - requires s.federation@eosio.code
                alien.worlds::burn
                alien.worlds::retire
                atomicassets::burnasset
            mint - requires s.federation@eosio.code
                atomicassets::mintasset


## Actions

* `addlookup(uint32_t from, uint32_t to, asset cost, uint8_t qty, time_point_sec start_time, bool active)` - adds source and destination NFT template ids as well as a cost and start time and a flag to indicate if the exchange pair is activated.
  * requires auth `s.federation@active`
* `tlmtransfer` - This tracks TLM deposits into this account and stores a record in the deposits table associated with the sender. 
  * requires auth of the TLM contract with the transfer action only
* `nfttransfer` - This tracks NFT transfers into this account. When an NFTs transfer is attempted, there is a check to find a matching record in the lookups table for the NFT templates. If an active record is found, there is a check for an associated deposit that would cover the cost of the shining process for each NFT. If all the checks are satisfied the received NFTs and deposited TLM are burned and a new NFT is created for the sending account.
  * requires auth of the `atomicassets` contract with the transfer NFT action related to this collection only.

## Storage

* Lookups Table to store potential NFTs that can be shined
    * uint32: from
    * uint32: to
    * uint8: qty
    * asset: cost
    * time point sec: start time
    * bool: active

* Deposits Table to store temporary fungible token transfers to pay for shining actions. 
    * name: account
    * asset: quantity