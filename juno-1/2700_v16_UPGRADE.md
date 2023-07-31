# Juno v16 - Invictus Upgrade

Additional links:

- [Proposal #TODO](https://www.mintscan.io/juno/proposals/TODO).
- [The v16.0.0 changelog is viewable here](https://github.com/CosmosContracts/juno/releases/tag/v16.0.0).

The target block for this upgrade is [TODO](https://www.mintscan.io/juno/blocks/TODO), which is expected to arrive on _Mon August 7th, 2023 at 1600UTC_, +/- 1 hour.

These are the instructions you will need if you run cosmo-visor:

```bash
cd juno
git fetch --tags && git checkout v16.0.0
make build && make install

junod version --long | grep "cosmos_sdk_version\|commit\|version:"
# commit: 054796f6173a9f15d012b656e255f94a4ec1d2cd
# cosmos_sdk_version: v0.47.3
# version: v16.0.0

mkdir -p $DAEMON_HOME/cosmovisor/upgrades/v16/bin && cp $HOME/go/bin/junod $DAEMON_HOME/cosmovisor/upgrades/v16/bin

$DAEMON_HOME/cosmovisor/upgrades/v15/bin/junod version
```

Alternatively, you can run the upgrade the old-fashioned way.
