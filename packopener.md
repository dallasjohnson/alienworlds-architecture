# **PackOpener** smart contract:
## Blockchain account: `pack.worlds`
## Permissions

    owner     1:    1 EOS8TpackZ64RR3zx7FiB4LrMqp5fRS343AvitC2sn842hcxBGRXA
        active     1:    1 EOS8TpackZ64RR3zx7FiB4LrMqp5fRS343AvitC2sn842hcxBGRXA


## Actions

This smart contract manages packs of NFTs including the selection and packing of NFTs into each pack as well as the opening to reveal the contents of each pack to the new pack owner.

Features include:
*  Packs - A pack has a name, symbol and bonus token asset. Once a pack has been added it can also be edited, deleted or activated.
    * `addpack(name pack_name, symbol pack_symbol, extended_asset bonus_ft, bool active)`
    * `editpack(name pack_name, symbol pack_symbol, extended_asset bonus_ft)`
    * `activatepack(name pack_name, bool active)`
    * `delpack(name pack_name)`
*  Cards - Each pack has cards associated with them that are assigned with {crate, probability} tuples. All the probabilities for each card added to a pack must must add up to 100%. Once these cards are added to a pack their {crate, probability} values can be edited (as long as the total probabilities still totals to 100%. A card can also be deleted from a pack.
    * `addcard(name pack_name, uint64_t card_id, vector<cardprob> card_probabilities)`
    * `editcard(uint64_t card_id, vector<cardprob> card_probabilities)`
    * `delcard(uint64_t card_id)`
*  Crates - A crate provides the link between packs and the cards by providing the actual NFT assets to fill the packs for a given probability. The type of asset provided through a crate can be either an asset directly or an asset from a pre-defined list of ids with an associated NFT template. Crates can also be edited or deleted.
    * `addcrate(name crate_name, name type, vector<uint64_t> ids)`
    * `editcrate(name crate_name, vector<uint64_t> template_ids)`
    * `delcrate(name crate_name)`
*  `addasset(name crate_name, uint64_t asset_id)` - Each crate has NFT assets added to it so they can be assigned to packs later. Each NFT must be owned by this contract for this to be possible. Each crate can have all assets cleared from it with the `emptycrate` action.
    
*  `open(name account)` - For a user to be able to open a pack they must have deposited an asset with the matching symbol into this contract so it is visible in the deposit table with the user's account.
    * If a matching pack is found and it is active then the opening process can continue which involves requesting a random value from Wax random number generator.
    * When the random number returns random assets or templates are chosen from the created assets table and a random amount of bonus tokens from the pack are also issued for the account opening the pack. All of these are added to a claim table and they then claimed in a deferred transaction. By claiming in a deferred transaction if there is a unexpected transaction failure during the claiming of assets (such as CPU timeout) the prepared assets from the random value processing will still be available for the user to claim as a separate blockchain transaction.

## Storage
* Packs table
    * name: packname
    * symbol: pack_symbol
    * extended_asset: bonus token
    * bool: active
* Cards table
    * uint64: card id
    * name: pack name
    * vector<cardprob>: card probabilities
* Crates table
    * name: crate name
    * name: type
    * vector<uint64>: ids
* Deposits table
    * name: account
    * asset: deposited asset
* Claims table
    * name: account
    * vector<chosen card>: chosen cards
    * asset: fungible token