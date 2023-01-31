#### Week 3 : Mining and network block propagation

---

**Group questions:**
- What is a BGP hijack attack? What prevents it?
    - A malicious node can announce a victim's IP prefixes to reroute the traffic through itself, exposing unencrypted data. It can also create partitions in case of Bitcoin network by dropping peer connections
- What is the selfish-mining attack? How does it work? Is it a real threat?
    - A miner withholding blocks to create a fork at a later point. It can be profitable only if a pool has significant hashpower(>30%)
- What is the different between overt and covert ASICBoost?
    - Covert [ASICBoost](https://bitcoinops.org/en/topics/asicboost/) is not detectable, overt ASICBoost requires a change in one of the field of the block header
- Why would some miners use parts of the version field as a nonce? What effect does this have on nodes?
    - For the miners who wish to use the overt ASICBoost
- **How do pools give work assignments to client miners? Do all miners search the same block candidates?**
    - Mining pools exist to reduce the variance of the payout, [wiki link](https://en.wikipedia.org/wiki/Mining_pool)
    - **Work assignment**: 
        - by assigning a range of nonce, after finishing hashing with that range, the worker requests a new work unit 
        - the newer pools mostly allow the miners to choose all the details 
    - **All miners don't search the same block candidate, miners could have different set of mempool transactions in their block candidate. But the valid block must generate a hash starting with certain number of zeros based on the current difficulty.**
    - Payout assignment: 
        - Pay per share: split based on shares
        - Proportional: split based on shares but share value calculated after block is found
        - Pooled mining: pools are switched and older shares are given less weight
        - Pay-per-last-N-shares: similar to proportional but N last shares instead of only last block
        - [P2Pool](https://en.bitcoinwiki.org/wiki/P2Pool): decentralized pool, done via mining a sidechain at a lower difficulty and faster rate of block publication(P2Pool: 30 sec)
    - **Questions:**
        - What's the difference between pool suggesting range of nonce vs giving miners the freedom to choose anything on their own? To avoid repeated work in case batch of mempool tx match exactly for different block candidates?
        - What's extranonce for? (2 to 100 bytes, [link](https://en.bitcoin.it/wiki/Transaction)), [link2](https://bitcoin.stackexchange.com/questions/5048/what-is-the-extranonce (the original design had extra nonce, later removed)

---

Other questions:

- Can you switch receiver's address, change amounts in case of RBF and CPFP? [link](https://bitcoin.stackexchange.com/questions/113929/can-i-use-rbf-protocol-to-change-the-value-of-the-transaction)
- Why are P2Pools not successful? Resource inefficiency?


---
Notes:

- [graphical illustration of bitcoin block](https://blog.bitmex.com/graphical-illustration-of-a-bitcoin-block/)
- Stratum v2 improvements: better security and more tx censorship resistance
- Bad propagation can be good for large miners
- Sources of block latency: [Block propagation, Greg Maxwell](https://www.youtube.com/watch?v=EHIuuKCm53o): 
    - Creating a block template (30 ms)
    - Dispatching the template to hashers
    - Hearing a solution back from hashers
    - Distributing solution to first-level peer(serialize and tcp round trips)
    - First peer validating
    - Distributing solution to second-level peer
- Security, [Matt Corallo](https://www.youtube.com/watch?v=k_z-FBAil6k):
    - DNS is (usually) unauthenticated
    - BGP is unauthenticated and trivial to steal
        - Pakistan accidentally took down youtube
        - MyEtherWallet got popp'ed via AWS getting hijacked
    - Even off-path attackers can (sometimes) MITM
    - Stratum makes these attacks worse than other unauthenticated protocols - just needs one injected packet worht of data to hijack a miner until it is restarted.
- Resolving a BGP hijack attack is a human driven process and can take hours
    - Security advances (Hijacking Bitcoin [talk](https://www.youtube.com/watch?v=4rTp37nJzGs)):
        - Short-term: routing-aware peer selection, monitor changes in peer behavior, stats etc.
        - Long-term: e2ee or MAC(prevents delay attacks, not partition attacks), deploy secure routing protocols(prevents partition attacks, not delay attacks)
- RBF: replace by fees(sender pays higher fees), CPFP(child pays for parent): receiver pays higher fees



---
References:
- [Bitcoin nonce value distribution including ASICBoost](https://blog.bitmex.com/the-mystery-of-the-bitcoin-nonce-pattern/)
- [PoW mining simulator](https://github.com/LarryRuane/minesim)
- [P2Pool](https://en.bitcoin.it/wiki/P2Pool), [BraidPool](https://github.com/pool2win/braidpool) 
- [Erlay - to improve bandwidth efficienty](https://bitcoinops.org/en/topics/erlay/)
- [Riot - tx fee report](https://www.riotblockchain.com/news-media/research)
- RBF: privacy issue?
- [Border Gateway Protocol(BGP)](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)