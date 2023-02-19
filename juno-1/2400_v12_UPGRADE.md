# Juno v12 Upgrade

We have decided that the original slated v12 release should be pushed to v13 to allow for some more testing.

Juno v12 is a minor change that adds bug fixes and version bumps, and _crucially_ a fix to stop governance spam.

Additional links:

- [The upgrade prop is viewable here](https://www.mintscan.io/juno/proposals/249).
- [The v12.0.0 changelog is viewable here](https://github.com/CosmosContracts/juno/releases/tag/v12.0.0).

The target block for this upgrade is [7075551](https://www.mintscan.io/juno/blocks/7075551), which is expected to arrive on _Mon Feb 20th 2023, at 1700UTC_, +/- 1 hour.

These are the instructions you will need if you run cosmovisor:

```bash
cd juno
git fetch --tags && git checkout v12.0.0
make build && make install
# this will return commit c5286524143747a3a6df42ef5095a588b78df734
junod version --long

mkdir -p $DAEMON_HOME/cosmovisor/upgrades/v12/bin && cp $HOME/go/bin/junod $DAEMON_HOME/cosmovisor/upgrades/v12/bin
# this will return v12.0.0
$DAEMON_HOME/cosmovisor/upgrades/v12/bin/junod version
```

Alternatively, you can run the upgrade the old-fashioned way.
