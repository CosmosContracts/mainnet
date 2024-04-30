# Juno v22 - Upgrade

Additional links:

- [Proposal #347]([https://www.mintscan.io/juno/proposals/340](https://www.mintscan.io/juno/proposals/347)).
- [The v22.0.0 changelog is viewable here](https://github.com/CosmosContracts/juno/releases/tag/v22.0.0).

The target block for this upgrade is [15873890](https://www.mintscan.io/juno/blocks/15873890), which is expected to arrive on _Tuesday April 30th, 2024 at 1500UTC_, +/- 1 hour.

These are the instructions you will need if you run cosmo-visor:

```bash
cd juno
git fetch --tags && git checkout v22.0.0
make build && make install

junod version --long | grep "cosmos_sdk_version\|commit\|version:"
# commit: b0faafbd6df4bb03940d99df13030af7f7bc315b
# cosmos_sdk_version: v0.47.11-0.20240417094812-f556fd956fb1
# version: v22.0.0

mkdir -p $DAEMON_HOME/cosmovisor/upgrades/v22/bin && cp $HOME/go/bin/junod $DAEMON_HOME/cosmovisor/upgrades/v22/bin

$DAEMON_HOME/cosmovisor/upgrades/v22/bin/junod version
```

Alternatively, you can run the upgrade the old-fashioned way.
