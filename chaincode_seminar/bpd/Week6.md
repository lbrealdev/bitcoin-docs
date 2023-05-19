#### Week 6: Debate
---
---

**AGAINST: "Bigger blocks beyong SegWit block size limit"**
- Argumemnts against big blocks:
    - **Running full-nodes** would require more storage(total blockchain size), bandwidth(to download/upload all tx and blocks), CPU cost(to validate all txs and blocks), hard to boot a node(IBD). This can result in using SPVs instead of full-nodes which then threatens the security of the Bitcoin network. 
    - **Mining centralization**: bigger blocks can result in more selfish-mining attacks making smaller miners less profitable and causing mining-centralization
    - **Lower block subsidy from txs**: bigger blocks would mean lower demand for block space, meaning lower miner reward from tx fee in the block generation subsidy. 

- The fee has been historically low since big institutions started batching their transactions,  [historical fee chart](https://bitinfocharts.com/comparison/bitcoin-transactionfees.html#alltime). As the block reward cuts in half every 4 years, the subsidy from tx fees will keep diverse set of miners incentivized to keep mining specially if that comes with greater security of the network as well as keeping mining itself fair and less vunerable to attacks.

- Increasing adoption of the layer2 solutions like lightning network and sidechains like liquid will also move users of smaller and more frequent transactions to those solutions (this will still be utilizing base layer to some extent for instance, opening and closing channels would be 2-of-2 multi-sig tx on base layer still meaning it will off-load such txs and at the same time ensures the demand on the base layer)

---
---


`
assumeutxo: use of serialized utxo sets to reduce the amount of time needed to bootstrap a usable Bitcoin node with acceptable changes in security
`

**FOR: "The tradeoffs of assumeutxo are not worth it"**

The trade-offs:
- need extra work of adding an updated hash and verify with proper scrutiny in the new releases
- getting the utxo snapshot separately outside of bitcoin-core download is not a good UX and will centralize to third-parties like umbrel, start9 etc.

- a hardcoded assumeutxo hash will flag which snapshots are considered valid
(currently Bitcoin ships with hardcoded assumevalid values as a performance optimization where hardcoded hash is updated every release with a block several thousand blocks deep at the time of the PR, [link](https://bitcoin.stackexchange.com/questions/88652/does-assumevalid-lower-the-security-of-bitcoin). It just assumes script part to be valid up until hardcoded hash)

- The implementation is divided into to phases, phase 1 adds an RPC command that allowed you to activate a UTXO snapshot that you had obtained somehow(http, tor, torrent), phase 2 which is unlikely had plans to distribute utxo set in chunks into the p2p layer. (ref [link](https://btctranscripts.com/london-bitcoin-devs/2021-11-02-socratic-seminar-assumeutxo/#assumedvalid-and-checkpoints))

- [chainparams.cpp](https://github.com/bitcoin/bitcoin/blob/50422b770a40f5fa964201d1e99fd6b5dc1653ca/src/chainparams.cpp#L166)

