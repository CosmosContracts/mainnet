# Juno v14 - Aurora Upgrade

Juno v14 is a major release that adds many features such as FeeShare, Token Factory, Packet Forward Middleware, CosmWasm 0.30, and more.

Additional links:

- [The v14.0.0 changelog is viewable here](https://github.com/CosmosContracts/juno/releases/tag/v14.0.0).

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
