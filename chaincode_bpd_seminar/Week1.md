## Week 1 : Intro to BPD


**Other questions from reading**
    
- Reclaiming disk space: why can't optional storage of all unspent tx be compressed and stored and appended live using code within that does it?
- SPV: why no alerting ever implemented from nodes to light clients?    
- Privacy: why not coinjoin by default?
- Finney attack: how easy is it to perform such an attack?
- What was missing in b-money and bitgold
- Why not remove the 13 checkpoints upto 2014 in Bitcoin-core sooner than later? for performance reasons. How much of performance gain that is? 
- Why are SPV offer bad privacy because of bloom filter querying? What are SPV Fraud Proofs? Why doesn't everyone use something like BitGoD(hybrid node: SPV+addl_verification)?
- "Yeah, so I think it’s useful to think about the transaction relay network separately from the block propagation network. I would call a full node something that is on the propagation network and is validating the blockchain, and then on top of that, you can also have a mempool with relay transactions. Those are two separate functions and two separate networks. They just happen to share the same mode of transport right now." ---> Tor node probably does neither because it does no outbound?

---

**Other questions assigned to the breakout group**:

- What is the difference between the block propagation network and the transaction relay network? How does participating in one or the other impact your definition of a full node?
    - John: "Yeah, so I think it’s useful to think about the transaction relay network separately from the block propagation network. I would call a full node something that is on the propagation network and is validating the blockchain, and then on top of that, you can also have a mempool with relay transactions. Those are two separate functions and two separate networks. They just happen to share the same mode of transport right now. But that’s not necessary."
    - if run separately, 0-conf will have 0-trust?

-  How have show-stopping bugs or disruptions to the network been handled in the past?
        - there were hard-coded checkpoints
        - critical overflow bug patches(where these hard fork? it says reorg)

---

**What is a Sybil Attack, how was it solved in the past, how does PoW resolve it for the new nodes joining the network?**
- In an open P2P network, there is no registration of nodes. Thus an adversary can create enough Sybils(sockpuppet nodes), to overcome consensus guarantees of the system. (Closed network example: Elon vs Twitter on sockpuppet accounts)
- Sybil attacks ; Spam ; denial of service(DoS) are similar. PoW addresses all of these.
- The PoW instance has to be specific to that transaction and the recipient and there should be no trapdoor(central authority that could bypass the work)
- **In the past** most networks had a centralized authority maintaining a logic(heurestics) or blacklist(in case of spam)
- **PoW** resolves it for new nodes : proof of work requires certain amount of hashpower to be expended in order to publish a block(mining node) and cryptographic guarantees are used (linking of previous hashes)
- **Questions**:
    - In the scenario, when a node determines that one or more of it's peer is a malicious node? does it try to  switch that peer and does it issue any alert?
    - How is bitnodes.io able to discover all of the nodes of the network and how does it idenitfy Tor nodes?
    - Can there be simulations and experiments to mimic different attacks or be able to try on testnet? 
    - Why can't be there a small incentive(from tx fee perhaps) to run honest full-nodes based on uptime or some other heurestic?
    - Difference between eclipse attack and Sybil attack? [link](https://bitcoin.stackexchange.com/questions/61151/eclipse-attack-vs-sybil-attack)
    - In case of running a full node over Tor? does it increase the chance of Sybil attack?
    - If running Tor node and there's a Tor outage, does the node become vulnerable to any attacks or it just can't find peers?

    
---

**Links shared during meetup**:
* https://bitnodes.io/
* https://github.com/bitcoin/bitcoin/pull/17428
* https://github.com/bitcoin/bitcoin/blob/master/doc/bips.md
* https://bitcoincore.org/en/2016/06/07/compact-blocks-faq/