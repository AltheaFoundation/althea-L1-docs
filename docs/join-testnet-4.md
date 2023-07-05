# Joining Althea L1 Testnet 4

## Setting up your validator

Please see the [validator system setup](validator-system-setup.md) for recommended machine specs

Althea L1 testnet 4 has already launched, so this guide is for joining the already active testnet. The first setup is to setup the CLI

## Download Althea L1

If you have a system architecture other than x86_64 Linux you will need to [grab the source code](https://github.com/althea-net/althea-l1) and build your own binary. But the vast majority of users should be able to download the release below.

```bash
wget https://github.com/althea-net/althea-L1/releases/download/v0.5.4/althea-linux-amd64
chmod +x althea-linux-amd64
sudo mv althea-linux-amd64 /usr/sbin/althea
```

## Generate priv_validator_key.json

The output of this command will generate priv_validator_key.json, which generates a different output each time it is ran even if the same input is provided. If you lose this file you will not be able to regenerate it and you will have to start a new validator. The default save location for this file will be ~/.gravity/config/priv_validator_key.json

```bash
althea init mymoniker --chain-id althea_417834-2
```

## Download the genesis file

The genesis file represents the current state of the blockchain and allows your node to sync up
with the rest.

```bash
wget https://raw.githubusercontent.com/althea-net/althea-L1-docs/main/testnet-4-genesis-collected.json
cp testnet-4-genesis-collected.json $HOME/.althea/config/genesis.json

```

## Add seed node and persistent peers

Change the `persistent_peers` field in ~/.althea/config/config.toml to contain the following:

```text
persistent_peers = "72a7e729fbb2be68a39d50d2f9de18079da175c4@chainripper-2.althea.net:23296"
```

### Add your validator key

We need to import the validator key. This is the key containing Althea tokens, don't worry about getting them that comes next

```bash
# you will be prompted for your key phrase
althea keys add <my validator key name> --recover
```

Or if your key is stored in a ledger device.

```bash
althea keys add <my validator key name> --ledger
```

#### View your key (optional)

If you need to view the address of your validator operator key, you can do so with the following command: </br>

```bash
althea keys show <validator key name> --bech val
```

You should see an output like so:

```text
- name: <validator key name>
  ...
  address: altheavaloper<keystring>
  ...
```

## Setup Althea services

We recommend using systemd to manage your validator and orchestrator processes.

systemd makes it very easy to increase the open files limit for validators and ensure auto restart on failure.
You can run `althea start` without systemd, but if you do so be absolutely sure you have increased the system
open files limit.

```bash
sudo su
cd /etc/systemd/system
wget https://raw.githubusercontent.com/althea-net/althea-l1-docs/main/configs/althea.service
```

Now we have to stop and customize these services as appropriate

If you are running your validator as the `root` user, the `althea.service` file requires no modification
if you are running as a different user modify line 13 like so

```text
Environment="HOME=/path/to/your/home/dir"
```

Now that we have modified these services it's time to set them to run on startup

```bash
sudo systemctl daemon-reload
sudo systemctl enable althea
sudo service althea start
```

## Troubleshooting SELinux

If your services are not starting, you may want to try disabling it to see if it resolves the issue </br>

```bash
sed -i""  -e "s/SELINUX=enforcing/SELINUX=disabled/" /etc/selinux/config
```

```bash
setenforce 0
```

## Monitoring your logs

These lines will allow you to watch the logs coming out of your Althea full node as if you where directly attached to the process rather than using systemd.

```bash

journalctl -u althea.service -f --output cat

```

## Observe Sync Status and Time Remaining

You will need to wait for your Althea node to fully sync before progressing in the instructions </br>

You can issue the following command to check the sync status of the Gravity Node </br>

```bash
althea status 2>&1| jq .SyncInfo.catching_up
```

Value of 'false' means that it is now synced, 'true' means that sync is still in process.

## Getting some Althea tokens

Drop by the #validators channel in the [Althea discord](https://discord.com/invite/hHx7HxcycF)

post your althea1 validator address and recieve tokens to start your validator.

## Send your validator setup transaction

```bash

althea tx staking create-validator \
 --amount=975000000000000000000aalthea\
 --pubkey=$(althea tendermint show-validator) \
 --moniker="put your validator name here" \
 --chain-id=althea_417834-2 \
 --from=myvalidatorkeyname \
 --commission-rate="0.10" \
 --commission-max-rate="0.20" \
 --commission-max-change-rate="0.01" \
 --gas=auto \
 --min-self-delegation="1" \
 --gas-adjustment=1.4


```

## Confirm that you are validating

If you see one line in the response you are validating. If you don't see any output from this command you are not validating. Check that the last command ran successfully.

Be sure to replace 'my validator key name' with your actual key name. If you want to double check you can see all your keys with 'althea keys list'

```bash

althea query staking validator $(althea keys show myvalidatorkeyname --bech val --address)

```

## Congrats!

You have a gravity bridge validator setup and running. Checkout the [hackathon project index](https://dorahacks.io/hackathon/145) for any project you may want to participate in!
