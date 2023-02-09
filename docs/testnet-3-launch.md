# Testnet 3 launch

Althea Testne 3 will be starting with a decentralized launch. In order to determine the genesis validators everyone needs generate and submit a genesis address. Both gentx and addresses will be submitted via github

## These instructions have updated, people follow them exactly! you can download or build v0.3.1

```
wget https://github.com/althea-net/althea-chain/releases/download/v0.3.1/althea-v0.3.1-linux-amd64
chmod +x althea-v0.3.1-linux-amd64
mv althea-v0.3.1-linux-amd64 /usr/bin/althea
wget https://raw.githubusercontent.com/althea-net/althea-chain-docs/main/testnet-3-genesis.json
althea unsafe-reset-all
mv testnet-3-genesis.json ~/.althea/config/genesis.json
althea gentx yourkeyname 1033000000000000000000ualthea --chain-id althea-testnet-3
```

The gentx command will print the location of the genesis file it has generated, click [this link](https://github.com/althea-net/althea-chain-docs/tree/main/gentxs) and select 'add file -> upload files' then upload the gentx file.
