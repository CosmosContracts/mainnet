# Juno v21 - Upgrade

Additional links:

- [Proposal #340](https://www.mintscan.io/juno/proposals/340).
- [The v21.0.0 changelog is viewable here](https://github.com/CosmosContracts/juno/releases/tag/v21.0.0).

The target block for this upgrade is [14556500](https://www.mintscan.io/juno/blocks/14556500), which is expected to arrive on _Mon March 11th, 2023 at 1430UTC_, +/- 1 hour.

These are the instructions you will need if you run cosmo-visor:

```bash
cd juno
git fetch --tags && git checkout v21.0.0
make build && make install

junod version --long | grep "cosmos_sdk_version\|commit\|version:"
# commit: e98863bf7112f4b117a2114e22f7482367362764
# cosmos_sdk_version: v0.47.6
# version: v21.0.0

mkdir -p $DAEMON_HOME/cosmovisor/upgrades/v21/bin && cp $HOME/go/bin/junod $DAEMON_HOME/cosmovisor/upgrades/v21/bin

$DAEMON_HOME/cosmovisor/upgrades/v21/bin/junod version
```

Alternatively, you can run the upgrade the old-fashioned way.
