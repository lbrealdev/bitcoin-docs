#### Week 4 : Mining and network block propagation

---

**Group questions:**

- **How does a fixed set of 4 outbound peers get chosen? In what circumstances would you evict or change them?**
    - The 4 outbound peers are the protected peers, [code comment with conditions](https://github.com/bitcoin/bitcoin/blob/3d0fca128813ef213cc71908aa956e955c1eb09a/src/net_processing.cpp#L445), 
    - Here's the actual [code](https://github.com/bitcoin/bitcoin/blob/3d0fca128813ef213cc71908aa956e955c1eb09a/src/net_processing.cpp#L2737) which sets m_protect=true (so flagged in-memory only)
    

    - **Eviction**: 
        - a protected node will have to first violate the protection condition and get tagged m_protect=false
        - then it evicts if they broadcast any of these: invalid tx or blocks, unconnected blocks, stalling, non-standard tx, malformed messages
        - [code](https://github.com/bitcoin/bitcoin/blob/3d0fca128813ef213cc71908aa956e955c1eb09a/src/net_processing.cpp#L4936) (ConsiderEviction method)

        

    
- What is the rationale behind the "new"/"tried" table design? Were there any prior inspirations within the field of distributed computing?
    - new table: IPs heard but not connected to, tried table: IPs previously connected
- What can an attacker do if they are able to eclipse a mining pool?
    - steal it's reward? selfish mining?
- How are anchor connections chosen? In what circumstances would you evict or change them?
    - `anchors.dat` is created on shutdown to save 2 last known outgoing block-relay-only peers, at startup are re-connected and file is deleted. This is to mitigate the eclipse attack.


---

**Other questions**:
- `num_outbound_hb_peers` : # outbound high-bandwidth peers?
- Does RBF tx have a new txid? the old tx and it's txid is invalidated by nodes after RBF tx gets in, how?
- If validity of a tx could depend on data outside of the tx, then could there be instances where node invalidates a certain tx because of different versions of Bitcoin-core, given that soft forks can make something previously valid, invalid?
- Is running Bitcoin node over Tor bad idea? [paper](https://ieeexplore.ieee.org/document/7163022)

---

**Notes**:

- [Onboarding to Bitcoin - Amiti Uttarwar](https://www.youtube.com/watch?v=H-wH6mY9pZo&t=257s):
    -  P2P design goals: reliable, timely, accesible, private, upgradeable
    - Reliability (network partitions):
        - unintentional: peer prioritization logic(ex. geographic partitions)
        - intentional: double spend strategy, eclipse attack
        - to counter eclipse attack:
            - increase # of connections: bandwidth!: upto 8 outbound connections, 125 total connections
            - choose diverse peers: limited info!
            - run multiple nodes: user-dependent strategy
            - value long-lasting connections: privacy! dynamic connections help maintain tx privacy
    - Privacy: 3 types of messages: addresses, txs, blocks
        - Blocks are less vulnerable to privacy attacks as compared to addresses and txs:  Infrequent, FIBRE is a network that miners use to transact blocks increases surveillance difficulty
    - A block-relay-only node is more resistant to an eclipse attack, anchors: persist peers and reconnect them on startup to mitigate attacks but bad for privacy. You can run a block-relay-only node with anchors to combine the benefits of both.

- [Bitcoin P2P network, John Newbery](https://www.youtube.com/watch?v=eVerdR2hOMw)
    - Network commands: VERSION, VERACK, ADDR, GETADDR, INV, GETDATA, GETBLOCKS, GETHEADERS, TX, BLOCK, HEADERS, PING, PONG
    - Upto 8 outbound, any number of inbound connections
    - Disconnecting and banning: bad-behavior: invalid tx or blocks, unconnected blocks, stalling, non-standard tx, malformed messages. **Resolution**: Either ignore, disconnect, ban(for 24 hours) or apply DoS points(if reaches 100 then ban)
    - Standardness only applies to txs regardless of the block
    - Types of nodes: full node, prune node, archival node, lightclient
    - Full node:
        - receives newly mined blocks
        - verifies the validity of all blocks and all tx included in these blocks
        - enforces the consensus rules of the Bitcoin network
        - maintains a collection of all the unspent outputs
        - most secure and private way to use Bitcoin
    - IBD: [initial block download](https://bitcoin.org/en/full-node#initial-block-downloadibd)
    - A pruned node is a type of full node, discards old block data but keeps the headers(min. of 2 days). It can't serve the old blocks so can't provide IBD.
    - An archival node retains all old block and undo data, can serve old blocks to peers on the network, signaled using NODE_NETWORK in the version handshake
    - SPV(simple payment verification) or a light-client only downloads the block headers and information about specific txs, it can validate proof of work(i.e. check it a tx was in a block), can use bloom filters to preserve(some) privacy
    - Node options: -blocksonly(no tx), -nolisten(no inbound), -onion(Tor), -proxy(connect to peers via a proxy), -whitelist(whitelist some IP address or subnet)
    - Message format:
        - Bitcoin P2P messages contain a header and a payload
        - Header is 24 bytes:
            - Magic(4 bytes): distinguish Bitcoin mainnet/testnet
            - Command name(12 bytes): eg ADDR, INV, BLOCK
            - Payload size(4 bytes)
            - Checksum(4 bytes): double sha256 of payload
        - Payload is upto 32MB and each command has its own defined format
    - Version handshake:
        - P2P connection starts with a version handshake
        - Used by nodes to exchange information about themselves
        - A node responds to a VERSION message with VERACK
    - Other control messages:
        - VERACK: sent in response to a VERSION message
        - ADDR: gossips connection info about other nodes
        - GETADDR: request info about other nodes
        - PING/PONG: confirms connectivity
        - FILTERLOAD/FILTERADD/FILTERCLEAR: sets and unsets bloom filters for SPV tx propagation
    - Inventory announcement:
        - new tx are announced in an INV message
        - INV messages contain the txid
        - if a node wants the announced inventory, it responds with a GETDATA message, announcing node then sends a TX message
    - Block propagation improvements over time: 'heardfirst syncing',SENDHEADERS, compact blocks, high bandwidth compact blocks 
    - INV(block hash) > GETHEADERS(locator) > GETDATA(block hash) > HEADERS > BLOCK(raw block)
    - SENDCMPCT > BLOCK(raw block)->CMPCTBLOCK(header + short ids) > GETBLOCKTXN > BLOCKTXN(txs)
    - Mempool(memory pool/unconfirmed txs):
        - Transactions that are propagated around the network using INVs are stored in nodes' mempools
        - Miners select the txs for the next block from their mempool based on fee rates
        - Nodes verify tx validity before accepting the tx to the mempool, nodes also check txs against standardness rules, double spends are not allowed in the mempool, tx chains are allowed in the mempool(=> child/ancestor txs: ~upto 25 levels deep)
        - Mempool limiting and eviction: tx expiry ages out old txs(default 14 days), also limiting the mempool size(default 300MB), when mempool is full we evict the lowest-fee paying txs, fee-rate calculation is done by "package"(child/ancestor txs), FEEFILTER can reject txs below certain fee
        - RBF: to prevent a stuck tx, Opt-in RBF in BIP 125: allows user to signal that their tx can be replaced later, RBF could potentially be used as a DoS vector, RBF tx has to pay higher fee than the sum paid by the original txs, it shouldn't contain any new unconfirmed inputs, it pays for its own bandwidth(some min. delta), max replacements allowed is 100 txs
    - Transaction caching: most txs are seen twice, from mempool first and when they're included in the block later, so rather than fully validate the tx twice, we can _**partially**_ cache the result of the first validation, caching makes block validation faster hence making block propagation much faster. Transactions are contextual, validity depends on data outside the tx so entire tx can't be cached(for instance could depend on nLockTime, one tx could be valid in one block and invalid in another)
    - Signature caching: signature validation is the most expensive part of transaction validation. Each tx input involves at least one ECDSA signature validation, keeps a cache of signature evaluations
    - Script caching: Bitcoin script validity is context-free. It doesn't depend on any data outside the tx, caches the validity of the entire scriptSig in each tx input.
    - Compact Blocks: BIP 152, included in v12. Saves block propagation bandwidth and time by not including all the raw txs in the block(low bandwidth and high bandwidth). Uses P2P message: CMPCTBLOCK. Refers to tx by shortid(6 bytes digest using SipHash-2-4)
    - Fee estimation: Bitcoin txs include an implicit tx fee, miners choose which txs to include based on fee rate which depends on current level of demand for blockchain space, estimating how much is required is a hard problem, use mempool for fee estimation but expected time to wait for a block is always 10 minutes, doesn't account for lucky/unlucky runs(longer delay vs shorter delay for next block to get mined), or use recent blocks for fee estimation but gameable by miners by stuffing his block with private, high fee paying txs to break users' fee estimates. Using recent blocks and the mempool combined is another way, requires larger sample of recent txs to make a good estimate.


**P2P discovery notes:**
- First time node is up, it uses DNS seeds which contains IP addresses of some known nodes, if node is restarting then it uses the persisted list of previously connected nodes
    - On a restart, after certain time(11 seconds) if the node has only 2 outbound connections then it utilizes DNS seeds
    - DNS seeds implementation [code(init.cpp and net.cpp)](https://github.com/bitcoin/bitcoin/commit/f684aec4f38d6a9e48e870ca5dae6bd65da516cf#diff-bd7a8d248657627e5a6021712731726335cb23daa765870609aab0f7202a2894), it used to use IRC seeding prior to this
    - Connecting to peers is done by sending a version message and then receiving a verack back, once connected then it can use the  [getaddr](https://developer.bitcoin.org/reference/p2p_networking.html#getaddr) command is issued to get the IP addresses of additional peers
    - maintains a tried table(64 buckets) and a new table(256 buckets)
    - `anchors.dat` is created on shutdown to save 2 last known outgoing block-relay-only peers, at startup are re-connected and file is deleted. This is to mitigate the eclipse attack.

**Bitcoin networking partitioning ([Ethan Heilman talk](https://www.youtube.com/watch?v=StnOVBbIpD8))**
- By default a node has: 8 outbound tcp conns, upto 116 inbound, 1 feeler conn(short-lived outbound)
- New/tried table: IPs heard/IPs prev connected
- Eclipse attack examples: 
    - 51% attack with 40% mining power
    - N-conf double spending
    - Attack on L2 protcols(censor breach-remedy tx to steal funds)
    - Privacy: determine if an node originated a tx
    - Forks by double-spending
- How can eclipse can happen: on-path or in-path attacks, off-path attack manipulate p2p network, DNS attacks poison the tables of a new node
- Eclipe new nodes via DNS: control some of the seeders/control the local DNS server/DNS cache poisoning/stuffing seeders with attacker IPs



---
**References**
- [Bitcoin core file system](https://github.com/bitcoin/bitcoin/blob/master/doc/files.md)
- [P2P developer reference](https://developer.bitcoin.org/reference/p2p_networking.html)
- [DNS seed policy and link to bitcoin-seeder](https://github.com/bitcoin/bitcoin/blob/master/doc/dnsseed-policy.md)
- [TxProbe paper: discovering topology using orphan txs](https://arxiv.org/abs/1812.00942)
- [Bitcoin Monitoring](https://www.dsn.kastel.kit.edu/bitcoin/videos.html)
