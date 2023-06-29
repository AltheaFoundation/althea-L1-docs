# Building on Althea L1

Althea L1 is a hybrid blockchain built with the goal of enabling machine to machine micropayments. By 'hybrid' we mean that that the chain has two separate execution environments, one fixed format transactional environment and an EVM environment for programmable transactions. By isolating these environments Althea L1 

The 'hybrid' in this case comes from the divide between the [cosmos-sdk](https://github.com/cosmos/cosmos-sdk) base layer of the chain and the [EVM](https://github.com/althea-net/ethermint) environment provided by the ethermint module.

## RPC disambiguation

Becuase Althea L1 is a hybrid environment it has several different rpc endpoints dependening on the task and what part of the system you want to interact with. Staking and delegation can be done through the Cosmos-SDK endpoints, while any interaction with the EVM is done via the normal EVM rpc interface.

Public RPC is avaialble at https://althea.zone

    Port 443 / 8545 EVM RPC
    Port 8546 EVM Websockets
    Port 26657 Cosmos RPC + websockets
    Port 9090  Cosmos GRPC
    Port 1317  Cosmos Legacy API

## EVM Development

For development in the Althea L1 all Ethereum development and wallet tooling. Metamask, Hardhat, Ethersjs, etc can simply be pointed at the EVM RPC and used normally.

### Exactly how does the CosmosSDK layer interact with the EVM layer?

Althea token balances are shared between the EVM and CosmosSDK layer, any given private key has a EVM hex addr and a bech32 `althea1` addr that represent the same account and actually encode the same public key bytes. In order to convert between the two you can use `althea debug addr <address>` with either address format to get the other representation.

A common use of these shared balances would be to send form Althea token from your Cosmos account to a metamask account. Copy the 0x address from Metamask, feed it into `althea debug addr` and then send tokens to that `althea1` address. You'll find your Metamask balance updates to reflect the balance.

This sort of seamless linkage only works for the native token, ERC20 tokens from within the EVM or IBC tokens coming in from other chains require a command to enter or exit the EVM. 

### Liquid Infrastructure

Liquid infrastructure is a form of account abstraction tying together the micro transaction componenetns of Althea L1 with the EVM environment. The [MicroTX](https://github.com/althea-net/althea-L1/tree/main/x/microtx) Cosmos-SDK module 

## MicroTX Development

### Deep Space

### Althea bandwidth market

