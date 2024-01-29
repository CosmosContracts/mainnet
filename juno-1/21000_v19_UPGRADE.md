# Juno v19 - Rhea Upgrade

Additional links:

- [Proposal #325](https://www.mintscan.io/juno/proposals/333).
- [The v19.0.0 changelog is viewable here](https://github.com/CosmosContracts/juno/releases/tag/v19.0.0).

The target block for this upgrade is [13678871](https://www.mintscan.io/juno/blocks/13678871), which is expected to arrive on _Mon Feb 5th, 2023 at 1700UTC_, +/- 1 hour.

These are the instructions you will need if you run cosmo-visor:

```bash
cd juno
git fetch --tags && git checkout v19.0.0
make build && make install

junod version --long | grep "cosmos_sdk_version\|commit\|version:"
# commit: 46bc071f91225d2dcf06897d7b71f668f5508087
# cosmos_sdk_version: v0.47.6
# version: v19.0.0

mkdir -p $DAEMON_HOME/cosmovisor/upgrades/v19/bin && cp $HOME/go/bin/junod $DAEMON_HOME/cosmovisor/upgrades/v19/bin

$DAEMON_HOME/cosmovisor/upgrades/v19/bin/junod version
```

Alternatively, you can run the upgrade the old-fashioned way.
