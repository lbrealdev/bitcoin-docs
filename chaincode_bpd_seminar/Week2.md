#### Week 2 : SegWit


#### Group questions:

- [Nicholas' notes](https://curly-father-00b.notion.site/Seminar-2-Segwit-da38abcd12cf46e3b4ffd65388b8582a)

- Is a non-segwit node considered a full node?
    - Yes, Bitcoin is backward-compatible but they can't parse the witness data?
- How is ECDSA malleable?
    - one of the inputs can have multiple values
- How Could BIP9 be considered controversial within the community? How was BIP148 received when first proposed?
    - BIP9 gives too much control of activation to the miners alone, BIP148 (UASF to implement SegWit) had mixed reviews - sdaftuar rejected it on the basis of being disruptive consensus rule change which could lead to network split
- **How do blocks commit to witness data?**
    - into the output of the coinbase tx
    - uses same merkle tree structure as tx data
    - the root for witness data is separated from tx data
    - wtxid are used instead of txid
    - one of the utxo in the output of the coinbase tx is used to add [OP_RETURN](https://en.bitcoin.it/wiki/OP_RETURN) to any [data](https://bitcoindev.network/guides/bitcoinjs-lib/embedding-data-with-op_return/) ([ECDH/BIP 47](https://en.bitcoin.it/wiki/ECDH_address))
    - **Questions:**
        - Is storing data or opentimestamps on bitcoin blockchain a bad practice?
        - Other than the size, is there any restriction on what can go into the OP_RETURN as data/Base58 etc?



---

#### Additional doubts/questions:
- Does the BIP9 signalling depend on decision of mining pools?
- What would have happened if UASF didn't get introducded via BIP148?
- Why not 60% or 65% instead of 80% threshold on BIP9->BIP91? 
- How can one reliably know how many nodes on the entire network are SegWit vs non-Segwit?
- Is there any technical reason for not upgrading to SegWit at the moment?
- How are fraud proofs being utilized post SegWit?
- Give time for IBLT and weak blocks to develop?(from Pieter's scaling Bitcoin talk of 2015)
- Pieter Wuille in his talk on bech32 address mentions that in the future potentially there will be no need for addresses, what does that exactly look like? nyms?
- If something like ASICBOOST is still available and at a same or greater practical efficiency, how detrimental that could be to the Bitcoin network?

---

#### Notes:

**[Address](https://bitcoin.design/guide/glossary/address/) types**:
- P2PKH(pay to pubkey hash)/legacy addr: starts with 1
- P2SH(pay to script hash)/script addr: starts with 3, multi-sig txs
- Native SegWit(P2WPKH, P2WSH): bech32 address starts with bc1q
- SegWit(P2WSH)/P2SH-nested-SegWit: starts with 3
- P2TR(pay to taproot)/bech32m: starts with bc1p

**Scaling Bitcoin(Pieter Wuille) - Hong Kong, [2015](https://www.youtube.com/watch?v=fst1IK_mrng)**, SegWit adds following:
- Fixes malleability
- Enables far simpler script upgrades
- Enables fraud proofs
- Allows pruning witness data for historical data
- Reduces bandwidth for light nodes and historical sync
- P2SH compatible for old senders
- Give time for IBLT and weak blocks to develop



**[Advanced SegWit](https://www.youtube.com/watch?v=JgNgnwF9hfY)(James O'Berine)**

>txid:  nVersion|txins|txouts|nLockTime
>wtxid:  nVersion|marker|flag|txins|txouts|witness|nLockTime

_SegWit benefits:_
- Malleability fix
- No quadratic sighash(if number of inputs inrease the computation for validation increases quadratically)
- Better p2sh security: SHA256+RIPEMD -> from 20 bytes to 32 bytes?
    >HASH160 <20 bytes> EQUAL vs OP_0 <32 bytes>
- Script upgradeability
- Block size increase: 4MB(1.6-2MB in practice)

_Additional notes_:
>_Hard forks vs Soft forks:_
>- Hard forks make that version backward-incompatible
>- Hard forks validates something that was previously invalid in the consensus/rules

- BIP9: established a standard framework for activating soft fork upgrades, read [more](https://river.com/learn/terms/b/bip-9-soft-fork-activation/), tldr: using version bit of Bitcoin blocks miners signal by setting chosen bit to 0 or 1 in the blocks that they mine seeking 95% support weighted by hashrate in a fixed timeperiod.

- BIP148: UASF in bitcoin that enforces the use of SegWit 

- BIP91: activation threshold down to 80% from 95% but never got merged into Bitcoin core?, says final [here](https://bips.xyz/91)

- [ASICBOOST](https://www.mit.edu/~jlrubin//public/pdfs/Asicboost.pdf) --> [the scandal explained](https://bitcoinmagazine.com/technical/breaking-down-bitcoins-asicboost-scandal)

- [BIP144](https://github.com/bitcoin/bips/blob/master/bip-0144.mediawiki) : new serialization format in SegWit upgrade, new messages(getdata): MSG_WITNESS_TX, MSG_WITNESS_BLOCK (inv types), new service NODE_WITNESS


**[Bech32 addresses](https://www.youtube.com/watch?v=NqiN9VFE4CU)(Pieter Wuille)**
- Case insensitive: easier to read/write
- Simple to convert(no bignum logic)
- Only 17% larger than Base 58
- Better checksums for prime power
- More compact in QR
- Base 32 is already used in onion addresses, tor, i2p etc.
- Uses error correcting coding theory: [BCH Codes](https://en.wikipedia.org/wiki/BCH_code)
- bc1qw508....v8f.... : human readable part(bc)+separator(1)+data part, base 32 encoding (witness version+witness program+checksum), length 42 (P2WPKH), 62(P2WSH)

---
#### Further reading/refs:

- [Weight Units, vbytes](https://en.bitcoin.it/wiki/Weight_units)
- [Message types - protocol documentation](https://en.bitcoin.it/wiki/Protocol_documentation#Message_types)
- [Modern soft fork activation](https://bitcoinmagazine.com/technical/bip-8-bip-9-or-modern-soft-fork-activation-how-bitcoin-could-upgrade-next)
- [Taproot soft fork](https://taproot.watch/about)
- [Speedy trial](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2021-March/018583.html)
- [Is speedy trial the way to change Bitcoin?](https://bitcoinmagazine.com/technical/is-speedy-trial-the-way-to-change-bitcoin)
- [BIP119 controversy](https://bitcoinmagazine.com/technical/what-is-bip-119-bitcoin-controversy-explained)
- [opentimestamps](https://github.com/opentimestamps)
- [Bitcoin Explained podcast, OP_RETURN](https://www.youtube.com/watch?v=NYj80OGlWGg)