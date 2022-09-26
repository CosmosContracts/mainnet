# Juno v10 Upgrade

Juno v10 brings two important changes to Juno:

- Changes to the mint module
- Changes to the ICA configuration of the chain in order to enable liquid staking chains over ICA

Additional links:

- [The upgrade prop is viewable here](https://www.mintscan.io/juno/proposals/40).
- [The v10.0.1 changelog is viewable here](https://github.com/CosmosContracts/juno/releases/tag/v10.0.1).

The target block for this upgrade is [5004269](https://www.mintscan.io/juno/blocks/5004269), which is expected to arrive on _Wed Sep 28 2022 at 1400UTC_, +/- 1 hour.

These are the instructions you will need if you run cosmovisor:

```bash
cd juno
git fetch --tags && git checkout v10.0.1
make build && make install
# this will return commit fb767b18c07b8b8f1f073515abdd682bd12099d7
junod version --long

mkdir -p $DAEMON_HOME/cosmovisor/upgrades/v10/bin && cp $HOME/go/bin/junod $DAEMON_HOME/cosmovisor/upgrades/v10/bin
# this will return v10.0.1
$DAEMON_HOME/cosmovisor/upgrades/v10/bin/junod version
```

Alternatively, you can run the upgrade the old-fashioned way.
