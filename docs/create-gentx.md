# Submitting a GenTX for Mainnet

Welcome! This will be the launch of Althea-L1.

## Download Althea L1

THIS BINARY IS NOT THE FINAL LAUNCH BINARY. But you can use it to create a gentx. You will have to return to this document to upgrade before chain launch.

If you have a system architecture other than x86_64 Linux you will need to [grab the source code](https://github.com/althea-net/althea-l1) and build your own binary. But the vast majority of users should be able to download the release below.

```bash
wget https://github.com/althea-net/althea-L1/releases/download/v1.2.0/althea-linux-amd64
chmod +x althea-linux-amd64
sudo mv althea-linux-amd64 /usr/sbin/althea
```

The git commit hash for this binary is `2c0ab30a45097f7ffd10b719cec3bea8d376d828`

The binary hash is

```bash
c6c5149134601151bf22efcf0503ef7a7e316a98  althea-linux-amd64
```

## Init your environment and copy in the genesis

```bash
althea init myvalidatorname --chain-id=althea_258432-1
wget https://raw.githubusercontent.com/althea-net/althea-L1-docs/main/althea-l1-mainnet-genesis.json -O ~/.althea/config/genesis.json
```

## Creating and submitting your GenTX

```bash
althea gentx myvalidatorkeyname  \
 <your amount here, remember to subtract for the fee>aalthea\
 --moniker="put your validator name here" \
 --chain-id=althea_258432-1 \
 --commission-rate="0.10" \
 --commission-max-rate="0.20" \
 --commission-max-change-rate="0.01" \
 --min-self-delegation="1" \
 --fees 20000000000000000aalthea
```

## Submitting your Gentx

Grab your gentx from `~/.althea/config/gentx/<filename>.json` and submit it as a Github pull request to this repo, specifically in the `gentx` folder. You should be able to do this entierly from the Github UI by clicking [this link](https://github.com/althea-net/althea-L1-docs/tree/main/gentx) then clicking 'add file' -> 'upload files' and selecting your gentx file.

Once your pull request is opened it will be merged if the tests pass. The tests ensure the gentx signature is valid so that the chain can execute your gentx on startup.

## Launch!

Launch is set for 11am PST Thursday April 25, see you there!
