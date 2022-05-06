# Veritas Upgrade

This upgrade relocates the confiscated funds to the governance-controlled Unity smart contract.

[Veritas Upgrade Proposal is viewable here](https://www.mintscan.io/juno/proposals/21).

These are the instructions you will need if you run cosmovisor:

```bash
cd juno
git fetch --tags && git checkout v5.0.0
make build && make install
# this will return commit bd0a4b1f05cb755a48bf3d8951e020785ea66fba
junod version --long

mkdir -p $DAEMON_HOME/cosmovisor/upgrades/unity/bin && cp $HOME/go/bin/junod $DAEMON_HOME/cosmovisor/upgrades/unity/bin
# this will return v5.0.0
$DAEMON_HOME/cosmovisor/upgrades/unity/bin/junod version
```

Alternatively, you can run the upgrade the old-fashioned way.
