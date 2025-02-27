# CHANGELOG

## Unreleased

### BREAKING CHANGES

- The `TMHOME` environment variable was renamed to `CMTHOME`, and all environment variables starting with `TM_` are instead prefixed with `CMT_`
  ([\#211](https://github.com/cometbft/cometbft/issues/211))
- `[protobuf]` Remove fields `sender`, `priority`, and `mempool_error` from
  `ResponseCheckTx`. ([\#260](https://github.com/cometbft/cometbft/issues/260))
- `[mempool]` Remove priority mempool.
  ([\#260](https://github.com/cometbft/cometbft/issues/260))
- `[config]` Remove `Version` field from `MempoolConfig`.
  ([\#260](https://github.com/cometbft/cometbft/issues/260))
- Bump minimum Go version to 1.20
  ([\#385](https://github.com/cometbft/cometbft/issues/385))
- `[crypto/merkle]` Do not allow verification of Merkle Proofs against empty trees (`nil` root). `Proof.ComputeRootHash` now panics when it encounters an error, but `Proof.Verify` does not panic
  ([\#558](https://github.com/cometbft/cometbft/issues/558))
- `[state/kvindexer]` Remove the function type from the event key stored in the database. This should be breaking only
for people who forked CometBFT and interact directly with the indexers kvstore.
  ([\#774](https://github.com/cometbft/cometbft/pull/774))
- `[kvindexer]` Added support for big integers and big floats in the kvindexer.
  Breaking changes: function `Number` in package `libs/pubsub/query/syntax` changed its return value.
  ([\#797](https://github.com/cometbft/cometbft/pull/797))
- `[pubsub]` Added support for big integers and big floats in the pubsub event query system.
  Breaking changes: function `Number` in package `libs/pubsub/query/syntax` changed its return value.
  ([\#797](https://github.com/cometbft/cometbft/pull/797))
- `[mempool]` Application can now set `ConsensusParams.Block.MaxBytes` to -1
  to have visibility on all transactions in the
  mempool at `PrepareProposal` time.
  This means that the total size of transactions sent via `RequestPrepareProposal`
  might exceed `RequestPrepareProposal.max_tx_bytes`.
  If that is the case, the application MUST make sure that the total size of transactions
  returned in `ResponsePrepareProposal.txs` does not exceed `RequestPrepareProposal.max_tx_bytes`,
  otherwise CometBFT will panic.
  ([\#980](https://github.com/cometbft/cometbft/issues/980))
- `[node]` Removed `ConsensusState()` accessor from `Node`
  struct - all access to consensus state should go via the reactor
  ([\#1120](https://github.com/cometbft/cometbft/pull/1120))
- `[state]` Move pruneBlocks from node/state to state/execution.
  ([\#6541](https://github.com/tendermint/tendermint/pull/6541))
- `[abci]` Move `app_hash` parameter from `Commit` to `FinalizeBlock`
  ([\#8664](https://github.com/tendermint/tendermint/pull/8664))
- `[abci]` Introduce `FinalizeBlock` which condenses `BeginBlock`, `DeliverTx`
  and `EndBlock` into a single method call
  ([\#9468](https://github.com/tendermint/tendermint/pull/9468))
- `[p2p]` Remove unused p2p/trust package
  ([\#9625](https://github.com/tendermint/tendermint/pull/9625))
- `[rpc]` Remove global environment and replace with constructor
  ([\#9655](https://github.com/tendermint/tendermint/pull/9655))
- `[inspect]` Add a new `inspect` command for introspecting
  the state and block store of a crashed tendermint node.
  ([\#9655](https://github.com/tendermint/tendermint/pull/9655))
- `[node]` Move DBContext and DBProvider from the node package to the config
  package. ([\#9655](https://github.com/tendermint/tendermint/pull/9655))
- `[metrics]` Move state-syncing and block-syncing metrics to
  their respective packages. Move labels from block_syncing
  -> blocksync_syncing and state_syncing -> statesync_syncing
  ([\#9682](https://github.com/tendermint/tendermint/pull/9682))

### BUG FIXES

- `[consensus]` Fixed a busy loop that happened when sending of a block part failed by sleeping in case of error.
  ([\#4](https://github.com/informalsystems/tendermint/pull/4))
- `[consensus]` Short-term fix for the case when `needProofBlock` cannot find
  previous block meta by defaulting to the creation of a new proof block.
  Special thanks to the [Vega.xyz](https://vega.xyz/) team, and in particular to
  Zohar (@ze97286), for reporting the problem and working with us to get to a
  fix. ([\#386](https://github.com/cometbft/cometbft/pull/386))
- `[kvindexer]` Forward porting the fixes done to the kvindexer in 0.37 in PR \#77
  ([\#423](https://github.com/cometbft/cometbft/pull/423))
- `[consensus]` Unexpected error conditions in `ApplyBlock` are non-recoverable, so ignoring the error and carrying on is a bug. We replaced a `return` that disregarded the error by a `panic`.
  ([\#496](https://github.com/cometbft/cometbft/pull/496))
- `[consensus]` Rename `(*PeerState).ToJSON` to `MarshalJSON` to fix a logging data race
  ([\#524](https://github.com/cometbft/cometbft/pull/524))
- `[light]` Fixed an edge case where a light client would panic when attempting
  to query a node that (1) has started from a non-zero height and (2) does
  not yet have any data. The light client will now, correctly, not panic
  _and_ keep the node in its list of providers in the same way it would if
  it queried a node starting from height zero that does not yet have data
  ([\#575](https://github.com/cometbft/cometbft/issues/575))
- `[abci]` Restore the snake_case naming in JSON serialization of
  `ExecTxResult` ([\#855](https://github.com/cometbft/cometbft/issues/855)).
- `[consensus]` Avoid recursive call after rename to (*PeerState).MarshalJSON
  ([\#863](https://github.com/cometbft/cometbft/pull/863))
- `[mempool/clist_mempool]` \#890 Prevent a transaction to appear twice in the mempool  (@otrack)
- `[docker]` Ensure Docker image uses consistent version of Go.
  ([\#9462](https://github.com/tendermint/tendermint/pull/9462))
- `[abci-cli]` Fix broken abci-cli help command.
  ([\#9717](https://github.com/tendermint/tendermint/pull/9717))

### DEPRECATIONS

- `[rpc/grpc]` Mark the gRPC broadcast API as deprecated.
  It will be superseded by a broader API as part of
  [\#81](https://github.com/cometbft/cometbft/issues/81)
  ([\#650](https://github.com/cometbft/cometbft/issues/650))

### FEATURES

- `[proxy]` Introduce `NewUnsyncLocalClientCreator`, which allows local ABCI
  clients to have the same concurrency model as remote clients (i.e. one
  mutex per client "connection", for each of the four ABCI "connections").
  ([\#9830](https://github.com/tendermint/tendermint/pull/9830))

### IMPROVEMENTS

- `[e2e]` Add functionality for uncoordinated (minor) upgrades
  ([\#56](https://github.com/tendermint/tendermint/pull/56))
- `[tools/tm-signer-harness]` Remove the folder as it is unused
  ([\#136](https://github.com/cometbft/cometbft/issues/136))
- `[blocksync]` Generate new metrics during BlockSync
  ([\#543](https://github.com/cometbft/cometbft/pull/543))
- `[jsonrpc/client]` Improve the error message for client errors stemming from
  bad HTTP responses.
  ([cometbft/cometbft\#638](https://github.com/cometbft/cometbft/pull/638))
- `[rpc]` Remove response data from response failure logs in order
  to prevent large quantities of log data from being produced
  ([\#654](https://github.com/cometbft/cometbft/issues/654))
- `[pubsub/kvindexer]` Numeric query conditions and event values are represented as big floats with default precision of 125.
  Integers are read as "big ints" and represented with as many bits as they need when converting to floats.
  ([\#797](https://github.com/cometbft/cometbft/pull/797))
- `[node]` Make handshake cancelable ([cometbft/cometbft\#857](https://github.com/cometbft/cometbft/pull/857))
- `[mempool]` Application can now set `ConsensusParams.Block.MaxBytes` to -1
  to gain more control on the max size of transactions in a block.
  It also allows the application to have visibility on all transactions in the
  mempool at `PrepareProposal` time.
  ([\#980](https://github.com/cometbft/cometbft/pull/980))
- `[crypto/merkle]` Improve HashAlternatives performance
  ([\#6443](https://github.com/tendermint/tendermint/pull/6443))
- `[p2p/pex]` Improve addrBook.hash performance
  ([\#6509](https://github.com/tendermint/tendermint/pull/6509))
- `[crypto/merkle]` Improve HashAlternatives performance
  ([\#6513](https://github.com/tendermint/tendermint/pull/6513))
- `[pubsub]` Performance improvements for the event query API
  ([\#7319](https://github.com/tendermint/tendermint/pull/7319))
- `[rpc]` Enable caching of RPC responses
  ([\#9650](https://github.com/tendermint/tendermint/pull/9650))

## v0.37.0

*March 6, 2023*

This is the first CometBFT release with ABCI 1.0, which introduces the
`PrepareProposal` and `ProcessProposal` methods, with the aim of expanding the
range of use cases that application developers can address. This is the first
change to ABCI towards ABCI++, and the full range of ABCI++ functionality will
only become available in the next major release with ABCI 2.0. See the
[specification](./spec/abci/) for more details.

In the v0.34.27 release, the CometBFT Go module is still
`github.com/tendermint/tendermint` to facilitate ease of upgrading for users,
but in this release we have changed this to `github.com/cometbft/cometbft`.

Please also see our [upgrading guidelines](./UPGRADING.md) for more details on
upgrading from the v0.34 release series.

Also see our [QA results](https://docs.cometbft.com/v0.37/qa/v037/cometbft) for
the v0.37 release.

We'd love your feedback on this release! Please reach out to us via one of our
communication channels, such as [GitHub
Discussions](https://github.com/cometbft/cometbft/discussions), with any of your
questions, comments and/or concerns.

See below for more details.

### BREAKING CHANGES

- The `TMHOME` environment variable was renamed to `CMTHOME`, and all environment variables starting with `TM_` are instead prefixed with `CMT_`
  ([\#211](https://github.com/cometbft/cometbft/issues/211))
- `[p2p]` Reactor `Send`, `TrySend` and `Receive` renamed to `SendEnvelope`,
  `TrySendEnvelope` and `ReceiveEnvelope` to allow metrics to be appended to
  messages and measure bytes sent/received.
  ([\#230](https://github.com/cometbft/cometbft/pull/230))
- Bump minimum Go version to 1.20
  ([\#385](https://github.com/cometbft/cometbft/issues/385))
- `[abci]` Make length delimiter encoding consistent
  (`uint64`) between ABCI and P2P wire-level protocols
  ([\#5783](https://github.com/tendermint/tendermint/pull/5783))
- `[abci]` Change the `key` and `value` fields from
  `[]byte` to `string` in the `EventAttribute` type.
  ([\#6403](https://github.com/tendermint/tendermint/pull/6403))
- `[abci/counter]` Delete counter example app
  ([\#6684](https://github.com/tendermint/tendermint/pull/6684))
- `[abci]` Renamed `EvidenceType` to `MisbehaviorType` and `Evidence`
  to `Misbehavior` as a more accurate label of their contents.
  ([\#8216](https://github.com/tendermint/tendermint/pull/8216))
- `[abci]` Added cli commands for `PrepareProposal` and `ProcessProposal`.
  ([\#8656](https://github.com/tendermint/tendermint/pull/8656))
- `[abci]` Added cli commands for `PrepareProposal` and `ProcessProposal`.
  ([\#8901](https://github.com/tendermint/tendermint/pull/8901))
- `[abci]` Renamed `LastCommitInfo` to `CommitInfo` in preparation for vote
  extensions. ([\#9122](https://github.com/tendermint/tendermint/pull/9122))
- Change spelling from British English to American. Rename
  `Subscription.Cancelled()` to `Subscription.Canceled()` in `libs/pubsub`
  ([\#9144](https://github.com/tendermint/tendermint/pull/9144))
- `[abci]` Removes unused Response/Request `SetOption` from ABCI
  ([\#9145](https://github.com/tendermint/tendermint/pull/9145))
- `[config]` Rename the fastsync section and the
  fast\_sync key blocksync and block\_sync respectively
  ([\#9259](https://github.com/tendermint/tendermint/pull/9259))
- `[types]` Reduce the use of protobuf types in core logic. `ConsensusParams`,
  `BlockParams`, `ValidatorParams`, `EvidenceParams`, `VersionParams` have
  become native types.  They still utilize protobuf when being sent over
  the wire or written to disk.  Moved `ValidateConsensusParams` inside
  (now native type) `ConsensusParams`, and renamed it to `ValidateBasic`.
  ([\#9287](https://github.com/tendermint/tendermint/pull/9287))
- `[abci/params]` Deduplicate `ConsensusParams` and `BlockParams` so
  only `types` proto definitions are use. Remove `TimeIotaMs` and use
  a hard-coded 1 millisecond value to ensure monotonically increasing
  block times. Rename `AppVersion` to `App` so as to not stutter.
  ([\#9287](https://github.com/tendermint/tendermint/pull/9287))
- `[abci]` New ABCI methods `PrepareProposal` and `ProcessProposal` which give
  the app control over transactions proposed and allows for verification of
  proposed blocks. ([\#9301](https://github.com/tendermint/tendermint/pull/9301))

### BUG FIXES

- `[consensus]` Fixed a busy loop that happened when sending of a block part failed by sleeping in case of error.
  ([\#4](https://github.com/informalsystems/tendermint/pull/4))
- `[state/kvindexer]` Fixed the default behaviour of the kvindexer to index and
  query attributes by events in which they occur. In 0.34.25 this was mitigated
  by a separated RPC flag. @jmalicevic
  ([\#77](https://github.com/cometbft/cometbft/pull/77))
- `[state/kvindexer]` Resolved crashes when event values contained slashes,
  introduced after adding event sequences in
  [\#77](https://github.com/cometbft/cometbft/pull/77). @jmalicevic
  ([\#382](https://github.com/cometbft/cometbft/pull/382))
- `[consensus]` ([\#386](https://github.com/cometbft/cometbft/pull/386)) Short-term fix for the case when `needProofBlock` cannot find previous block meta by defaulting to the creation of a new proof block. (@adizere)
  - Special thanks to the [Vega.xyz](https://vega.xyz/) team, and in particular to Zohar (@ze97286), for reporting the problem and working with us to get to a fix.
- `[docker]` enable cross platform build using docker buildx
  ([\#9073](https://github.com/tendermint/tendermint/pull/9073))
- `[consensus]` fix round number of `enterPropose`
  when handling `RoundStepNewRound` timeout.
  ([\#9229](https://github.com/tendermint/tendermint/pull/9229))
- `[docker]` ensure Docker image uses consistent version of Go
  ([\#9462](https://github.com/tendermint/tendermint/pull/9462))
- `[p2p]` prevent peers who have errored from being added to `peer_set`
  ([\#9500](https://github.com/tendermint/tendermint/pull/9500))
- `[blocksync]` handle the case when the sending
  queue is full: retry block request after a timeout
  ([\#9518](https://github.com/tendermint/tendermint/pull/9518))

### FEATURES

- `[abci]` New ABCI methods `PrepareProposal` and `ProcessProposal` which give
  the app control over transactions proposed and allows for verification of
  proposed blocks. ([\#9301](https://github.com/tendermint/tendermint/pull/9301))

### IMPROVEMENTS

- `[e2e]` Add functionality for uncoordinated (minor) upgrades
  ([\#56](https://github.com/tendermint/tendermint/pull/56))
- `[tools/tm-signer-harness]` Remove the folder as it is unused
  ([\#136](https://github.com/cometbft/cometbft/issues/136))
- `[p2p]` Reactor `Send`, `TrySend` and `Receive` renamed to `SendEnvelope`,
  `TrySendEnvelope` and `ReceiveEnvelope` to allow metrics to be appended to
  messages and measure bytes sent/received.
  ([\#230](https://github.com/cometbft/cometbft/pull/230))
- `[abci]` Added `AbciVersion` to `RequestInfo` allowing
  applications to check ABCI version when connecting to CometBFT.
  ([\#5706](https://github.com/tendermint/tendermint/pull/5706))
- `[cli]` add `--hard` flag to rollback command (and a boolean to the `RollbackState` method). This will rollback
   state and remove the last block. This command can be triggered multiple times. The application must also rollback
   state to the same height.
  ([\#9171](https://github.com/tendermint/tendermint/pull/9171))
- `[crypto]` Update to use btcec v2 and the latest btcutil.
  ([\#9250](https://github.com/tendermint/tendermint/pull/9250))
- `[rpc]` Added `header` and `header_by_hash` queries to the RPC client
  ([\#9276](https://github.com/tendermint/tendermint/pull/9276))
- `[proto]` Migrate from `gogo/protobuf` to `cosmos/gogoproto`
  ([\#9356](https://github.com/tendermint/tendermint/pull/9356))
- `[rpc]` Enable caching of RPC responses
  ([\#9650](https://github.com/tendermint/tendermint/pull/9650))
- `[consensus]` Save peer LastCommit correctly to achieve 50% reduction in gossiped precommits.
  ([\#9760](https://github.com/tendermint/tendermint/pull/9760))

## v0.34.27

*Feb 27, 2023*

This is the first official release of CometBFT - a fork of [Tendermint
Core](https://github.com/tendermint/tendermint). This particular release is
intended to be compatible with the Tendermint Core v0.34 release series.

For details as to how to upgrade to CometBFT from Tendermint Core, please see
our [upgrading guidelines](./UPGRADING.md).

If you have any questions, comments, concerns or feedback on this release, we
would love to hear from you! Please contact us via [GitHub
Discussions](https://github.com/cometbft/cometbft/discussions),
[Discord](https://discord.gg/cosmosnetwork) (in the `#cometbft` channel) or
[Telegram](https://t.me/CometBFT).

Special thanks to @wcsiu, @ze97286, @faddat and @JayT106 for their contributions
to this release!

### BREAKING CHANGES

- Rename binary to `cometbft` and Docker image to `cometbft/cometbft`
  ([\#152](https://github.com/cometbft/cometbft/pull/152))
- The `TMHOME` environment variable was renamed to `CMTHOME`, and all
  environment variables starting with `TM_` are instead prefixed with `CMT_`
  ([\#211](https://github.com/cometbft/cometbft/issues/211))
- Use Go 1.19 to build CometBFT, since Go 1.18 has reached end-of-life.
  ([\#360](https://github.com/cometbft/cometbft/issues/360))

### BUG FIXES

- `[consensus]` Fixed a busy loop that happened when sending of a block part
  failed by sleeping in case of error.
  ([\#4](https://github.com/informalsystems/tendermint/pull/4))
- `[state/kvindexer]` Resolved crashes when event values contained slashes,
  introduced after adding event sequences.
  (\#[383](https://github.com/cometbft/cometbft/pull/383): @jmalicevic)
- `[consensus]` Short-term fix for the case when `needProofBlock` cannot find
  previous block meta by defaulting to the creation of a new proof block.
  ([\#386](https://github.com/cometbft/cometbft/pull/386): @adizere)
  - Special thanks to the [Vega.xyz](https://vega.xyz/) team, and in particular
    to Zohar (@ze97286), for reporting the problem and working with us to get to
    a fix.
- `[p2p]` Correctly use non-blocking `TrySendEnvelope` method when attempting to
  send messages, as opposed to the blocking `SendEnvelope` method. It is unclear
  whether this has a meaningful impact on P2P performance, but this patch does
  correct the underlying behaviour to what it should be
  ([tendermint/tendermint\#9936](https://github.com/tendermint/tendermint/pull/9936))

### DEPENDENCIES

- Replace [tm-db](https://github.com/tendermint/tm-db) with
  [cometbft-db](https://github.com/cometbft/cometbft-db)
  ([\#160](https://github.com/cometbft/cometbft/pull/160))
- Bump tm-load-test to v1.3.0 to remove implicit dependency on Tendermint Core
  ([\#165](https://github.com/cometbft/cometbft/pull/165))
- `[crypto]` Update to use btcec v2 and the latest btcutil
  ([tendermint/tendermint\#9787](https://github.com/tendermint/tendermint/pull/9787):
  @wcsiu)

### FEATURES

- `[rpc]` Add `match_event` query parameter to indicate to the RPC that it
  should match events _within_ attributes, not only within a height
  ([tendermint/tendermint\#9759](https://github.com/tendermint/tendermint/pull/9759))

### IMPROVEMENTS

- `[e2e]` Add functionality for uncoordinated (minor) upgrades
  ([\#56](https://github.com/tendermint/tendermint/pull/56))
- `[tools/tm-signer-harness]` Remove the folder as it is unused
  ([\#136](https://github.com/cometbft/cometbft/issues/136))
- Append the commit hash to the version of CometBFT being built
  ([\#204](https://github.com/cometbft/cometbft/pull/204))
- `[mempool/v1]` Suppress "rejected bad transaction" in priority mempool logs by
  reducing log level from info to debug
  ([\#314](https://github.com/cometbft/cometbft/pull/314): @JayT106)
- `[consensus]` Add `consensus_block_gossip_parts_received` and
  `consensus_step_duration_seconds` metrics in order to aid in investigating the
  impact of database compaction on consensus performance
  ([tendermint/tendermint\#9733](https://github.com/tendermint/tendermint/pull/9733))
- `[state/kvindexer]` Add `match.event` keyword to support condition evaluation
  based on the event the attributes belong to
  ([tendermint/tendermint\#9759](https://github.com/tendermint/tendermint/pull/9759))
- `[p2p]` Reduce log spam through reducing log level of "Dialing peer" and
  "Added peer" messages from info to debug
  ([tendermint/tendermint\#9764](https://github.com/tendermint/tendermint/pull/9764):
  @faddat)
- `[consensus]` Reduce bandwidth consumption of consensus votes by roughly 50%
  through fixing a small logic bug
  ([tendermint/tendermint\#9776](https://github.com/tendermint/tendermint/pull/9776))

---

CometBFT is a fork of [Tendermint Core](https://github.com/tendermint/tendermint) as of late December 2022.

## Bug bounty

Friendly reminder, we have a [bug bounty program](https://hackerone.com/cosmos).

## Previous changes

For changes released before the creation of CometBFT, please refer to the Tendermint Core [CHANGELOG.md](https://github.com/tendermint/tendermint/blob/a9feb1c023e172b542c972605311af83b777855b/CHANGELOG.md).

