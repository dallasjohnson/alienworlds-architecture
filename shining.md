# **Shining** smart contract:
## Blockchain account: `s.federation`
## Permissions

    owner - requires federation @active
        active - requires PUB_K1_7sCYfweZorEdVrFuXMo79pF39rFcgjtShinEnWozGJYYgahsku
            burn - requires s.federation@eosio.code
                alien.worlds::burn
                alien.worlds::retire
                atomicassets::burnasset
            mint - requires s.federation@eosio.code
                atomicassets::mintasset


## Actions

This smart contract manages the shining of NFT tokens. The owner of particular NFTs that can transfer the NFT as well as a TLM amount to this contract in order to exchange the NFT for a different one. For an NFT type to be exchangeable for another type it needs to be added to the lookup table with an associated cost for the exchange. 

Features include:

* `addlookup(uint32_t from, uint32_t to, asset cost, uint8_t qty, time_point_sec start_time, bool active)` - adds source and destination NFT template ids as well as a cost and start time and a flag to indicate if the exchange pair is activated.
* `tlmtransfer` - This tracks TLM deposits into this account and stores a record in the deposits table associated with the sender. 
* `nfttransfer` - This tracks NFT transfers into this account. When an NFTs transfer is attempted there is a check to find a matching record in the lookups table for the NFT templates. If an active record is found there is a check for an associated deposit that would cover the cost of the shining process for each NFT. If all the checks are satisfied the received NFTs and deposited TLM are burned and a new NFT is created for the sending account.

## Storage

* Lookups Table - Potential NFTs that can be shined
    * uint32: from
    * uint32: to
    * uint8: qty
    * asset: cost
    * time point sec: start time
    * bool: active

* Deposits Table
    * name: account
    * asset: quantity