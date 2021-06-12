---
layout: default
---

# **Teleport Solidity** smart contract:
## Ethereum blockchain account: `TeleportToken`

## Features

The Teleport contract facilitates the transfer of TLM tokens between the Wax and Ethereum blockchains. The contract code is compatible with the ERC-20 standard and has added functionality to facilitate the teleport Functionality from the Ethereum/BSC side of the teleporting that are otherwise beyond the ERC-20 standard.

Actions include:
* `totalSupply() returns (uint totalSupply)` - returns the total available supply for the token.
* `balanceOf(address tokenOwner) returns (uint balance)` - returns the balance for given address.
* `claim(bytes sigData, bytes[] signatures) public returns (address toAddress)` - This action is called by the receiver of TLM tokens on the Ethereum/BSC blockchain to claim the tokens once enough oracles have verified the transaction by adding their signatures to the teleport record.
* `teleport(string to, uint tokens, uint chainid) public returns (bool success)` - This action transfers tokens from the message sender to and EOSIO based `to` address on WAX. Internally the tokens are transferred to the inaccessible address in the contract and the oracles monitor this action offchain and then perform the corresponding transfer to the `to` account on the Wax blockchain.
* `transfer(address to, uint tokens)` - Transfers `tokens` from the message sender to the `to` address.
* `approve(address spender, uint tokens)` - Approves `spender` account to be able to transfer `tokens` to another address.
* `transferFrom(address from, address to, uint tokens)` - transfers `tokens` from the `from` account to the `to` address. The tokens must be pre-approved for the message sender to perform this transfer before this action can succeed.
* `updateThreshold(uint8 newThreshold) public onlyOwner returns (bool success)` - Updates the required threshold for the number of oracle signatures on a Teleport before it can be claimed. Requires the auth of the contract owner to run.
* `regOracle(address _newOracle) public onlyOwner` - registers a new oracle with the Eth token contract. This can only be performed by the contract owner.
* `unregOracle(address _remOracle) public onlyOwner` - removes a registered oracle with the Eth token contract. This can only be performed by the contract owner.


## Storage

* Allowed - Storage for the amount a tokens that is approved to be transfer from an address by another designated address
    * address: allowedSender
    * address: fromAddress
    * uint: allowedAmount
  
* Signed - Storage for which oracles have signed to approve a Teleport transaction
    * uint64: teleportId
    * address: oracle
    * bool: hasSigned

* Claimed - Storage to indicate which Teleports have been claimed by the receiver
    * uint64: teleportId
    * bool: hasBeenClaimed

* Balances
    * uint: balance
