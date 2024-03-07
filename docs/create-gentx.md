# Submitting a GenTX for Mainnet Dress rehersal

Welcome! This will be the final testnet before launch of Althea-L1. This is a dress rehearsal, meaning while the chain id isn't final _the addresses, keys, and balances are_. Obviously mistakes will be corrected since that's the point of a testnet. **You should secure your keys just like you would on mainnet**.

## Download Althea L1

If you have a system architecture other than x86_64 Linux you will need to [grab the source code](https://github.com/althea-net/althea-l1) and build your own binary. But the vast majority of users should be able to download the release below.

```bash
wget https://github.com/althea-net/althea-L1/releases/download/v1.0.0-rc1/althea-linux-amd64
chmod +x althea-linux-amd64
sudo mv althea-linux-amd64 /usr/sbin/althea
```

## Creating and submitting your GenTX

```bash
althea gentx \
 --amount=<your amount here, remember to subtract for the fee>aalthea\
 --pubkey=$(althea tendermint show-validator) \
 --moniker="<validator_name>" \
 --chain-id=althea_417834-4 \
 --from=<key_name> \
 --commission-rate="0.10" \
 --commission-max-rate="0.20" \
 --commission-max-change-rate="0.01" \
 --gas=auto \
 --min-self-delegation="1" \
 --gas-adjustment=1.4
 --fees 20000000000000000aalthea
```

## Submitting your Gentx

Grab your gentx from `~/.althea/config/gentx/<filename>.json` and submit it as a Github pull request to this repo, specifically in the `gentx` folder. You should be able to do this entierly from the Github UI by clicking [this link](https://github.com/althea-net/althea-L1-docs/tree/main/gentxs) then clicking 'add file' -> 'upload files' and selecting your gentx file.

Once your pull request is opened it will be merged if the tests pass. The tests ensure the gentx signature is valid so that the chain can execute your gentx on startup.

## Launch!

Launch is set for 10am PST Thursday Mar 7, see you there!
