# MicroTX module endpoints

The MicrotTX module has several query endpoints which will serve information about Liquid Infrastructure accounts and fees. These endpoints are defined as gRPC endpoints but are also RESTful over HTTP/2 via grpc-web.

## REST Endpoints

All of the endpoints only support GET requests, as secure transaction signing is not supported via REST by the CosmosSDK.

### /microtx/v1/params

Returns the current module Params. Accepts no query string parameters.

This endpoint is useful for querying the fees that will be added on all micro transactions, however there is also the microtx_fee endpoint which will calculate the expected fee for a given micro transaction amount.

### /microtx/v1/microtx_fee?amount=...

Returns the fee to be paid for a given micro transaction amount. Expects one query string parameter: amount.

This endpoint is helpful for understanding precisely how much of a fee is required to send a micro transaction during development. It is NOT recommended to rely on this endpoint for production fee estimation for two reasons:

1. The fee is automatically added on to any micro transaction, if a micro transaction succeeds then the mandatory fee was already deducted successfully.
1. The expected fee is calculable locally. Query the `/microtx/v1/params` endpoint to get the microtx_fee_basis_points value, and then multiply your micro transaction amount by the fee basis points, and divide by 10000 to obtain the mandatory fee.

### /microtx/v1/liquid_accounts

Returns all known Liquid Infrastructure accounts, including the Liquid Account address, the Liquid Account owner's address, and the address of the LiquidInfrastructureNFT contract which controls the Liquid Account's profits. Accepts no query string parameters.

### /microtx/v1/liquid_account?owner=...&account=...&nft=...

Returns information about particular Liquid Infrastructure Accounts. Requires at least one of the following query string parameters:

* owner: provide a bech32 or EVM hex address of the account which currently owns the LiquidInfrastructureNFT
* account: provide a bech32 address of the Liquid Infrastructure account
* nft: provide the EVM hex address of the LiquidInfrastructureNFT contract

Returns the Liquid Account address, the Liquid Account owner's address, and the address of the LiquidInfrastructureNFT contract which controls the Liquid Account's profits for each matching Liquid Account.

This endpoint is particularly useful for production monitoring of accounts, for Liquid Infrastructure owners who want to quickly locate their accounts and token contract addresses, and for detangling complicated ownership of Liquid Infrastructure accounts.

## gRPC Endpoints

To test out the gRPC endpoints, we recommend trying out a command line tool like [grpcurl](https://github.com/fullstorydev/grpcurl). The following gRPC endpoints all align with the REST endpoints above, and are the base implementation of those REST endpoints.

### microtx.v1.Query.Params

Returns the current module Params. Accepts no input. Returns a response containing [the Params object defined here](https://github.com/althea-net/althea-L1/blob/main/proto/microtx/v1/genesis.proto#L7)

This endpoint is useful for querying the fees that will be added on all micro transactions, however there is also the microtx_fee endpoint which will calculate the expected fee for a given micro transaction amount.

### microtx.v1.Query.MicrotxFee

Returns the fee to be paid for a given micro transaction amount. Expects a request with the `amount` value populated.

This endpoint is useful for querying the fees that will be added on all micro transactions, however there is also the microtx_fee endpoint which will calculate the expected fee for a given micro transaction amount.

This endpoint is helpful for understanding precisely how much of a fee is required to send a micro transaction during development. It is NOT recommended to rely on this endpoint for production fee estimation for two reasons:

1. The fee is automatically added on to any micro transaction, if a micro transaction succeeds then the mandatory fee was already deducted successfully.
1. The expected fee is calculable locally. Query the microtx.v1.Query.Params endpoint to get the microtx_fee_basis_points value, and then multiply your micro transaction amount by the fee basis points, and divide by 10000 to obtain the mandatory fee.

### microtx.v1.Query.LiquidAccounts

Returns all known Liquid Infrastructure accounts, including the Liquid Account address, the Liquid Account owner's address, and the address of the LiquidInfrastructureNFT contract which controls the Liquid Account's profits. Accepts no input.

### microtx.v1.Query.LiquidAccount

Returns information about particular Liquid Infrastructure Accounts. Requires at least one of the following values in the request input:

* owner: provide a bech32 or EVM hex address of the account which currently owns the LiquidInfrastructureNFT
* account: provide a bech32 address of the Liquid Infrastructure account
* nft: provide the EVM hex address of the LiquidInfrastructureNFT contract

Returns the Liquid Account address, the Liquid Account owner's address, and the address of the LiquidInfrastructureNFT contract which controls the Liquid Account's profits for each matching Liquid Account.

This endpoint is particularly useful for production monitoring of accounts, for Liquid Infrastructure owners who want to quickly locate their accounts and token contract addresses, and for detangling complicated ownership of Liquid Infrastructure accounts.
