# Submitting a GenTX for Mainnet Dress rehearsal

Welcome! This will be the final testnet before the launch of the Althea-L1 mainnet. Treat this as a dress rehearsal, meaning that while the chain ID isn't final, _the addresses, keys, and balances are_. Any mistakes will be corrected, as that's the purpose of a testnet. **You should secure your keys just as you would on the mainnet**.

## Download Althea L1

If you have a system architecture other than x86_64 Linux you will need to [grab the source code](https://github.com/althea-net/althea-l1) and build your own binary. But the vast majority of users should be able to download the release below.

**Download**

```bash
wget https://github.com/althea-net/althea-L1/releases/download/v1.0.0-rc1/althea-linux-amd64
chmod +x althea-linux-amd64
sudo mv althea-linux-amd64 /usr/sbin/althea
```

**Steps to build your own**

```bash
git clone https://github.com/althea-net/althea-L1/
cd althea-L1
git checkout v1.0.0-rc1
make install
```

## Creating your GenTX

You will need to have the `althea` binary installed and your keys imported. To easily find the amount of tokens you will have at genesis and subtract the fee please run the following command but be sure to change `wallet_address` with your wallet address.

```bash
amount=$(cat althea-l1-dress-rehersal-genesis.json | \
grep -A5 'wallet_address' | \
grep -m1 -A2 '"coins":' | \
awk '/"amount":/ {print $2}' | tr -d '",')
result=$(echo "$amount - 20000000000000000" | bc)
echo $result
```

Create your gentx with the following command, be sure to replace the `--amount` flag with the result from the previous command.

```bash
althea gentx <key_name> <amount>aalthea  \
--moniker="moniler" \
--chain-id=althea_417834-4 \
--commission-rate="0.10" \
--commission-max-rate="0.20" \
--commission-max-change-rate="0.01" \
--min-self-delegation="1" \
--pubkey=$(althea tendermint show-validator)
```

## Submitting your Gentx

Grab your gentx from `~/.althea/config/gentx/<filename>.json` and submit it as a Github pull request to this repo, specifically in the `althea-l1-docs/networks/althea-testnet-4/gentx` folder. Please remember to change the filename to your validator moniker `<validator_moniker>`.

```bash
cp ~/.althea/config/gentx/<filename>.json althea-l1-docs/networks/althea-testnet-4/gentx/<validator_moniker>.json

git add .
git commit -m "add gentx for <validator_moniker>"
gh pr create
```

You should be able to do this entirely from the Github UI by clicking [this link](https://github.com/althea-net/althea-L1-docs/tree/main/gentxs) then clicking 'add file' -> 'upload files' and selecting your gentx file.

Once your pull request is opened it will be merged if the tests pass. The tests ensure the gentx signature is valid so that the chain can execute your gentx on startup.

## Launch!

Launch is set for 10am PST Thursday Mar 7, see you there!
