# Junø - Mainnet

_Planned Start Time: October 1st at 15:00 UTC._

Please have your gentx submitted by Wednesday, September 29th, 10pm UTC if you wish to participate.

You will need to have received Juno in the airdrop to successfully submit a gentx.

## Setup

**Prerequisites:** Make sure to have [Golang >=1.17](https://golang.org/).

#### Build from source

You need to ensure your gopath configuration is correct. If the following **'make'** step does not work then you might have to add these lines to your .profile or .zshrc in the users home folder:

```
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
```

```sh
git clone https://github.com/CosmosContracts/Juno
cd Juno
make build && make install
```

This will build and install `junod` binary into `$GOBIN`.

Note: When building from source, it is important to have your `$GOPATH` set correctly. When in doubt, the following should do:

```sh
mkdir ~/go
export GOPATH=~/go
```

### Minimum hardware requirements

- 2GB RAM
- 25GB of disk space
- 1.4 GHz amd64 CPU

## Setup validator node

Below are the instructions to generate & submit your genesis transaction

### Generate genesis transaction (gentx)

This testnet is a simulation of Juno mainnet. Similar to Osmosis, only nodes that received the airdrop will be able to validate. Others will be able to join the validator set at a later date.

1. Initialize the Juno directories and create the local genesis file with the correct
   chain-id

   ```bash
   junod init <moniker-name> --chain-id=juno-1
   ```

2. Create a local key pair (you should use the same key associated with you airdropped account)

   ```sh
   > junod keys add <key-name>
   ```

   Note: since this testnet involves using the same Cosmos Hub key that you received your airdrop with, you may wish to use an offline key. Do this with `junod keys add <key-name> --pubkey <your-pubkey>`. For the rest of the transactions, you will use the `--generate-only` flag and sign them offline with `junod tx sign`.

3. Download the pre-genesis file:

   ```sh
   curl -s  https://raw.githubusercontent.com/CosmosContracts/mainnet/main/juno-1/pre-genesis.json >~/.juno/config/genesis.json
   ```

   Find your account in the `juno-1/pre-genesis.json` file. The balance of your airdrop is what you'll be able to use with your validator.

4. Create the gentx:

   ```bash
   junod gentx <key-name> <balance>ujuno --chain-id=juno-1
   ```

   If all goes well, you will see a message similar to the following:

   ```bash
   Genesis transaction written to "/home/user/.juno/config/gentx/gentx-******.json"
   ```

### Submit genesis transaction

- Fork [the testnets repo](https://github.com/CosmosContracts/testnets) into your Github account

- Clone your repo using

  ```bash
  git clone https://github.com/<your-github-username>/testnets
  ```

- Copy the generated gentx json file to `<repo_path>/juno-1/gentx/`

  ```sh
  > cd testnets
  > cp ~/.juno/config/gentx/gentx*.json ./juno-1/gentx/
  ```

- Commit and push to your repo
- Create a PR onto https://github.com/CosmosContracts/testnets
- Only PRs from individuals / groups with a history successfully running nodes will be accepted. This is to ensure the network successfully starts on time.

#### Running in production

**Note, we'll be going through some upgrades soon after Juno mainnet. Consider using [Cosmovisor](https://github.com/cosmos/cosmos-sdk/tree/master/cosmovisor) to make your life easier.**

Download Genesis file when the time is right. Put it in your `/home/user/.juno` folder.

Create a systemd file for your Juno service:

```sh
sudo vi /etc/systemd/system/junod.service
```

Copy and paste the following and update `<YOUR_USERNAME>`, `<GO_WORKSPACE>`, and `<CHAIN_ID>`:

```sh
Description=Juno daemon
After=network-online.target

[Service]
User=root
ExecStart=/home/<YOUR_USERNAME>/<GO_WORKSPACE>/go/bin/junod start --p2p.laddr tcp://0.0.0.0:26656 --home /home/<YOUR_USERNAME>/.juno
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
```

2
**This assumes `$HOME/go_workspace` to be your Go workspace, and `$HOME/.juno` to be your directory for config and data. Your actual directory locations may vary.**

Enable and start the new service:

```sh
sudo systemctl enable junod
sudo systemctl start junod
```

Check status:

```sh
junod status
```

Check logs:

```sh
journalctl -u junod -f
```

### Learn more

- [Starport](https://github.com/tendermint/starport)
- [SPN](https://github.com/tendermint/spn)
- [Cosmos Network](https://cosmos.network)
- [Cosmos Community Discord](https://discord.com/invite/W8trcGV) (check out the #starport channel)
