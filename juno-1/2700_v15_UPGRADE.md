# Juno v15 - Tokenfactory Upgrade

Additional links:

- [Proposal #295](https://www.mintscan.io/juno/proposals/295).
- [The v15.0.0 changelog is viewable here](https://github.com/CosmosContracts/juno/releases/tag/v15.0.0).

The target block for this upgrade is [8577241](https://www.mintscan.io/juno/blocks/8577241), which is expected to arrive on _Mon June 5th 2023, at 1600UTC_, +/- 1 hour.

These are the instructions you will need if you run cosmovisor:

```bash
cd juno
git fetch --tags && git checkout v15.0.0
make build && make install

junod version --long | grep "cosmos_sdk_version\|commit\|version:"
# version: v15.0.0
# commit: f507f9c7856e523acb0d7b9c241b68225cb51d7c
# cosmos_sdk_version: v0.45.16

mkdir -p $DAEMON_HOME/cosmovisor/upgrades/v15/bin && cp $HOME/go/bin/junod $DAEMON_HOME/cosmovisor/upgrades/v15/bin

$DAEMON_HOME/cosmovisor/upgrades/v15/bin/junod version
```

Alternatively, you can run the upgrade the old-fashioned way.
