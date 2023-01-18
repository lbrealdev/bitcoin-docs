### xvrn


**Motivation: Utilize existing FOSS and add minimal tooling around it for effective usage and adoption of Bitcoin and it's adjacent software stack which in turn helps towards the goal of censorship-resistant consumer payments.**

---
#### 1. Running required software in a trustless and easy way:
- Required software: [bitcoin-core](https://bitcoin.org/en/download) and [core-lightning](https://corelightning.org/)
- Supplementary software: [BTCPay](https://btcpayserver.org/), [RTL](https://www.ridethelightning.info/), [core-ln plugins](https://lightning.readthedocs.io/PLUGINS.html) and [LNBits](https://lnbits.com/)
- Software modes: allow regtest, testnet, mainnet, pruned, pruned with snapshot
- Status: [initial stages](https://github.com/PradyumnaKrishna/bitnode/issues/1)
- Potential add-ons: [nostr relays](https://www.nostr.net/), [Caddy](https://caddyserver.com/)(for reverse proxy needs)
- Challenges: 
    - Initial block download(IBD) time, disk-space and compute resources for bitcoin-core
    - Channel initialization for lightning(#2 below)
- References:
    - [regtest w docker tutorial](https://www.willianantunes.com/blog/2022/04/bitcoin-node-with-regtest-mode-using-docker/) | [learning bitcoin from cli](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line)
    - [installing core-lightning](https://lightning.readthedocs.io/INSTALL.html)
    - [pruned](https://bitcoin.stackexchange.com/questions/92769/bitcoin-full-node-how-to-run-a-pruned-node-explaining-pruning) snapshot: [btcpay fastsync](https://docs.btcpayserver.org/Docker/fastsync/) | [specter snapshot](https://prunednode.today/) | [eznode](https://ezno.de/)
    - [assumeutxo](https://bitcoinops.org/en/topics/assumeutxo/)
    - [bitcoin-core guix](https://github.com/fanquake/core-review/blob/master/guix/README.md)
    - [building bitcoin for android](https://rusnak.io/how-to-build-bitcoin-for-android/)
    - [Umbrel](https://umbrel.com/) | [Start9](https://start9.com/) | [mynode](https://mynodebtc.com/) | [nodl](https://www.nodl.eu/)
    

---
#### 2. Lightning network software:
- Create simplified solution for initializing channels and liquidity
- Potential add-ons: self-hosted publishing software(lightweight LN native micro-blog, newsletters etc.) and applications of nostr protocol for this
- Status: TBD
- Challenges: 
    - Setting up initial channels and liquidity
    - Channel rebalancing management
    - Avoiding centralized services for swaps
- References:
    - [Understanding lightning network](https://bitcoinmagazine.com/technical/understanding-the-lightning-network-part-building-a-bidirectional-payment-channel-1464710791)
    - [LSP](https://medium.com/breez-technology/introducing-lightning-service-providers-fe9fb1665d5f)
    - [Peerswap](https://www.peerswap.dev/)
    - [Splicing](https://thebitcoinmanual.com/articles/splicing-lightning-network/)


---

#### 3. Simplified key management/security guides:
- Create simplified guide using existing best practices for key management and security(for [bip39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki), passphrases, psbt(offline signing), [multi-sig](https://en.bitcoin.it/wiki/Multi-signature), hw and sw wallets)
- Status: TBD
- Challenges: 
    - Loss of private keys: inability to access or keys getting stolen
    - Safe and validated backups
    - Easy to way to manage many keys and backups in case of a multi-sig setup
    - Using high entropy passphrases on single-sig setups(if applied out of need) and storing them safely
- References:
    - [BIP39 original discussion](https://github.com/bitcoin/bips/pull/17)
    - [Sparrow wallet](https://sparrowwallet.com/) | [Coldcard hw wallet](https://blog.coinkite.com/understanding-mk4-security-model/) | [SeedSigner(DIY)](https://seedsigner.com/)
    - Vaults: [Revault](https://revault.dev/#howItWorks) | [OP_VAULT](https://github.com/bitcoin/bitcoin/pull/26857)
    - Key management in other protocols: [lightning: vls](https://vls.tech/) | [nostr](https://bitcoinmagazine.com/technical/solving-nostr-key-management-issues)
    - A passphrase selection [experiment](https://github.com/ns-xvrn/guided_passphrase)