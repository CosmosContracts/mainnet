# Juno v30 mainnet upgrade — DRAFT

> **Status:** proposed target. The intended halt is block `40420069`; it becomes authoritative only after an on-chain software-upgrade proposal passes and `junod query upgrade plan` reports plan `v30` at that height.

This package prepares Juno mainnet to upgrade from v29 to [`v30.0.0`](https://github.com/CosmosContracts/juno/releases/tag/v30.0.0), the source release already exercised on `uni-7`.

| Item | Value |
|---|---|
| Chain ID | `juno-1` |
| Target version | `v30.0.0` |
| CosmWasm support | CosmWasm `2.2` (`wasmd v0.61.11`, `wasmvm v3.0.4`) |
| Upgrade plan name | **`v30`** |
| Upgrade height | **`40420069`** |
| Approximate UTC halt | **2026-08-03 15:33 UTC — height is authoritative** |
| Release commit | `c0b3a8d258d52d16e5bc39a75168a99aab9d098e` |

The annotated release tag peels to the commit above. Operators build the release from source, following the established pattern used by previous Juno mainnet upgrades.

## What changes in v30

This is a consensus-breaking SDK and state migration. It:

- upgrades to Cosmos SDK `v0.53.7`, CometBFT `v0.38.23`, wasmd `v0.61.11`, wasmvm `v3.0.4`, and IBC-Go `v10.6.0`, supporting CosmWasm `2.2` contracts plus Juno's `token_factory` capability;
- adds the `feemarket` and `votingsnapshot` stores;
- deletes the `globalfee`, `crisis`, `params`, `nft`, `feeibc`, and `interchainquery` stores;
- backfills voting-snapshot state from current staking delegations;
- enables the fee market for the staking bond denom (`ujuno`) with minimum base gas price `0.075` and maximum block utilization derived from consensus `block.max_gas`;
- sets the cw-hooks contract failure-removal threshold to `3`.

The same `v30.0.0` commit successfully upgraded `uni-7` under plan `v30` at height `16034000`. That testnet height is historical and must never be used for mainnet.

## Completed evidence

- [x] `v30.0.0` build, lint, CodeQL, upgrade, fee-market, IBC, IBC-hooks, PFM, CosmWasm, DAO DAO, and other release CI jobs passed on the tagged commit.
- [x] `uni-7` successfully halted and resumed under plan `v30` at height `16034000` on the same release commit.
- [x] Live post-upgrade checks confirmed continued blocks, fee-market state, cw-hooks parameters, voting-snapshot backfill/queries, module versions, and existing IBC channels.
- [x] A clean source build from tag `v30.0.0` was independently reproduced with Go `1.25.2` and reported the expected release commit and dependency versions.
- [x] Mainnet has no scheduled upgrade plan; the gov authority/parameters match the payload; two providers report consensus `block.max_gas = 100000000`; all 674 IBC channels were enumerated with no `icqhost` port or `icq-*` channel.

## Build v30 from source

Use Go `1.25.2`, matching the tagged release build configuration. Build as the same OS user that runs `junod`, from a clean checkout of the release tag, as in previous Juno upgrades:

```bash
go version
# go version go1.25.2 ...

cd juno
git fetch --tags && git checkout v30.0.0

test "$(git rev-parse HEAD)" = "c0b3a8d258d52d16e5bc39a75168a99aab9d098e"
test -z "$(git status --porcelain)"

make build && make install

GOBIN="$(go env GOBIN)"
test -n "$GOBIN" || GOBIN="$(go env GOPATH)/bin"
JUNOD_BIN="$GOBIN/junod"
export PATH="$GOBIN:$PATH"

junod version --long | grep "cosmos_sdk_version\|commit\|version:"
# commit: c0b3a8d258d52d16e5bc39a75168a99aab9d098e
# cosmos_sdk_version: v0.53.7
# version: v30.0.0

go version -m "$JUNOD_BIN" | grep 'github.com/CosmWasm/wasmvm/v3'
ldd "$JUNOD_BIN" | grep 'libwasmvm' | grep -v 'not found'
```

The binary must report:

- version `v30.0.0`;
- commit `c0b3a8d258d52d16e5bc39a75168a99aab9d098e`;
- Go `1.25.2`;
- Cosmos SDK `v0.53.7`;
- CometBFT `v0.38.23`;
- wasmvm `v3.0.4`.

The `libwasmvm` check must resolve to an accessible library path. Stop if it reports `not found`.

Record the locally built binary's checksum, then stage a candidate copy without replacing the running v29 binary:

```bash
install -d "$HOME/juno-v30"
install -m 0755 "$JUNOD_BIN" "$HOME/juno-v30/junod"
sha256sum "$HOME/juno-v30/junod"
```

## Before the halt

1. Confirm the on-chain plan rather than trusting this document:

   ```bash
   RPC="https://juno-rpc.publicnode.com:443"
   junod query upgrade plan --node "$RPC" --output json
   ```

   The response must name `v30` and show the approved mainnet height. If it does not, stop.

2. Confirm the existing node is healthy, synced, and signing normally.
3. Preserve the current binary and record its checksum/version.
4. Complete the published snapshot/restore procedure. Handle `priv_validator_state.json` separately and never restore stale signing state onto a validator that may have signed later heights.
5. Set `minimum-gas-prices = "0.075ujuno"` in `app.toml`, or leave it empty so the on-chain fee market sets the floor. Do not retain a lower non-empty value.
6. Capture consensus `block.max_gas` from two providers at the same height; this is the expected post-upgrade fee-market maximum utilization. The value currently observed is `100000000`, but it is not an invariant.
7. Stage v30 without replacing the running v29 binary.

## Stage with Cosmovisor

The directory must match the on-chain plan name exactly: `v30`. Inspect the actual service before deriving its home or staging anything:

```bash
sudo systemctl show junod -p User -p ExecStart -p Environment --no-pager
sudo systemctl cat junod
```

Confirm `ExecStart` uses Cosmovisor, identify the service user's exact Juno home, and confirm `DAEMON_NAME=junod`, automatic binary download is disabled, and either `DAEMON_RESTART_AFTER_UPGRADE=true` or the systemd unit has an appropriate restart policy. If `ExecStart` does not use Cosmovisor, stop and use the manual procedure instead.

Set `DAEMON_HOME` to the exact home used by the service—not the interactive user's default—then stage the binary as the service user or restore the correct ownership explicitly:

```bash
export DAEMON_HOME="<EXACT_SERVICE_JUNO_HOME>"
install -d "$DAEMON_HOME/cosmovisor/upgrades/v30/bin"
install -m 0755 "$HOME/juno-v30/junod" \
  "$DAEMON_HOME/cosmovisor/upgrades/v30/bin/junod"

"$DAEMON_HOME/cosmovisor/upgrades/v30/bin/junod" version --long
sha256sum "$DAEMON_HOME/cosmovisor/upgrades/v30/bin/junod"
```

Confirm the staged binary reports the expected version and commit, and that its checksum matches the locally built candidate recorded above.

Confirm the staged directory and binary are owned by the service user. At the scheduled halt, observe Cosmovisor perform the switch; do not manually replace or start a second `junod` process.

## Manual upgrade

If Cosmovisor is not used, inspect the service and record its exact user and executable before the halt:

```bash
sudo systemctl show junod -p User -p ExecStart --no-pager
sudo systemctl cat junod
```

If `ExecStart` uses Cosmovisor, stop and use the Cosmovisor procedure instead. Otherwise set `ACTUAL_JUNOD` to the exact executable used by `ExecStart`; do not infer it from `command -v`, `$HOME`, or the interactive shell user. Preserve that exact v29 executable under a new, checksum-recorded backup path before the halt.

At the approved scheduled halt only:

```bash
ACTUAL_JUNOD="<EXACT_EXECSTART_JUNOD_PATH>"
V29_BACKUP="<NEW_NONEXISTENT_V29_BACKUP_PATH>"
DAEMON_HOME="<EXACT_SERVICE_JUNO_HOME>"
UPGRADE_HEIGHT="<APPROVED_MAINNET_HEIGHT>"

test -x "$ACTUAL_JUNOD"
test ! -e "$V29_BACKUP"
sudo systemctl stop junod
if sudo systemctl is-active --quiet junod; then
  echo "junod did not stop" >&2
  exit 1
fi
jq -e --arg height "$UPGRADE_HEIGHT" \
  '.name == "v30" and (.height | tostring) == $height' \
  "$DAEMON_HOME/data/upgrade-info.json" >/dev/null
sudo install -m 0755 "$ACTUAL_JUNOD" "$V29_BACKUP"
sha256sum "$V29_BACKUP"

sudo install -m 0755 "$HOME/juno-v30/junod" "$ACTUAL_JUNOD.new"
sha256sum "$ACTUAL_JUNOD.new"
"$ACTUAL_JUNOD.new" version --long
sudo mv "$ACTUAL_JUNOD.new" "$ACTUAL_JUNOD"
sudo systemctl start junod
sudo systemctl show junod -p ExecStart --no-pager
journalctl -u junod -f --no-hostname
```

## Halt execution

At T-60 minutes:

- confirm the governance proposal passed and `query upgrade plan` returns exactly `v30` at the approved height;
- confirm more than 67% voting power is ready;
- confirm snapshot checksums, two independent RPCs, and incident coordination;
- pause nonessential relayers and transaction automation.

At the halt, use exactly one of the mutually exclusive Cosmovisor or manual procedures above:

1. Confirm the v29 binary stopped because of the scheduled upgrade—not a consensus or infrastructure failure.
2. Verify `$DAEMON_HOME/data/upgrade-info.json` contains plan name `v30` and the approved height.
3. Let Cosmovisor perform its staged switch, or perform the verified manual replacement; never do both. Preserve migration logs.
4. Watch for store-loader, voting-snapshot backfill, wasmvm, IBC, and fee-market errors.
5. Never delete state or run `unsafe-reset-all` in response to a migration panic.
6. Confirm at least 67% upgraded voting power and two post-upgrade blocks before resuming automation.

## Post-upgrade verification

From at least two independent RPC/REST providers:

```bash
RPC="https://juno-rpc.publicnode.com:443"
REST="https://juno-rest.publicnode.com"
UPGRADE_HEIGHT="<APPROVED_MAINNET_HEIGHT>"
DELEGATOR="<EXISTING_JUNO_ADDRESS>"

junod status --node "$RPC"
junod query upgrade applied v30 --node "$RPC" --output json
junod query upgrade module-versions --node "$RPC" --output json
junod query feemarket params --node "$RPC" --output json
junod query feemarket gas-price ujuno --node "$RPC" --output json
junod query cw-hooks params --node "$RPC" --output json

curl -fsS "$REST/juno/feemarket/v1/params" | jq
curl -fsS "$REST/juno/feemarket/v1/state" | jq
curl -fsS "$REST/juno/feemarket/v1/gas_price/ujuno" | jq
curl -fsS "$REST/juno/votingsnapshot/v1/params" | jq
curl -fsS "$REST/juno/votingsnapshot/v1/voting_power/$DELEGATOR/$UPGRADE_HEIGHT" | jq
curl -fsS "$REST/juno/votingsnapshot/v1/total_voting_power/$UPGRADE_HEIGHT" | jq
```

## Failure policy

Block-commit status is not the double-sign safety boundary. A validator may already have emitted a proposal, prevote, or precommit at a height/round/step even when no block committed. Preserve logs and each validator's newest signing state before any recovery action.

Never restore an older `priv_validator_state.json` with the same consensus key onto a validator that may have signed later state. Before a coordinated restore, stop and isolate the consensus key/HSM, collect each validator's highest signed height/round/step, define one network-wide recovery height and state, and document how signing state is preserved or advanced before any validator restarts. Individual rollback is forbidden both before and after the first post-upgrade commit.

Prefer a deterministic fix-forward binary. Use network-wide restore only through the pre-agreed, rehearsed recovery procedure with an incident commander and explicit double-sign controls.
