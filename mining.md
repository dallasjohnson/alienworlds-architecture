# **Mining** smart contract
## Blockchain account: `m.federation`
## Permissions

    owner - requires federation@active
        active - requires PUB_K1_8UNvtBz9Bs62Hoayf5HSaZMgfB26tEFMypjur2dfcHBR1GJTF1
            claim - requires m.federation@eosio.code
                    m.federation::claimnfts
            issue - requires m.federation@eosio.code
                    atomicassets::mintasset
                    atomicassets::setassetdata
            log - requires m.federation@eosio.code
                    m.federation::logmine
                    m.federation::logrand
            rando - requires PUB_K1_5rFHu5XjbCASyD7nARANDoXhQgfDJQBaGpvSPdTaAh7CQb3q6U
                    m.federation::receiverand
            random - requires m.federation@eosio.code
                    federation::miningnft
                    federation::miningstart
                    orng.wax::requestrand
            setparam - requires either eosusacardzz@airdrops or PUB_K1_7JUT21YpUHbgiEgpBQuCXGmzsmsWupuPWQV25kKxUyWyvq91KF
                    m.federation::setparam
                    m.federation::testparam
            xfer - requires m.federation@eosio.code
                    alien.worlds::transfer
                    federation::setprofitshr

## Actions

This smart contract is the mining process that users of the app perform to earn NFTs and Telium tokens. The actions include:
    
* `setparam(uint64_t key, uint64_t value)` - Creating and updating bots
* `testparam(name key)` - Testing a key matches an existing bot value
* Clear params (in batches of 50 to prevent timeout)
* `setbag(name account, vector<uint64_t> items)` - Set miner's tool bag (with a max of 3 tools)
* `setland(name account, uint64_t land_id)` - Set the current land to mine on for a given miner (mint a tool if the miner is new)
* `mine(name miner, vector<char> nonce)` - Mine action
    * miner must have preselected Land to mine on
    * the land must have something to mine (ease > 0)
    * land commission must be less than 25%
    * mining ease (when combined with land.ease and a global multiplier < 80%
    * mining delay (when combined with land.delay and a global multiplier) should have elapsed enough time from previous mine. Previous mining time is taken from the most recent of the `miner.prev_mine` time or the `last_time` of each the tools in the mining bag.
    * mining difficulty (from land difficulty) < 15
    * hash the {account}{time}{nonce} from miner and check the result has leading 0's. (easier if they have a `.wam` account.
    * if the miner as > 0 luck then request a random number for them to be able to get an NFT and lock their NFT bag.
    * Checks to see if the miner is a bot ( not sure what is happening here)
    * fill the mine bucket with TLM based on time since last mine and the fill rate.
    * separate the profit share from the mining reward.
    * send reward to miner
    * if landowner is not one the key alien worlds accounts send commission to the owner.
    * log the mining event results.
    * Update the mining times for tools in the current bag.
* `fill(name account, name planet_name)` - Updates the amount of available TLM that can be mined for a planet based on previously deposited TLM amount in the deposits table for this account.
* `procrand()` - as the mining actions occur random number requests accumulate in in the `randos` table so they can be batch processed more efficiently that one request per random number.
* `receiverand(uint64_t assoc_id, checksum256 random_value)` - handles the returned randomvalue to process the pending randos records in batches of up to 20 record.
    * For each record basic checks for game logic
    * the luck of each is adjusted (based on miner luck, land luck and if they are a bot.
    * if the luck is still > 0 more random numbers are derived to possibly mint a new NFT.
    * if a new NFT is created it is added to the pending claims for that account where they are claimed in a following deferred transaction. (deferred to save on CPU time processing).
    * The new minting is logged to the fed contract to be able to watch the action as an inline action.
* `setnfts(name rarity, vector<uint32_t> template_ids)` - Adds new NFTs to the mining bucket ready to be won through mining.

## Storage

* Miners table
    * name: miner
    * checksum256: last mine tx
    * time point sec: last mine
    * uint64: current land

* bag table
    * name: account
    * vector<uint64>: items
    * bool: locked

* deposit table
    * name: account
    * asset: quantity

* rando table - Random number request queue
    * uint64: id
    * name: account

* global values singleton
    * uint16: delay multiplier
    * uint16: luck multiplier
    * uint16: ease multiplier

* state singleton
    * time point: last fill time
    * double: fill rate
    * asset: bucket total
    * asset: mine bucket

* mining nft table - NFTs held for mining rewards
    * name: rarity
    * vector<uint32>: template ids

* guest nft table - NFTs held for guest rewards, given to all landholders
    * name: id
    * uint16: probability
    * vector<uint64>: asset ids

* claims table - Template ids that have been chosen, they must be claimed and minted in a separate tx so that the user cannot block the minting and get different nfts
    * name: miner
    * vector<uint32>: template ids
