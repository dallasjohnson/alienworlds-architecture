# **Federation** smart contract
## Blockchain account: `federation`
## Permissions
    
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
    
* `addplanet(name planet_name, string title, symbol dac_symbol, string metadata)` - Create planets
* `updateplanet(name planet_name, string title, string metadata, bool active)` - Update planets
* `removeplanet(name planet_name)` - Remove planets
* `setmap(name planet_name, uint16_t x, uint16_t y, uint64_t asset_id)` - Set a map point for the planet
* `setavatar(name account, uint64_t avatar_id)` - Set an avatar NFT for each user (either using a provided one or create a default one)
* `settag(name account, string tag)` - Set a tag for each user
* `miningstart(name receiver, uint32_t preset_id)` - Start mining (mint a shovel for the user)
* `setprofitshr(name owner, uint64_t land_id, uint16_t profit_share)` - Set profit share on land NFT
* `setlandnick(name owner, uint64_t land_id, string nickname)` - Set Nickname on land NFT
* Manage staking of TLM (Once TLM is staked to the Fed contract planet DAC tokens are issued and given in exchange)
    * `stake(name account, name planet_name, asset quantity)` - Stake to a particular planet, player should have also transferred the required amount of Trilium.
    * `withdraw(name account)` - Withdraws any deposited tokens
    * `refund(uint64_t id)` - refund unstaked tokens to the player
* Handle daily planet claims (weighted by staking and number of NFTs held by planet and in total)
    * `claim(name planet_name)` - Used by planets to claim their rewards based on staked Trillium
    * `logclaim(name planet_name, asset planet_quantity, asset mining_quantity)` log the claim action 
* `ogtransfer(name collection_name, name from, name to, vector<uint64_t> asset_ids, string memo)` - to keep a registry of all land owners based on NFT minting and transfers
* Fix total stake of each planet
* `miningnft(name receiver, name rarity, name planet_name)` - Mint NFTs from mining with time-weighted multiplier.
* `awardnft(name receiver, uint8_t randomValue)` - award NFTs based on game results.
* `agreeterms(name account, uint16_t terms_id, checksum256 terms_hash)` - Capture agreement from users to terms
* distribute Telium (TLM) tokens

## Storage

* State Singleton
    *  int64:  total stake
    *  uint32: nft genesis
    * uint64: nft total
    * timepointsec: last land fill
    * uint64: land rating total
* User terms table
    * name:         account
    * int16:        terms id
    * checksum256:  terms hash
* Planet table
    * name:         planet name
    * string:       title
    * string:       metadata
    * symbol:       dac_symbol
    * bool:	 active
    * int64:	total stake
    * int64:	nft multiplier
    * time point sec: last claim
* Land registry table
    * uint64: id;
    * name: owner
* Map Table
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
* Players table
    * name: account
    * uint64: avatar
    * string: tag