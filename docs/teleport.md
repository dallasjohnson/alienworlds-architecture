---
layout: default
---

# **Teleport** smart contract:
## Blockchain account: `other.worlds`

The Teleport contract facilitates the transfer of TLM tokens between the Wax and Ethereum blockchains. 
All blockchains, at a base level, can only run as self-contained systems that are not able to interact with any external system (including other blockchains) without needing to trust specific data sources or oracles. Oracles could be utilised as a single data source providing a single trusted data feed or, preferably, multiple sources providing multiple data feeds that should agree on the same version of the truth. In order to transfer tokens between Wax and Ethereum, the same challenges apply and they can be overcome with oracles that monitor specific actions on one blockchain and write corresponding action to the other to signal the transfer of tokens.
This EOSIO C++ contract would be installed on the WAX blockchain and its features include capturing tokens on the EOSIO side and processing a `teleport` action which is then processed by trusted oracles that then send the equivalent TLM tokens on the Ethereum side. This process is also reversible to receive tokens from the Ethereum contract and send them out on the EOSIO side. To prevent faults and potential points of centralisation the contract ensures a minimum number of oracles has verified each cross-chain transaction before the tokens are available for the intended recipient on the receiving blockchain.

## Technical view of Permissions on chain
**-- Permission Name** - Requirements to satisfy  

**-- -- -- -- -- Child Permission Name** - Requirements to satisfy

   owner - requires federation @active
        active - requires either other.worlds@eosio.code or 
                            PUB_K1_6XXHdC1zFjHj7rNaibWUZMKVotherUzGcHJBHE1SsZqNKWvLWL


## Features

Tursted oracles must be registered with the teleport contract in order to participate in the trusted crosschain transfers. Once there are enough registered oracles. A user can transfer tokens to this contract's account (`other.worlds`) along with an assoicated `teleport` action defining the destination address on Ethereum. The oracles monitor for these teleport actions and use them to sign transactions onto Ethereum that will send the corresponding Ethereum version of the token to the recipient once enough oracles have agreed. Then the same happens in the reverse direction.

## Actions
* `regoracle(name oracle_name)` - registers a new oracle account that can be added to the list of trusted oracles within the contract.
  * requires auth `other.worlds@active`
* `unregoracle(name oracle_name)` - removes an existing oracle from the trusted oracles list.
  * requires auth `other.worlds@active`
* `transfer(name from, name to, asset quantity, string memo)` - this action is for notifications of transfers within the TLM token contract and is used to capture the movement of tokens that intended by the `from` account to be transferred to Ethereum.
  * requires auth `from`
*  `teleport(name from, asset quantity, uint8_t chain_id, checksum256 eth_address)` - each transfer call should have an associated `teleport` call detailing the Ethereum desitnation account for the transfer. This creates a teleport record that oracles monitor for the Ethereum side of the transfer.
   * requires auth `from`
*  `withdraw(name account, asset quantity)` - provides the ability for a user to withdraw tokens they had previously transferred but not yet run a `teleport` action for.
   * requires auth `from`

*  `logteleport(uint64_t id, uint32_t timestamp, name from, asset quantity, uint8_t chain_id, checksum256 eth_address)` - This action is triggered as an inline action from the teleport action so that it can be monitored by the oracles with the automatically generated teleport id and timestamp added.
     * requires auth of this contract code permission.
* `sign(name oracle_name, uint64_t id, string signature)` - This must run by oracles to acknowledge the receipt of tokens with a signature that must also be relayed to the Ethereum blockchain through the `claim` action on the Ethereum contract.
  * requries auth of a registered oracle
* `received(name oracle_name, name to, checksum256 ref, asset quantity, uint8_t chain_id, bool confirmed)` - ackowledges the receipt of tokens transferred from Ethereum to Wax. Once this action has been called a sufficient number of times from different oracles marking the transfer as confirmed for the same transaction details the asset `quantity` is transfered to the intended `to` recipient and the receipt is marked as `completed`.
  * requries auth of a registered oracle
* `claimed(name oracle_name, uint64_t id, checksum256 to_eth, asset quantity)` - Marks a Wax initiated teleport as claimed on the Ethereum side of the teleport action. 
  * requries auth of a registered oracle

## Storage
* Teleports table stores all the teleport actions initiated from the Wax chain including the amount, destination and the state of the transfers and oracle confirmations.
    * uint64_t: id
    * uint32_t: time
    * name: account
    * asset: quantity
    * int8_t: chain_id
    * checksum256: eth_address
    * vector<name>: oracles
    * vector<string>: signatures
    * bool: claimed
* Receipts table stores the details about teleport transfers into the Wax chain, including the amount, source of the teleport and state of the oracles approvals in the receipt.
    * uint64_t: id
    * time_point_sec: date
    * checksum256: ref
    * name: to
    * uint8_t: chain_id
    * uint8_t: confirmations
    * asset: quantity
    * vector<name>: approvers
    * bool: completed

* The Oracles table keeps a list of the known and trusted oracles that are permitted to validate teleport transfers.
    * name: account

* Deposits table to store the token assets that have been transferred to this contract for the purpose of teleporting to Ethereum. This should generally hold a temporary record since an associated `teleport` action should clear the related record here while running the teleport action after the correct amount has been verified.
    * name: account
    * asset: quantity
