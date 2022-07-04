# Multiverse Upgrade

Multiverse adds ICA Host functionality to the Juno chain, and exposes the following actions to Controller Chains:

- Native functionality and core SDK modules
- Smart Contract store
- Smart Contract instantiate
- Smart Contract execute

Additional links:

- [The Multiverse upgrade prop is viewable here](https://www.mintscan.io/juno/proposals/28).
- [The v7.0.0 changelog is viewable here](https://github.com/CosmosContracts/juno/releases/tag/v7.0.0).

The target block for this upgrade is [3851750](https://www.mintscan.io/juno/blocks/3851750), which is expected to arrive on _Thursday July 7th at 1700UTC_, +/- 1 hour.

Note that go 1.18 is now required - so you will need to remove any older version of go and install the right version if you are building manually.

These are the instructions you will need if you run cosmovisor:

```bash
cd juno
git fetch --tags && git checkout v7.0.0
make build && make install
# this will return commit 1291b66f3cd3529ad244391619f7b4166bb28373
junod version --long

mkdir -p $DAEMON_HOME/cosmovisor/upgrades/veritas/bin && cp $HOME/go/bin/junod $DAEMON_HOME/cosmovisor/upgrades/veritas/bin
# this will return v7.0.0
$DAEMON_HOME/cosmovisor/upgrades/veritas/bin/junod version
```

Alternatively, you can run the upgrade the old-fashioned way.
