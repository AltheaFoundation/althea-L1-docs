# Testnet 3 launch

Althea Testne 3 will be starting with a decentralized launch. In order to determine the genesis validators everyone needs generate and submit a genesis address. Both gentx and addresses will be submitted via github

```
wget https://github.com/althea-net/althea-chain/releases/download/v0.3.0/althea-v0.3.0-linux-amd64
althea init myvalidatorname
althea keys add --algo secp256k1 --coin-type 118 myvalidatorkey
```

Take the `althea1` address produced by this command and add it to the folder `validator-genesis-addresses/` in this repo. You can use Github's ui web-editor to do this without any cli commands.

Just follow [this link](https://github.com/althea-net/althea-chain-docs/tree/main/validator-genesis-addresses), sign into github and select the 'add file' button to create a file with the name as your address containing one line of your address

Key collection ends at 11:00am EST on Feb 9th, after that all validators who have added a key will need to create a gentx. Instructions will be here for that soon.