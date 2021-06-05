# Juno Mainnet

Repository to keep track of Juno genesis files and validators GenTxs

# How to verify Genesis

## Requirements 
1. A synced cosmoshub-3 node with pruning default ([backup here](https://archive.interchain.io))
2. Latest [juno](https://github.com/CosmosContracts/Juno) binary 
3. Exchanges list JSON [file](./exchanges.json)

## Parameters
You can find a list of parameters in the following table `parameters.md`

## Procedure

### Export cosmoshub-3 snapshot

First we need to export state from the hub3 snapshot, if we have a synced node is quite simple with the following command

```
gaiad export --height 5200790 > cosmoshub3.json
```

a file named `cosmoshub3.json` will be generated containing the state of Cosmos Hub on block before the stargate upgrade. 

### Generate balances snapshot

Now we need to parse all the balances of cosmoshub 3 and generate a file with filtered accounts, to do so run the following command: 

```
junod export-airdrop-snapshot uatom cosmoshub3.json exchanges.json juno_out.json --juno-whalecap 50000000000
``` 

A new file `juno_out.json` containing all the airdropped balances will be generated. 

Some useful statistics will be printed in the command line: 

```
cosmos accounts: 78254
atomTotalSupply: 268335648775167
total staked atoms: 179033710700329
extra whale amounts: 118667082985153
total juno airdrop: 30663193590002
```

We will need this information to calculate multi-sig dev fund amount, incentivized testnet amount and community pool amount.

### Init the genesis file

Now we can start forging the genesis file, let's start using the traditional cosmos-sdk init command 

```
junod init <moniker> --chain-id juno-1
```

An empty genesis.json file will be created in your `.juno/config` directory. 

### Add airdrop accounts

Now we can add all the airdrop accounts generated in the `juno_out.json` file before, running the following command

```
junod add-airdrop-accounts juno_out.json ujuno
```

It may take from 30 minutes up to a couple of hours, depending on your system specs.

### Add community pool amount

Open the geneis.json file manually with your favorite text editor, and find the following section inside `app_data.distribution.fee_pool.community_pool`

```json
"fee_pool": {
    "community_pool": []
},
```

we need to add the community pool amount, so replace it with

```json
"fee_pool": {
    "community_pool": [{
        "denom": "ujuno",
        "amount": 20000000000000
    }]
}
```

### Add Multi Sig Amount

Use the following command to add multi-sig amount, that will include dev fund and incentivized testnet prize

```
junod add-genesis-account juno196jh55p8p65ekee3c6vqfdh2xtny2u8pkl67r7 17210834520000ujuno
```

### Compare SHA256 Sum

Now you can compare the SHA256 hash of the provieded genesis.json with yours.

```
$ sha256sum juno-1/genesis.json 
f88960195fe6f81d378fbb63801f57d8389568aa06c2d4652b0c7266c6f7fa68  juno-1/genesis.json


$ sha256sum juno-1/my-genesis.json 
f88960195fe6f81d378fbb63801f57d8389568aa06c2d4652b0c7266c6f7fa68  juno-1/my-genesis.json
```