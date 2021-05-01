# **Federation** smart contract
## Blockchain account: `federation`

Each of the planets in the Alien Worlds federation contributes and competes in a wider ecosystem where each planet and member involved in each planet can win or earn Trilium tokens or NFTs as well as performing various exchange intereactions with their planet specific token. In order to govern the rules and common behaviour between all the planets the Federation performs some key roles to hold everything together, including:
* Managing the creation and updating of planets.
* Managing the admission of users into the ecosystem and associating them with a planet.
* Managing the creation and distribution of Trilium tokens to planets through mining rewards and inflation
* Managing land ownership on planets and the associated profit share from mining on that land.
* Managing the staking of Trilium tokens for staking rewards.

The permissions to perform the actions on this contract are customised to ensure that only the minimum amount of access has been granted for that particular action to succeed. For example a user staking can only be performed by the individual user account that is staking. Planet claiming rewards can only be performed a planet account rather than an indidual user. The adminstrative actions such as managing planets requires the simultaneous permssion of multiple users as detailed in the `active` permission below. This ensures a high level of security and prevents potential mallicious or accidental harm caused by un-authoriseds users of the system.
## Technical view of Permissions on chain
**-- Permission Name** - Requirements to satisfy  

**-- -- -- -- -- Child Permission Name** - Requirements to satisfy

**-- -- -- -- -- -- -- Linked to Contract::Action**
    
    owner - requires weight of 3 from +1 aamir.worlds@active, +1 aarav.worlds@active, +1 advik.worlds@active, +1 anya.worlds@active
        active - requires weight of 2 from +1 aamir.worlds@active, +1 aarav.worlds@active, +1 advik.worlds@active, +1 anya.worlds@active
            claim - requires PUB_K1_7sPaybfLLBb8asFuP4A9DDAKH1tku6gVbTxEF8eq5CEGib14eD
                federation::filllandpot
            issue - requires federation@eosio.code
                alien.worlds::issue
                atomicassets::mintasset
                atomicassets::setassetdata
                token.worlds::burn
                token.worlds::issue
                token.worlds::transfer
            log - requires federation@eosio.code
                federation::logclaim
            mint - requires PUB_K1_7Q7nKcbeEwzJCXngYW6Z1CwDG5M1fZmQgpminty18vNArVuH8A
                atomicassets::burnasset
                atomicassets::createtempl
                atomictoolsx::announcelink
                atomictoolsx::cancellink
                federation::addplanet
                federation::setmap
                federation::updateplanet
            refund - requires federation@eosio.code
                federation::refund
            xfer - requires federation@eosio.code
                alien.worlds::transfer
                m.federation::fill

## Actions
	
This smart contract is responsible for many of the admin features relevant to all of the planets including:
    
* `addplanet(name planet_name, string title, symbol dac_symbol, string metadata)` - Create a new planet in the Alien Worlds federation.
    * requires auth: `federation@mint`
* `updateplanet(name planet_name, string title, string metadata, bool active)` - Updates metadata or active for a planet.
    * requires auth: `federation@mint`
* `removeplanet(name planet_name)` - Removes a planet from the federation
    * requires auth: `federation@active`
* `setmap(name planet_name, uint16_t x, uint16_t y, uint64_t asset_id)` - Set a map point for the planet
    * requires auth: `federation@mint`
* `setavatar(name account, uint64_t avatar_id)` - Set an avatar NFT for a user (either using a provided one or create a default one if the user is new)
    * requires auth: `account@active`
* `settag(name account, string tag)` - Set a tag (string less than 19 characters long) for a user.
    * requires auth: `account@active`
* `miningstart(name receiver, uint32_t preset_id)` - Start mining from the Mining contract. mint a standard shovel for a new user(receiver) to the game.
    * requires auth: `m.federation@random`
* `setprofitshr(name owner, uint64_t land_id, uint16_t profit_share)` - Set profit share on land NFT as set by the owner of the land NFT only.
    * requires auth: `owner@active`
* `setlandnick(name owner, uint64_t land_id, string nickname)` - Set Nickname on land NFT
    * requires auth: `m.federation@active`

Manage staking of TLM (Once TLM is staked to the Fed contract planet DAC tokens are issued and given in exchange)
* `stake(name account, name planet_name, asset quantity)` - Stake to a particular planet, `account` should have also transferred the required amount of Trilium to this account before this action is called. Mints planet related tokens in exchange for the staking at the planet DAC token exchange rate. The transfer and staking could be called together in one EOSIO transaction to allow complete transaction rollback on any failure in either action.
    * requires auth: `account@active`
* `withdraw(name account)` - Withdraws any deposited tokens that have not yet been exchanged for planet DAC tokens for the given account.
    * requires auth of any valid account
* `refund(uint64_t id)` - refund unstaked tokens to the player
    * requires auth of any valid account

Handle daily planet claims (weighted by staking and number of NFTs held by planet and in total)
* `claim(name planet_name)` - Used by planets to claim their rewards based on staked Trillium and NFTs held with the planet. Also ensure enough is held aside for a daily claim amount for Binance separately from all other planets.
  * requires auth: `planet_name@active`
* `logclaim(name planet_name, asset planet_quantity, asset mining_quantity)` log the claim action as an inline action for logging off-chain.
  * requires auth: `federation@log`
* `logtransfer(name collection_name, name from, name to, vector<uint64_t> asset_ids, string memo)` - to keep a registry of all land owners based on NFT minting and transfers
  * requires auth: `atomicassets` as nft transfer action listener only
* `fixstake(name planet_name, int64_t total_stake)` - Fix total stake of my checking the staked amount staked by each planet.
  * requires auth: `federation@active`
* `miningnft(name receiver, name rarity, name planet_name)` - Update a NFT related time-weighted multiplier for the related planet which is then used as part of the mining calculation.
  * requires auth: `m.federation@random`
* `awardnft(name receiver, uint8_t randomValue)` - award NFTs based on game results.
  * requires auth: WIP
* `agreeterms(name account, uint16_t terms_id, checksum256 terms_hash)` - Capture agreement from users to terms
  * requires auth: `account@active`
* `filllandpot` - distribute daily Trilium (TLM) tokens to land owners pot. WIP
  * requires auth `federation@claim`

## Storage
This contract stores the state relevent to all the planets, Land ownership, users and token staking.

* State Singleton to store some global values that are needed by various contract actions.
    *  int64:  total stake
    *  uint32: nft genesis
    * uint64: nft total
    * timepointsec: last land fill
    * uint64: land rating total
* User terms table to store which versions of the terms and conditions each user has agreed to.
    * name:         account
    * int16:        terms id
    * checksum256:  terms hash
* Planet table to store planet specific global values used for various actions involving specif planets.
    * name:         planet name
    * string:       title
    * string:       metadata
    * symbol:       dac_symbol
    * bool:	 active
    * int64:	total stake
    * int64:	nft multiplier
    * time point sec: last claim
* Land registry table to store the ownership of different land parcels used for mining profit shares.
    * uint64: id;
    * name: owner
* Map Table to store details of NFTs as linked to map coordinates.
    * uint16: x
    * uint16: y
    * uint64: asset_id
* Deposits table - Temporary store of deposits during staking process.
    * name:  account
    * asset: quantity
* Refunds table - Holds refunds during the unstaking process
    * uint64: id;
    * name: account;
    * asset: quantity;
    * time point sec: refund time;
    * name: planet name
* Players table to store common details about all players.
    * name: account
    * uint64: avatar
    * string: tag