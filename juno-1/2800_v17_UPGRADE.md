# Juno v17 - Vesta Upgrade

Additional links:

- [Proposal #317](https://www.mintscan.io/juno/proposals/317).
- [The v17.0.0 changelog is viewable here](https://github.com/CosmosContracts/juno/releases/tag/v17.0.0).

The target block for this upgrade is [10078449](https://www.mintscan.io/juno/blocks/10078449), which is expected to arrive on _Mon September 18th, 2023 at 1600UTC_, +/- 1 hour.

These are the instructions you will need if you run cosmo-visor:

```bash
cd juno
git fetch --tags && git checkout v17.0.0
make build && make install

junod version --long | grep "cosmos_sdk_version\|commit\|version:"
# commit: dc98d1f181601c73b543a406898c2ee02f042b90
# cosmos_sdk_version: v0.47.4
# version: v17.0.0 

mkdir -p $DAEMON_HOME/cosmovisor/upgrades/v17/bin && cp $HOME/go/bin/junod $DAEMON_HOME/cosmovisor/upgrades/v17/bin

$DAEMON_HOME/cosmovisor/upgrades/v17/bin/junod version
```

Alternatively, you can run the upgrade the old-fashioned way.
