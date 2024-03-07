# Submitting a GenTX for Mainnet Dress rehersal

Welcome! This will be the final testnet before launch of Althea-L1. This is a dress rehersal meaning while the chain id isn't final *the addresses, keys, and balances are* obviously mistakes will be corrected since that's the point of a testnet. But you should secure your keys just like you would on mainnet.

## Download Althea L1

If you have a system architecture other than x86_64 Linux you will need to [grab the source code](https://github.com/althea-net/althea-l1) and build your own binary. But the vast majority of users should be able to download the release below.

```bash
wget https://github.com/althea-net/althea-L1/releases/download/v1.0.0-rc1/althea-linux-amd64
chmod +x althea-linux-amd64
sudo mv althea-linux-amd64 /usr/sbin/althea

althea version --long | head
```
version: v1.0.0-rc1
commit: 92f53e521ec93807d75769bcef3d8ed2dd7a11c8

## Init and download genesis Althea L1

althea init <put your validator name here> --chain-id=althea_417834-4
wget -O $HOME/.althea/config/genesis.json https://raw.githubusercontent.com/althea-net/althea-L1-docs/main/althea-l1-dress-rehersal-genesis.json

## Creating and submitting your GenTX

```bash
althea gentx <myvalidatorkeyname> <your amount here, remember to subtract for the fee>aalthea\
 --moniker=<put your validator name here> \
 --chain-id=althea_417834-4 \
 --commission-rate="0.10" \
 --commission-max-rate="0.20" \
 --commission-max-change-rate="0.01" \
 --min-self-delegation="1000000"
```

## Submitting your Gentx

Grab your gentx from `~/.althea/config/gentx/<filename>.json` and submit it as a Github pull request to this repo, specifically in the `gentx` folder. You should be able to do this entierly from the Github UI by clicking [this link](https://github.com/althea-net/althea-L1-docs/tree/main/gentxs) then clicking 'add file' -> 'upload files' and selecting your gentx file.

Once your pull request is opened it will be merged if the tests pass. The tests ensure the gentx signature is valid so that the chain can execute your gentx on startup.

## Launch!

Launch is set for 10am PST Thursday Mar 7, see you there!
