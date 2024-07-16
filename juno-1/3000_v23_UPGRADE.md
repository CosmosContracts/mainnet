# Juno v23 - Upgrade

Additional links:

- [Proposal #347](https://www.mintscan.io/juno/proposals/352).
- [The v23.0.0 changelog is viewable here](https://github.com/CosmosContracts/juno/releases/tag/v23.0.0).

The target block for this upgrade is [18032000](https://www.mintscan.io/juno/blocks/18032000), which is expected to arrive on _Thursday July 18th, 2024 at 13:56:47 + 00:00 UTC

These are the instructions you will need if you run cosmovisor:

```bash
cd juno
git fetch --tags && git checkout v23.0.0
make build && make install

junod version --long | grep "cosmos_sdk_version\|commit\|version:"
# commit: bf140aa60045ba92b83d0cb7f3bc47a2661a4e7e
# cosmos_sdk_version: v0.47.12
# version: v23.0.0

mkdir -p $DAEMON_HOME/cosmovisor/upgrades/v23/bin && cp $HOME/go/bin/junod $DAEMON_HOME/cosmovisor/upgrades/v23/bin

$DAEMON_HOME/cosmovisor/upgrades/v23/bin/junod version
```

Alternatively, you can run the upgrade the old-fashioned way.