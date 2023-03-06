# Juno v13 Upgrade

Juno v13 is a major release that adds many features such as FeeShare, Token Factory, Packet Forward Middleware, CosmWasm 0.30, and more.

Additional links:

- [The upgrade prop is viewable here](https://www.mintscan.io/juno/proposals/XXX).
- [The v13.0.0 changelog is viewable here](https://github.com/CosmosContracts/juno/releases/tag/v13.0.0).

The target block for this upgrade is [7374801](https://www.mintscan.io/juno/blocks/7374801), which is expected to arrive on _Mon Mar 13th 2023, at 1700UTC_, +/- 1 hour.

These are the instructions you will need if you run cosmovisor:

```bash
cd juno
git fetch --tags && git checkout v13.0.0
make build && make install
# this will return commit 6b69a3d2a9ef8bfca4bf9cb33fe53bf609aa63b6
junod version --long

mkdir -p $DAEMON_HOME/cosmovisor/upgrades/v13/bin && cp $HOME/go/bin/junod $DAEMON_HOME/cosmovisor/upgrades/v13/bin
# this will return v13.0.0
$DAEMON_HOME/cosmovisor/upgrades/v13/bin/junod version
```

Alternatively, you can run the upgrade the old-fashioned way.
