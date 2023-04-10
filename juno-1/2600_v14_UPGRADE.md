# Juno v14 - Aurora Upgrade

Additional links:

- [Proposal #282](https://www.mintscan.io/juno/proposals/282).
- [The v14.0.0 changelog is viewable here](https://github.com/CosmosContracts/juno/releases/tag/v14.0.0).
- [Features article](https://medium.com/@JunoNetwork/jun%C3%B8-aurora-ac67a8143e22).

The target block for this upgrade is [7875721](https://www.mintscan.io/juno/blocks/7875721), which is expected to arrive on _Mon April 17th 2023, at 1700UTC_, +/- 1 hour.

These are the instructions you will need if you run cosmovisor:

```bash
cd juno
git fetch --tags && git checkout v14.0.0
make build && make install
# this will return commit cd0e46c3e590a0416465a6f6054b8ba71327608a
junod version --long

mkdir -p $DAEMON_HOME/cosmovisor/upgrades/v14/bin && cp $HOME/go/bin/junod $DAEMON_HOME/cosmovisor/upgrades/v14/bin
# this will return v14.0.0
$DAEMON_HOME/cosmovisor/upgrades/v14/bin/junod version
```

Alternatively, you can run the upgrade the old-fashioned way.
