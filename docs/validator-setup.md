
# Joining the Althea L1 validator set (*mainnet*)

This guide covers how to install Althea locally and create an Althea L1 *validator*. Before you begin, please see the [validator system setup](https://github.com/AltheaFoundation/althea-L1-docs/blob/main/docs/validator-system-setup.md) for recommended system specs.

If you encounter any difficulties, the [Althea Discord server](https://discord.com/invite/hHx7HxcycF) is a great resource for technical assistance.



## Download the Althea L1 binary

If you have a system architecture other than x86_64 Linux you will need to [grab the source code](https://github.com/althea-net/althea-l1) and build your own binary. Most users users should be able to download and use the release below.

Download the Althea L1 v1.3.0 binary:
```bash
wget https://github.com/AltheaFoundation/althea-L1/releases/download/v1.3.0/althea-linux-amd64
```

Download the Althea L1 v1.4.0 binary:
```bash
wget https://github.com/AltheaFoundation/althea-L1/releases/download/v1.4.0/althea-linux-amd64
```

**NOTE** v1.4.0 will **not sync beyond block 271**. You should therefore **begin with v1.3.0** and sync to this block. Once block 271 is reached, you will see various error messages in the logs, at which point you can stop your validator and replace the v1.3.0 binary with v1.4.0. If you are using state-sync or a snapshot, you may *skip this step and start with v1.4.0*.

After downloading the binary, add ``execute permissions`` and move send it to its new home.

```bash 
chmod +x althea-linux-amd64
sudo mv althea-linux-amd64 /usr/sbin/althea
``` 


## Generate *priv_validator_key.json*

This following command will generate *priv_validator_key.json* and creates a different output each time it is run, *even if the same input is provided*. If you lose this file **you will not be able to regenerate it and you will have to start a new validator.**

The default save location for this file is ``~/.althea/config/priv_validator_key.json``

## Download the genesis file

The genesis file represents the current state of the blockchain and allows your node to sync. After downloading the genesis file, rename it and move it to its appropriate folder.

```bash
wget https://raw.githubusercontent.com/AltheaFoundation/althea-L1-docs/refs/heads/main/althea-l1-mainnet-genesis.json
mv althea-l1-mainnet-genesis.json $HOME/.althea/config/genesis.json

```

## Add *seed node* and *persistent peers*

The next step is to update two parameters in the P2P options in ``config.toml``, which is located in ``~/.althea/config/``.

Navigate to the P2P Configuration options
``` 
#######################################################
###           P2P Configuration Options             ###
#######################################################
```

Change the ``persistent_peers`` to the following:
```
persistent_peers="bc47f3e8f9134a812462e793d8767ef7334c0119@chainripper-2.althea.net:23296"
```
Change the ``seeds`` parameter to the following:
```
seeds = "ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@seeds.polkachu.com:12456"
```

## Add or create your validator key

If you already have a an Althea address containing $ALTHEA that you will be using for your validator, import it:

```
althea keys add <validator key name> --recover
```
If your key is stored on a ledger device, add the ``--ledger`` flag:
```
althea keys add <my validator key name> -- ledger
```
To create a new validator key, skip the ``--recover`` flag:
```
althea keys add <name of your new validator key>
``` 
This will output your new address and mnemonic recovery phrase. **Make a note of the recovery phrase and store it in a safe and secure place**.

To view the *validator operator address* of your validator key, use this command:

```
althea keys show <validator key name> --bech val
```
This should output something like this:

```
VALOPER - validator operating address

- name: <VALIDATOR KEY NAME>
  type: local
  address: altheavaloperXXXXXXXXXXXXXXXXXXX
  pubkey: '{"@type":"/ethermint.crypto.v1.ethsecp256k1.PubKey","key":"XXXXXXXXXX"}'
  mnemonic: ""
  ```

## Setup Althea services

We recommend using *systemd* to manage your validator and orchestrator processes. 

Using *systemd* makes it very easy to increase the open files limit for validators and ensure auto-restart on failure. You can run althea start without *systemd*, but to do so **must first increase the system open files limit**.

```bash
cd /etc/systemd/system
sudo wget https://raw.githubusercontent.com/althea-net/althea-l1-docs/main/configs/althea.service
```
After navigating to the systemd/system folder and downloading the service file and enter the correct home path on ``line 13``. 
```
Environment="HOME=/path/to/your/home/dir"
```
If you are running your validator as the ``root`` user, you will not need to make any changes to the service file.

Now that we have modified these services it's time to set them to run on startup

```bash
sudo systemctl daemon-reload
sudo systemctl enable althea
sudo service althea start
```

### Troubleshooting SELinux
If your services are not starting, you may want to try disabling it to see if it resolves the issue.

```bash
sed -i""  -e "s/SELINUX=enforcing/SELINUX=disabled/" /etc/selinux/config
```
```bash
setenforce 0
```


## Monitoring your validator
These commands will allow you to view the real-time logs of your Althea node as though you where directly attached to the process, rather than using *systemd*.

```bash
journalctl -u althea.service -f --output cat
```

## Observe *sync status* and *time remaining*

Before proceeding any further, allow your Althea node to fully sync.

You can issue the following command to check the *sync status* of the Althea Node
```bash
althea status 2>&1| jq .SyncInfo.catching_up
```
If the command returns ``false`` your node is now completely synced. If the command returns ``true`` then it is still syncing and needs some more time.

## Send your validator setup transaction

Note: Creating a validator **requires at least 1 ALTHEA token** to fulfill the minimum delegation requirement. If tokens are not yet available on an exchange, you can visit the [Althea Discord server](https://discord.com/invite/hHx7HxcycF) and share your validator address to receive one.

When you have 1 ALTHEA or more in your wallet, run the following command to fire up your validator.
```bash
althea tx staking create-validator \
 --amount=<your amount here, remember to subtract for the fee>aalthea\
 --pubkey=$(althea tendermint show-validator) \
 --moniker="put your validator name here" \
 --chain-id=althea_258432-1 \
 --from=YourValidatorKeyName \
 --commission-rate="0.10" \
 --commission-max-rate="0.20" \
 --commission-max-change-rate="0.01" \
 --gas=300000 \
 --min-self-delegation="1" \
 --gas-adjustment=1.4
 --fees 30000000000000000aalthea
```
If the command is unsuccessful, note the error message. If it is a gas or fee error, increase the ``fees`` and ``gas`` flags as needed and run the command again.

## Confirm that your validator is validating
Run the following command to query your validator:
```bash
althea q staking validator $(althea keys show YOUR_VALIDATOR_KEY_NAME --bech val --address)
```
This should output information about your validator. If you don't see any output from this command, confirm that the command was executed successfully.

Be sure to replace YOUR_VALIDATOR_KEY_NAME with your actual key name. 

If you want to confirm your validator key name, you can see all your keys with ``althea keys list``



## Congratulations!
You have an Althea L1 validator setup and running!  Let's liquify some infrastructure!
