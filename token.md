# **Alien Worlds token** smart contract:
## Blockchain account: `alien.worlds`
## Permissions

    owner - requires 3 from: +1  aamir.worlds@active, +1  aarav.worlds@active, +1  advik.worlds@active, +1  anya.worlds@active
        active - requires 2 from: +1  aamir.worlds@active, +1  aarav.worlds@active, +1  advik.worlds@active, +1  anya.worlds@active


## Actions

This smart contract manages the fungible tokens related to Alien Worlds on the WAX blockchain. This is closely equivalent to the ERC20 contract on Ethereum based blockchains.

Features include:

* `create(name issuer, asset maximum_supply)` - creates a new token specifying the issuer, symbol and max supply.
* `issue(name to, asset quantity, string memo)` - This action creates and sends new tokens to the `to` account with the specified memo.
* `burn(name from, asset quantity, string memo)` - This action burns tokens from the `from` account with the specified memo. Must have auth of the `from` account.
* `transfer(name from, name to, asset quantity, string memo)` - This action transfers tokens to the `to` account from the `from` account with the auth of the `from` account.
* `open(name owner, symbol symbol, name ram_payer` - This action opens a record to store a balance for the `owner` account with the RAM to be paid by the `ram_payer`.
* `close(name owner, symbol symbol)` - This action is the opposite to the open action in order to free up RAM. The balance must be 0 for this to succeed.
* `addvesting(name account, time_point_sec vesting_start, uint32_t vesting_length, asset vesting_quantity);` - This action is for vesting tokens on behalf of a users account. This either creates a new vesting record of modifies an existing record It requires the auth of the active auth on this contract.

## Storage

* Accounts Table - table to hold all the token balances
    * asset: balance

* Currency Stats
    * asset: supply
    * asset: max_supply
    * name: issuer

* Vesting Table
    * name: account
    * time point: vesting_start
    * uint32: vesting_length
    * asset: quantity