# MEV-geth for MiningDAO block producers

This is a fork of MEV-geth.

With MiningDAO we wanted to create a tool which makes sure that Mining pools dont have any ability to frontrun searchers and normal DeFi users(using private relays).
To make it possible mining pool has to mine blocks while being oblivious about content of such blocks.
One more requirement was to make sure that absolutely anyone can become a block producer and system remains completely permisionless.

In the first version we've implemented a very simple smart contract which handles new block submissions: https://etherscan.io/address/0xccccccccab8785b30f5d4ceafa0f3cbd1edaa58f#code

This contract DOES NOT VERIFY that block nonce / mixDigest is valid or that it was broadcasted without delay and block producer for now has to trust pool operator to provide valid solutions for block headers.

In the next version we're planning to introduce trustless approach to verify submited block nonce on-chain(see smartpool) and verify delay in seconds for share submission(using oracles).

## Quick start
Since it's a very first version we only expect sophisticated searchers to submit us whole blocks.
For anyone who's just starting out you can always use our bundle endpoint: https://bundle.miningdao.io/

Under existing design block producers pay FULL price even for UNCLES so you need to make sure that you understand the code and
accurately estimate your optimal payment for block header. You should also decrease your uncle rate by close integration with https://bloxroute.com/

As first step you should go over [introduced changes](https://github.com/Mining-DAO/mev-geth/commit/778e4dbd826f7c6db28ae5a373780796344cfd24) and corresponding [smart contract](https://etherscan.io/address/0xccccccccab8785b30f5d4ceafa0f3cbd1edaa58f#code) then you should modify submit work endpoint as well as setting a key for your block producer inside [MakeWork](consensus/ethash/sealer.go) function.

Next you'll have to start mev-geth in mining mode notifying our endpoint at https://block.miningdao.io about new sealed work. Example command:
```
--miner.notify=https://block.miningdao.io
```
You'll have to make sure that only `eth_submitWork` endpoint is externally available and nothing else.
