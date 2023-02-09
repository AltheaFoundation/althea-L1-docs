# Testnet 3 launch

Althea Testne 3 will be starting with a decentralized launch. In order to determine the genesis validators everyone needs generate and submit a genesis address. Both gentx and addresses will be submitted via github

## These instructions have updated, please follow them exactly! you can download or build v0.3.1

```
wget https://github.com/althea-net/althea-chain/releases/download/v0.3.1/althea-v0.3.1-linux-amd64
chmod +x althea-v0.3.1-linux-amd64
mv althea-v0.3.1-linux-amd64 /usr/bin/althea
wget https://raw.githubusercontent.com/althea-net/althea-chain-docs/main/testnet-3-genesis-collected-v2.json
althea tendermint unsafe-reset-all
mv testnet-3-genesis-collected-v2.json ~/.althea/config/genesis.json
althea start
```

Your validator will start and begin to try and find peers, you may want to configure some persistant peers or share seed addresses. You can follow these same steps if you did not get a gentx in just to start a node and join the validator set later
