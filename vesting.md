# **Vesting Solidity** smart contract:
## Ethereum blockchain account: `Vesting`

## Features

This smart contract facilitates the vesting of TLM tokens on the Ethereum blockchain. The contract code is compatible with the ERC-20 standard and has added functionality to facilitate vesting of tokens that are otherwise beyond the ERC-20 standard.

Actions include:

* `balanceOf(address tokenOwner) returns (uint balance)` - returns the balance for given address.
* `claim(uint256 amount)` - This action is for the user to claim back their vested tokens after they have been vested for sufficiently long time. It checks that the `amount` being claimed is both available from the `balance` for the user and that they have sufficient funds from the vested tokens that will be available to be claimed.
* `maxClaim(address user) returns (uint256)` - This action shows how much tokens are available to be claimed with the claim action.
* `setSchedule(address user, uint32 start, uint32 length, uint256 initial, uint256 amount)` - This action sets a vesting schedule for the provided user address. It can only be called by the contract owner.
* `addTokens(address newOwner, uint256 amount)` - This action adds a new `amount` of tokens to the `newOwner`'s balance with the amount being subtracted from the sender's balance. It can only be called by the contract owner.
* `removeTokens(address owner, uint256 amount)` - This action is the opposite to the `addTokens` action in order to remove tokens from an account. It can only be called by the contract owner.
* `transfer(address to, uint tokens)` - Always fails with an error since users should use `claim` instead.
* `approve(address spender, uint tokens)` - Always fails with an error since users should not be able to transfer at all.
* `transferFrom(address from, address to, uint tokens)` - Always fails with an error since users should use `claim` instead.

## Storage

* Schedules - Storage of vesting schedules for each user
    * uint32: start
    * uint32: length
    * uint256: initial
    * uint256: tokens

* Balances
    * uint256: balance
