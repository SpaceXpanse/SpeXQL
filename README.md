# SpeXQL /WIP/
## A decentralized, trusted, high performance, SQL database with blockchain features
<!-- 
<p align="center">
    <img src="logo/covenantsql_horizontal.png"
        width="760">
</p>
<p align="center">
    <a href="https://goreportcard.com/report/github.com/CovenantSQL/CovenantSQL">
        <img src="https://goreportcard.com/badge/github.com/CovenantSQL/CovenantSQL?style=flat-square"
            alt="Go Report Card"></a>
    <a href="https://codecov.io/gh/CovenantSQL/CovenantSQL">
        <img src="https://codecov.io/gh/CovenantSQL/CovenantSQL/branch/develop/graph/badge.svg"
            alt="Coverage"></a>
    <a href="https://travis-ci.org/CovenantSQL/CovenantSQL">
        <img src="https://travis-ci.org/CovenantSQL/CovenantSQL.png?branch=develop"
            alt="Build Status"/></a>
    <a href="https://opensource.org/licenses/Apache-2.0">
        <img src="https://img.shields.io/badge/License-Apache%202.0-blue.svg"
            alt="License"></a>
    <a href="https://godoc.org/github.com/CovenantSQL/CovenantSQL">
        <img src="https://img.shields.io/badge/godoc-reference-blue.svg"
            alt="GoDoc"></a>
    <a href="https://formulae.brew.sh/formula/cql">
        <img src="https://img.shields.io/homebrew/v/cql.svg?color=blue&label=brew%20install%20cql"
            alt="homebrew"></a>
</p>

[中文简介](https://github.com/CovenantSQL/CovenantSQL/blob/develop/README-zh.md)
-->

**SpaceXpanseSQL (SpeXQL)** /derived from CovenantSQL/ or just **XQL** for short is a **Byzantine Fault Tolerant** relational database built on SQLite:

- **ServerLess**: Free, High Availabile, Auto Sync Database Service for Serverless App
- **SQL**: Most SQL-92 support.
- **Decentralize**: Running on Open Internet without Central Coordination.
- **Privacy**: Access with Granted Permission and Encryption Pass.
- **Immutable**: Query History in XQL is Immutable and Trackable.
- **Permission**: Column Level ACL and SQL Pattern Whitelist.

## What is XQL?

- Open source alternative of Amazon QLDB
- Low cost DBaaS
- Just like filecoin + IPFS is the decentralized file system, XQL is the decentralized database

## Quick Start

XQL client supports:

- macOS X 10.9+
- Linux 2.6.23+ (x86, x86_64, armeabi-v7a, arm64-v8a)

<details><summary>Developer Guide</summary>
<p>

#### MacOS
- 🍺 Homebrew users can just run:

    ```bash
    brew install cql
    ```

- non-Homebrew users can run:
    
    ```bash
    sudo bash -c 'curl -L "https://mac.gridb.io/cql" | tar xzv -C /usr/local/bin/ --strip-components=1'
    ```

#### Linux

- Just run：

    ```bash
    sudo bash -c 'curl -L "https://linux.gridb.io/cql" | tar xzv -C /usr/local/bin/ --strip-components=1'
    ```

#### To continue: [TestNet Quickstart](https://developers.covenantsql.io/docs/en/quickstart)

#### SDKs

CovenantSQL Testnet is already released, [have a try](https://developers.covenantsql.io/docs/quickstart).

- [Golang](client/)
- [Java](https://github.com/CovenantSQL/cql-java-driver)
- [NodeJS](https://github.com/CovenantSQL/covenantsql-proxy-js)
- [Python](https://github.com/CovenantSQL/cql-python-driver)
- [Microsoft Excel (by community)](https://github.com/melancholiaforever/CQL_Excel)

</p>
</details>


## How XQL works

### 3 Layers Arch

![CovenantSQL 3 Layer design](https://cdn.jsdelivr.net/gh/CovenantSQL/docs@b7143254adb804dff0e3bc1f2f6ab11ad9cd44f5/website/static/img/2layers.svg)

- Layer 1: **Global Consensus Layer** (the main chain, the middle ring in the architecture diagram):
    - There will only be one main chain throughout the network.
    - Mainly responsible for database Miner and the user’s contract matching, transaction settlement, anti-cheating, shard chain lock hash and other global consensus matters.
- Layer 2: **SQL Consensus Layer** (shard chain, rings on both sides):
    - Each database will have its own separate shard chain.
    - Mainly responsible for: the signature, delivery and consistency of the various Transactions of the database. The data history of the permanent traceability is mainly implemented here, and the hash lock is performed in the main chain.
- Layer 3: **Datastore Layer** (database engine with SQL-92 support):
    - Each Database has its own independent distributed engine.
    - Mainly responsible for: database storage & encryption, query processing & signature, efficient indexing.


<details><summary>For more details</summary>
<p>

### Consensus Algorithm

XQL supports 2 kinds of consensus algorithm:

1. DPoS (Delegated Proof-of-Stake) is applied in `Eventually consistency mode` database and also `Layer 1 (Global Consensus Layer)` in BlockProducer. XQL miners pack all SQL queries and its signatures by the client into blocks thus form a blockchain. We named the algorithm [`Xenomint`](https://github.com/CovenantSQL/CovenantSQL/tree/develop/xenomint). 
2. BFT-Raft (Byzantine Fault-Toleranted Raft)<sup>[bft-raft](#bft-raft)</sup> is applied in `Strong consistency mode` database. We named our implementation [`Kayak`](https://github.com/CovenantSQL/CovenantSQL/tree/develop/kayak).  The XQL miner leader does a `Two-Phase Commit` with `Kayak` to support `Transaction`.<sup>[transaction](#transaction)</sup>

XQL database consistency mode and node count can be selected in database creation with command  `cql create '{"UseEventualConsistency": true, "Node": 3}'`

## Comparison

|                              | Ethereum          | Hyperledger Fabric     | Amazon QLDB | CovenantSQL                                                  |
| ---------------------------- | ----------------- | ---------------------- | ----------- | ------------------------------------------------------------ |
| **Dev language**             | Solidity, ewasm   | Chaincode (Go, NodeJS) | ?           | Python, Golang, Java, PHP, NodeJS, MatLab                    |
| **Dev Pattern**              | Smart   Contract  | Chaincode              | SQL         | SQL                                                          |
| **Open Source**              | Y                 | Y                      | N           | Y                                                            |
| **Nodes for HA**             | 3                 | 15                     | ?           | 3                                                            |
| **Column Level ACL**         | N                 | Y                      | ?           | Y                                                            |
| **Data Format**              | File              | Key-value              | Document    | File<sup>[fuse](#fuse)</sup>, Key-value, Structured          |
| **Storage Encryption**       | N                 | API                    | Y           | Y                                                            |
| **Data Desensitization**     | N                 | N                      | N           | Y                                                            |
| **Multi-tenant**             | DIY               | DIY                    | N           | Y                                                            |
| **Throughput (1s delay)**    | 15~10 tx/s        | 3500 tx/s              | ?           | 11065 tx/s (Eventually Consistency)<br/>1866 tx/s (Strong Consistency) |
| **Consistency Delay**        | 2~6 min           | < 1 s                  | ?           | < 10 ms                                                      |
| **Secure for Open Internet** | Y                 | N                      | Only in AWS | Y                                                            |
| **Consensus**                | PoW + PoS(Casper) | CFT                    | ?           | DPoS (Eventually Consistency)<br/>BFT-Raft (Strong Consistency) |

#### FootNotes

- <a name="bft-raft">BFT-Raft</a>: A XQL leader offline needs XQL Block Producer to decide whether to wait for leader online for data integrity or promote a follower node for availability. This part is still under construction and any advice is welcome.  

- <a name="transaction">Transaction</a>: Talking about `ACID`, XQL has full "Consistency, Isolation, Durability" and a limited `Atomicity` support. That is even under strong consistency mode, XQL transaction is only supported on the leader node. If you want to do "read `v`, `v++`, write `v` back" parallelly and atomically, then the only way is "read `v` from the leader, `v++`, write `v` back to leader"

- <a name="fuse">FUSE</a>: XQL has a [simple FUSE](https://github.com/CovenantSQL/CovenantSQL/tree/develop/cmd/cql-fuse) support adopted from CockroachDB. The performance is not very ideal and still has some issues. But it can pass fio test like:

  ```bash
  fio --debug=io --loops=1 --size=8m --filename=../mnt/fiotest.tmp --stonewall --direct=1 --name=Seqread --bs=128k --rw=read --name=Seqwrite --bs=128k --rw=write --name=4krandread --bs=4k --rw=randread --name=4krandwrite --bs=4k --rw=randwrite
  ```

### Network Stack

[DH-RPC](rpc/) := TLS - Cert + DHT

| Layer              | Implementation |
|:-------------------|:--------------:|
| RPC                |     `net/rpc`    |
| Naming             |      [**C**onsistent **S**ecure **DHT**](https://godoc.org/github.com/CovenantSQL/CovenantSQL/consistent)     |
| Pooling            |  Session Pool  |
| Multiplex          |      [smux](https://github.com/xtaci/smux)     |
| Transport Security |      [**E**nhanced **TLS**](https://github.com/CovenantSQL/research/wiki/ETLS(Enhanced-Transport-Layer-Security))      |
| Network            |       TCP or KCP for optional later      |

### Test Tools we use
  - [**G**lobal **N**etwork **T**opology **E**mulator](https://github.com/CovenantSQL/GNTE) is used for network emulating.
  - [Liner Consistency Test](https://github.com/anishathalye/porcupine)

## Papers

Our team members published:

- [Thunder crystal: a novel crowdsourcing-based content distribution platform](https://dl.acm.org/citation.cfm?id=2736085)
- [Analyzing streaming performance in crowdsourcing-based video service systems](https://ieeexplore.ieee.org/abstract/document/7114727/)
- [Performance Analysis of Thunder Crystal: A Crowdsourcing-Based Video Distribution Platform](https://ieeexplore.ieee.org/abstract/document/7762143/)

that inspired us:

- [Bitcoin: A Peer-to-Peer Electronic Cash System](https://bitcoin.org/bitcoin.pdf)
- [S/Kademlia](https://github.com/thunderdb/research/wiki/Secure-Kademlia)
    - [S/Kademlia: A practicable approach towards secure key-based routing](https://ieeexplore.ieee.org/document/4447808/)
- [vSQL: Verifying arbitrary SQL queries over dynamic outsourced databases](https://ieeexplore.ieee.org/abstract/document/7958614/)


</p>
</details>

## Performance 

Strong consistency bench result (2 miners, 8 core aws c5.2xlarge):
![CovenantSQL bench](logo/bench.png)

As seen above, the concurrency pressure on the database increased gradually in the first 5 hours, and the write TPS also increased. When the TPS no longer grows, the concurrent pressure is maintained for 100 hours.

## Demos

- [CovenantForum](https://demo.covenantsql.io/forum/)
- [Weibo Bot @BlockPin](https://weibo.com/BlockPin)
- [Markdown Editor with CovenantSQL sync](https://github.com/CovenantSQL/stackedit)
- [Web Admin for CovenantSQL](https://github.com/CovenantSQL/adminer)
- [How CovenantSQL works(video)](https://youtu.be/2Mz5POxxaQM?t=106)

## Use cases

<details><summary>Traditional App & ĐApp use cases</summary>
<p>

### Traditional App

#### Privacy data

If you are a developper of password management tools just like [1Password](https://1password.com/) or [LastPass](https://www.lastpass.com/). You can use XQL as the database to take benefits:

1. Serverless: no need to deploy a server to store your user's password for sync which is the hot potato.
2. Security: XQL handles all the encryption work. Decentralized data storage gives more confidence to your users.
3. Regulation: XQL naturally comply with [GDPR](https://en.wikipedia.org/wiki/General_Data_Protection_Regulation).

#### IoT storage

XQL miners are deployed globally, IoT node can write to nearest XQL miner directly.

1. Cheaper: Without passing all the traffic through a gateway, you can save a large bandwidth fee. And, XQL is a shared economic database which makes storage cheaper.
2. Faster: XQL consensus protocol is designed for Internet where network latency is unavoidable.

#### Open data service

For example, you are the most detailed Bitcoin OHLC data maintainer. You can directly expose an online SQL interface to your customers to meet a wide range of query needs.

1. XQL can limit specific SQL query statements to meet the needs while also balancing data security;
2. XQL can record SQL query records on the blockchain, which is very convenient for customers to check their bills for long-tail customers and billing, like [this](https://explorer.dbhub.org/dbs/7a51191ae06afa22595b3904dc558d41057a279393b22650a95a3fc610e1e2df/requests/f466f7bf89d4dd1ece7849ef3cbe5c619c2e6e793c65b31966dbe4c7db0bb072)
3. For customers with high performance requirements, Slave nodes can be deployed at the customer to meet the needs of customers with low latency queries while enabling almost real-time data updates.

#### Secure storage

Thanks to the XQL data history is immutable, XQL can be used as a storage for sensitive operational logs to prevent hacking and erasure access logs.

### ĐApp

Storing data on Bitcoin or Ethereum is quite expensive ($4305 / MB on Ethereum 2018-05-15). Programming is very complicated due to the lack of support for structured data storage. XQL gives you a low-cost structured SQL database and also provides more room for ĐApp to exchange data with real-world.

</p>
</details>


## TestNet

- [Quick Start](https://developers.covenantsql.io)
- [MainChain Explorer](http://scan.covenantsql.io)
- [SQLChain Explorer](https://explorer.dbhub.org)
- [Demo & Forum](https://demo.covenantsql.io/forum/)

## Contact

- [Blog](https://medium.com/@covenant_labs)
- [YouTube](https://www.youtube.com/channel/UCe9P_TMiexSHW2GGV5qBmZw)
- [Mail](mailto:webmaster@covenantsql.io)
- [Forum](https://demo.covenantsql.io/forum/)
- <a href="https://twitter.com/intent/follow?screen_name=CovenantLabs"><img src="https://img.shields.io/twitter/url/https/twitter.com/fold_left.svg?style=social&label=Follow%20%40CovenantLabs" alt="follow on Twitter"></a>
- [![Join the chat at https://gitter.im/CovenantSQL/CovenantSQL](https://badges.gitter.im/CovenantSQL/CovenantSQL.svg)](https://gitter.im/CovenantSQL/CovenantSQL?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
- Add `CovenantSQL` to join WeChat group
    <p align="left">
        <img src="logo/wechat.jpeg" height="100">
    </p>
