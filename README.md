# cw20 DAO

This builds on [cw3-flex-multisig](https://github.com/CosmWasm/cw-plus/tree/main/contracts/cw3-flex-multisig) and instead has the voting set maintained by cw20 tokens. This allows for the cw20s to serve as governance tokens in the DAO, similar to how governance tokens work using contracts like [Compound Governance](https://compound.finance/governance).

## Instantiation

The first step to create such a DAO is to instantiate an `cw20` contract.

When creating the DAO, in addition to the `cw20` conrtract address you must set the required threshold to pass a vote as well as the max/default voting period.

## Execution Process

First, a voter with an cw20 balance must submit a proposal. The proposer can set
an expiration time for the voting process, or it defaults to the limit
provided when creating the contract (so proposals can be closed after several
days).

Before the proposal has expired, any voter with the required cw20 token can add their
vote. Only "Yes" votes are tallied. If enough "Yes" votes were submitted before
the proposal expiration date, the status is set to "Passed".

Once a proposal is "Passed", anyone with the correct cw20 token may submit an
"Execute" message. This will trigger the proposal to send all stored messages from
the proposal and update it's state to "Executed", so it cannot run again. (Note if
the execution fails for any reason - out of gas, insufficient funds, etc - the state
update will be reverted, and it will remain "Passed", so you can try again).

Once a proposal has expired without passing, anyone can submit a "Close"
message to mark it closed. This has no effect beyond cleaning up the UI/database.

## Running this contract

You will need Rust 1.44.1+ with `wasm32-unknown-unknown` target installed.

You can run unit tests on this via:

`cargo test`

Once you are happy with the content, you can compile it to wasm via:

```
RUSTFLAGS='-C link-arg=-s' cargo wasm
cp ../../target/wasm32-unknown-unknown/release/cw_dao.wasm .
ls -l cw_dao.wasm
sha256sum cw_dao.wasm
```

Or for a production-ready (optimized) build, run a build command in
the repository root: https://github.com/CosmWasm/cosmwasm-plus#compiling.
