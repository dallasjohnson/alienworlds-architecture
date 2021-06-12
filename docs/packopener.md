---
layout: default
---

# **PackOpener** smart contract:
## Blockchain account: `open.worlds`

The Pack opener contract provides a mechanism to create and transfer packs of NFTs without the actual contents of the packs been known before the packs have been opened. The contents of the packs can be pre-configured with crates of potential NFTs with assigned probabilities for each crate, but they would not be crystalised as actual NFTs until after the opening process, along with the further input of a random value. 
## Technical view of Permissions on chain
**-- Permission Name** - Requirements to satisfy  

**-- -- -- -- -- Child Permission Name** - Requirements to satisfy

**-- -- -- -- -- -- -- Linked to Contract::Action**

    owner     1:    1 EOS8TpackZ64RR3zx7FiB4LrMqp5fRS343AvitC2sn842hcxBGRXA
        active     1:    1 EOS8TpackZ64RR3zx7FiB4LrMqp5fRS343AvitC2sn842hcxBGRXA

    owner     1:    1 PUB_K1_88QRopenyixg1TzjmArDpspfKAeK2jmkCygARfFmRFipp8k9ra
        active    1:    1  PUB_K1_88QRopenyixg1TzjmArDpspfKAeK2jmkCygARfFmRFipp8k9ra
            claim 1:        1  open.worlds @eosio.code
                open.worlds::claim
            issue 1:        1  open.worlds @eosio.code
                alien.worlds::transfer
                atomicassets::mintasset
                atomicassets::transfer
            log 1:      1  open.worlds @eosio.code
                open.worlds::logopen
            random 1:       1  open.worlds @eosio.code
                orng.wax::requestrand



## Features

This smart contract manages packs of NFTs, including the selection and packing of NFTs into each pack as well as the opening to reveal the contents of each pack to the new pack owner.
### Packs 
A pack has a name, symbol and bonus token asset. Once a pack has been added, it can also be edited, deleted or activated.

__Pack related actions:__

* `addpack(name pack_name, symbol pack_symbol, extended_asset bonus_ft, bool active)` - Adds a new pack
  * requires auth `open.worlds@active`
* `editpack(name pack_name, symbol pack_symbol, extended_asset bonus_ft)`
  * requires auth `open.worlds@active`
* `activatepack(name pack_name, bool active)`
  * requires auth `open.worlds@active`
* `delpack(name pack_name)`
  * requires auth `open.worlds@active`
*  Cards - Each pack has cards associated with them that are assigned with {crate, probability} tuples. All the probabilities for each card added to a pack must add up to 100%. Once these cards are added to a pack their {crate, probability} values can be edited (as long as the total probabilities still totals to 100%. A card can also be deleted from a pack.
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
*  `receiverand(uint64_t assoc_id, checksum256 random_value)` When the random number returns, random assets or templates are chosen from the created assets table and a random amount of bonus tokens from the pack are also issued for the account opening the pack. All of these are added to a claim table and they can then be claimed in a deferred transaction. 
*  `claim(name account, name pack_name)` The claim action is called at the end of processing of the random returned value in a deferred transaction. If there is an unexpected transaction failure during the claiming of assets (such as CPU timeout) the prepared assets from the random value processing will still be available to be claimed as a separate blockchain transaction. If the claiming was performed as an inline action from within the `receiverand` both actions could potentially fail (or be forced to fail by the claim receiver) if they wanted to selectively choose which NFTs or bonus tokens they would be rewarded from the receiverand action. 

## Storage
* Packs table to store details of packs of cards that could be opened
    * name: packname
    * symbol: pack_symbol
    * extended_asset: bonus token
    * bool: active
* Cards table to store cards that could potentially be added to packs.
    * uint64: card id
    * name: pack name
    * vector<cardprob>: card probabilities
* Crates table to store the crates of NFTs that may be assigned to packs upon opening
    * name: crate name
    * name: type
    * vector<uint64>: ids
* Deposits table to store the packs that have been deposited which would start the opening process.
    * name: account
    * asset: deposited asset
* Claims table to store the NFTs that are the result of opening available for a user to claim
    * name: account
    * vector<chosen card>: chosen cards
    * asset: fungible token