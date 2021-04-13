# Alien Worlds Architecture

## Game overview

The Alien Worlds gaming platform consists of a federation of planets. Each planet is governed as a DAC (Decentralised Autonomous Collective) where players of the game could participate in an election to appoint a board of custodians to govern each planet.

The game play is around the interplay between the planets with the central interaction from the Alien Worlds Federation. This interplay is facilitated with a common token for the whole federation called Telium (TLM) and planet-specific tokens that are used for token-weighted governance of each planet. A new planet can be created with the permission of the federation and then will start receiving rewards (in the form of Telium tokens and NFTs) for performing tasks (mining and staking) and challenges within the Alien Worlds universe. With these rewards, each planet can then govern itself (as a DAC) to maximise the wealth and growth of everyone involved with each particular planet.

Within the Alien Worlds universe, the Federation remains a central controller for the dispersion of TLM and centrally created Alien Worlds NFTs, creation of new planets and other master actions that apply to the governance of all planets.

As the Alien Worlds universe matures, further challenges and avenues to earn or win rewards will emerge from the Alien Worlds team and the community since everything is available for expansion running public blockchain technology.

## Technical Overview

The Alien Worlds platform has a decentralised architecture facilitated by blockchain technology - specifically using the Wax blockchain, with smart contracts written in C++, and Ethereum blockchain, with smart contracts written in Solidity. Using the two platforms allows for the platform to benefit from the superior throughput, performance and cheap blockchain actions available on WAX while at the same time being able to benefit from the additional industry exposure and trading liquidity available on Ethereum. While the critical application and value data is stored on underlying blockchains the users of the game will interact via web based user interfaces that are designed to abstract the blockchain complexity away from the end user. While this requires users to trust the intermediate web layer written by the Alien Worlds team, the underlying blockchain data is available for anyone to audit as all transactions are recorded of public blockchain ledgers (WAX and Ethereum). The core features of the platform are separated into multiple smart contracts. This allows for each to modified seprately which has the benefit of reducing the risk of causing damage through software update bug and also makes evolution of features easier since there is a loose coupling between features. Loose coupling between software components in any software system is historically a good practice to all for continual maintenance and evolution. Blockchain technology such as EOSIO (the underlying blockchain technology of WAX), has the added benefit of a extremely powerful and secure permissions mechansism built directly into the blockchain protocol which ensures that all the permissions to perform each of the smart contract actions, including updating the smart contract code and permissions themselves can be enforced with the utmost confidence.

## Smart Contracts

### WAX blockchain
[Federation](./federation.md)

[Mining](./mining.md)

[PackOpener](./packopener.md)

[Shining](./shining.md)

[Token Contract](./token.md)

### Ethereum blockchain

[Vesting Contract](./vesting.md)
