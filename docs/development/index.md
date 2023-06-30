# Building on Althea L1

Althea L1 is a hybrid blockchain built with the goal of enabling machine to machine micropayments. By 'hybrid' we mean that that the chain has two separate execution environments, one fixed format transactional environment and an EVM environment for programmable transactions. By isolating these environments Althea L1 **TODO: FINISH THIS SENTENCE**

The 'hybrid' in this case comes from the divide between the [cosmos-sdk](https://github.com/cosmos/cosmos-sdk) base layer of the chain and the [EVM](https://github.com/althea-net/ethermint) environment provided by the ethermint module.

## RPC disambiguation

Because Althea L1 is a hybrid environment it has several different rpc endpoints dependeing on the task and what part of the system you want to interact with. Staking and delegation can be done through the Cosmos-SDK endpoints, while any interaction with the EVM is done via the normal EVM rpc interface.

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

A common use of these shared balances would be to send some Althea token from your Cosmos account to a metamask account. Copy the 0x address from Metamask, feed it into `althea debug addr` and then send tokens to that `althea1` address. You'll find your Metamask balance updates to reflect the balance.

This sort of seamless linkage only works for the native token, ERC20 tokens from within the EVM or IBC tokens coming in from other chains require a command to enter or exit the EVM. 

## Liquid Infrastructure

Liquid infrastructure is a form of account abstraction tying together the micro transaction components of Althea L1 with the EVM environment. The [MicroTX](https://github.com/althea-net/althea-L1/tree/main/x/microtx) Cosmos-SDK module defines a method of Tokenizing an account so that a configurable amount of token balances are automatically redirected under the control of an [NFT](https://github.com/althea-net/althea-L1/tree/main/solidity/contracts/TokenizedAccountNFT.sol). Once an account has been Tokenized, the NFT may be traded as normal within the Althea-L1 EVM environment and the current owner will have control of accrued balances. It is expected that the Tokenized accounts are acting as the wallet for a piece of infrastructure. Tokenizing this infrastructure can make collecting profits more convenient, and also makes the infrastructure much more liquid.

## MicroTX Development
### Account setup
To get started developing with the MicroTX module, one first needs to create and fund an account, in particular an EVM compatible account. To do so, run `althea keys add <keyname>` and look for output like the following:

```
- name: example
  type: local
  address: althea14jjatu7u3h07sgyfqv2r2z79qlmy5lrw27asjm
  pubkey: '{"@type":"/ethermint.crypto.v1.ethsecp256k1.PubKey","key":"AlcADKCujbGChCwIE0IVLzhSp3AG2Q8rmxG/aqRjqZ/L"}'
  mnemonic: ""

**Important** write this mnemonic phrase in a safe place.
It is the only way to recover your account if you ever forget your password.

<24 word mnemonic phrase>
```
Pay particular attention to the line that says `pubkey: '{"@type":"/ethermint.crypto.v1.ethsecp256k1.PubKey"`, if a different type of pubkey is output (e.g. "/cosmos.crypto.secp256k1.PubKey") then the account **will not work with the MicroTX module**.

It is also possible to import an existing Ethereum-compatible key using `althea keys unsafe-import-eth-key`, use the --help flag in the command line for more information. In this case using `althea debug addr <address>` can be used to obtain an `althea1...` address usable with the althea CLI, however you may need to omit the 0x prefix of the hexadecimal address.
### Obtaining account info
It is recommended to at a minimum obtain and save the Ethereum-style address for the account you just created by running `althea debug addr <address>` and copying the value output with `Address (hex): ...`. This hexadecimal value likely is missing the familiar 0x prefix.

Using an account which has funds, send some Althea token to the `althea1...` address using the `althea tx bank send` command OR use a standard EVM transaction to send the native token to the `0x...` address. Either way your balance should update in both environments simultaneously.

### Identifying a suitable test token
The MicroTX module **only** works with tokens registered to work in both the EVM and Cosmos SDK environments. To identify such a token run `althea q erc20 token-pairs`. Note that the `token-pair` subcommand exists to get more information about a registered token. If no tokens appear, see the [Registering Tokens](#registering-tokens) section below.

It may be necessary to ask kind members in the Discord for help obtaining these tokens, however deploying and registering your own ERC20 is always a great option.

### Making payments with micro transactions
The MicroTX module makes use of the MsgMicrotx to facilitate machine to machine payments. To submit such a payment via CLI, run `althea tx microtx microtx [sender] [receiver] [amounts...]` to send one or multiple `amounts` from `sender` to `receiver`. It is also possible to submit these transactions programattically via Rust with [deep_space](https://github.com/althea-net/deep_space/blob/master/src/client/send.rs#L336-L393) using the `contact.send_microtx()` function. Note that Althea-L1 only supports the latest version of the deep_space crate for such payments so make sure you update your crate!

### Tokenizing accounts
Once a suitable account is created, it may be Tokenized by submitting a MsgTokenizeAccount. When an account is Tokenized, the MicroTX module will deploy a new TokenizedAccountNFT contract within the EVM owned by the account in question. Additionally, the MicroTX module will update its metadata map it uses to keep track of all Tokenized accounts. Conveniently, this metadata map is exposed to users via gRPC and the command line.

Using the private key of the account you want to tokenize, run `althea tx microtx tokenize-account --from <keyname>`. Note that this transaction takes a lot of gas, you may want to provide the `--gas 1000000` flag to ensure the transaction succeeds. Once the transaction is successful, you can query for important information using the following command:
```
$ althea q microtx tokenized-account --account <althea1... address>
accounts:
- nft_address: 0x<hex>
  owner: <althea1... address>
  tokenized_account: <althea1... address>
```
This query should indicate that the account is the current owner, and a new TokenizedAccountNFT has been deployed to manage the account's balances. As it is, this NFT will not receive any balances because it has not been configured yet.

### Transferring ownership
The TokenizedAccountNFT contract is built on top of the standard [OpenZeppelin ERC721](https://github.com/OpenZeppelin/openzeppelin-contracts/tree/master/contracts/token/ERC721) contract which controls a single token, the Account token with ID = 1. As such, it may be transferred in the ordinary ERC721 fashion. The TokenizedAccountNFT contract also has several protected functions built on top of ERC721, see [NFT Contract Details](#nft-contract-details) for more information.

### Configuring working balance thresholds
Commonly, Liquid Infrastructure accounts need a working balance of tokens to keep up with their work. However, it is common that accounts never need more than ~100 USD worth of tokens to perform their function. To ensure that your newly-Tokenized account only keeps a certain balance on-hand, it is possible to configure ERC20 balance thresholds. Once an account receives a micro transaction that puts its balances over the configured threshold, the excess balance will be deposited in the registered NFT contract.

To configure the thresholds, [call the `setThresholds()` function](https://github.com/althea-net/althea-L1/tree/main/solidity/contracts/TokenizedAccountNFT.sol#L73-L99), passing each ERC20 and its respective balance threshold that the Tokenized account should not exceed. For example the arguments should be arranged as `[address1, address2], [threshold for address1, threshold for address2]`. Note that this function is access controlled, so make sure whatever private key you use to call `setThresholds()` is either the owner or has been approved for the Account token (ID = 1).

The current thresholds can be queried [using the `getThresholds()` function](https://github.com/althea-net/althea-L1/tree/main/solidity/contracts/TokenizedAccountNFT.sol#L61-L71), which does not manipulate state but should only be called via `eth_call` to avoid wasting tokens.

### Withdrawing token balances
Try to exceed your account's configured thresholds by submitting micro transactions and watch tokens flow in to the NFT contract. Only the tokens set in `setThresholds()` will have excess balances redirected to the NFT contract.

To withdraw these balances, call the [`withdrawBalances()`](https://github.com/althea-net/althea-L1/tree/main/solidity/contracts/TokenizedAccountNFT.sol#L101-L112) function to send them to the owner or the [`withdrawBalancesTo()`](https://github.com/althea-net/althea-L1/tree/main/solidity/contracts/TokenizedAccountNFT.sol#L127-L143) function to send them to another address. Note that these functions are access controlled, so make sure whatever private key you use to call these functions is either the owner or has been approved for the Account token (ID = 1).

## Deep Space

## Althea bandwidth market

## Registering Tokens
The [ERC20](https://github.com/Canto-Network/Canto/tree/main/x/erc20) module handles converting between Cosmos SDK Coins and ERC20's in the EVM. Registering tokens for conversion requires a governance vote, which can be initiated using one of two commands:

* Originally Cosmos SDK / IBC Coins: `althea tx gov submit-proposal register-coin`
    * It's necessary to provide metadata information about the token so that the EVM side is aware of things like decimals and symbols
* Originally EVM ERC20 Tokens: `althea tx gov submit-proposal register-erc20 <contract-address>`

Once either command has been run and in the event the proposal passes governance, a representation of the token will be created in the opposite environment. Use the `althea q erc20 token-pairs` command to discover the denom/contract address needed for use.

To actually use these tokens, one can either send the ERC20 to the ERC20 module's address (run `althea q auth module-account erc20` to get it) in the EVM or can run the `althea tx erc20 convert-coin` (or `convert-erc20`) commands to swap the tokens between the envionments.

## NFT Contract Details
Liquid Infrastructure's Tokenized accounts make use of the [TokenizedAccountNFT contract](https://github.com/althea-net/althea-L1/tree/main/solidity/contracts/TokenizedAccountNFT.sol) to control their working balances.

This contract uses a helper [OwnableApprovableERC721 contract](https://github.com/althea-net/althea-L1/tree/main/solidity/contracts/OwnableApprovableERC721.sol) to implement access control over its functions. The helper contract defines two [function modifiers](https://docs.soliditylang.org/en/latest/contracts.html#function-modifiers).

The importaint detail here is that `recoverAccount()` (not implemented in the testnet) is protected so that only the owner of the NFT can call it, while the `setThresholds()`, `withdrawBalances()` and `withdrawBalancesTo()` functions can be called by the owner or anyone approved to operate the Account token.