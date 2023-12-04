# Juno v18 - Epona Upgrade

Additional links:

- [Proposal #325](https://www.mintscan.io/juno/proposals/325).
- [The v18.0.0 changelog is viewable here](https://github.com/CosmosContracts/juno/releases/tag/v18.0.0).

The target block for this upgrade is [12265007](https://www.mintscan.io/juno/blocks/12265007), which is expected to arrive on _Mon December 11th, 2023 at 1600UTC_, +/- 1 hour.

These are the instructions you will need if you run cosmo-visor:

```bash
cd juno
git fetch --tags && git checkout v18.0.0
make build && make install

junod version --long | grep "cosmos_sdk_version\|commit\|version:"
# commit: a56c4421081db13d06e12d3a1ba466ee7d8d5896
# cosmos_sdk_version: v0.47.5
# version: v18.0.0

mkdir -p $DAEMON_HOME/cosmovisor/upgrades/v18/bin && cp $HOME/go/bin/junod $DAEMON_HOME/cosmovisor/upgrades/v18/bin

$DAEMON_HOME/cosmovisor/upgrades/v18/bin/junod version
```

Alternatively, you can run the upgrade the old-fashioned way.
