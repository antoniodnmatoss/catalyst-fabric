# Component Overview

Catalyst Blockchain Manager leverages HyperLedger Fabric inner components that play together in order to enable the configuration and development of Blockchain networks and the use cases implemented on top of them.\
A more comprehensive overview of Hyperledger Fabric can be found[ here.](https://hyperledger-fabric.readthedocs.io/en/release-2.5/)

## Peer Nodes

Blockchain networks are composed by a list of organizations that interact in a decentralized and distributed _manner_. Each organization manages its nodes and identities that are required to enable its participation on that network. Therefore, a Peer Node is the component that represents its Organization in the physical network, acting both as a client and a server.\
It participates in the creation and management of blockchain ledgers, as well as the validation of transactions and blocks. \
\
A Peer Node stores a copy of the ledger and acts as an endpoint for executing chaincode and sending proposals to endorsers. They also serve as a point of contact for other nodes to query the blockchain ledger and validate transactions.\
\
Their main responsibilities are :

1. Ledger replication: Peer Nodes ensure that a copy of the ledger is stored on multiple nodes, providing robustness and reliability to the network.
2. Validation: Peer Nodes validate transactions and blocks before they are added to the ledger, ensuring the integrity of the network.
3. Chaincode execution: Peer Nodes execute chaincode, which is the business logic of the network, on behalf of client applications.
4. Endorsing transactions: Peer Nodes endorse transactions by providing their own signatures, which are required before a transaction can be committed to the ledger.
5. Accessibility: Peer Nodes serve as points of contact for other nodes to access the blockchain ledger and perform queries. This enables a decentralized network where no single node holds all the data.

For a more detailed explanation check [Peer Nodes documentation](https://hyperledger-fabric.readthedocs.io/en/release-2.5/peers/peers.html).

## Certificate Authorities

One of the main features of Hyperledger Fabric is that it enables the trust that everyone in the network is who claims to be. Being a permissioned protocol, every actor in the Hyperledger Fabric network needs a specific identity from an authorized entity. Every time a user or entity performs an activity in the network, they do so by authorizing the use of their identity

To achieve this, the role of a Certificate authority is crucial as it  issues the certificates that are implicitly used by every component of an organization.\
These certificates can be seen as  data files used to cryptographically identify an entity.

For a more detailed explanation check [Certificate Authorities documentation.](https://hyperledger-fabric.readthedocs.io/en/release-2.5/identity/identity.html#what-is-an-identity)

## Ordering Service

The Ordering Service in Hyperledger Fabric is a component that provides a reliable, scalable, and centralized mechanism for ensuring the consistency of transactions across all Peer Nodes in a network, enabling the efficient and consistent ordering of transactions and the maintenance of a tamper-resistant ledger.

The Ordering Service is responsible for creating blocks of transactions, assigning each block a unique identifier, and distributing the blocks to all Peer Nodes in the network. This ensures that all every nodes has the same view of the ledger.

it can be seen as a cluster of nodes, providing redundancy and resilience in case of failures. This  ensure sthat the ordering process continues even if a node goes down, maintaining the consistency and reliability of the network.

For a more detailed explanation check [Ordering Service documentation.](https://hyperledger-fabric.readthedocs.io/en/release-2.5/orderer/ordering\_service.html)

## Channel

A Channel in Hyperledger Fabric is the communication mean between participating Peer Nodes, through which they transact and maintain a shared ledger. It acts as a separate and isolated ledger, which enables parties to transact confidentially with each other without revealing the details of their transactions to other parties in the network, provideing a flexible and secure way for parties to transact and maintain a shared ledger.

In a Hyperledger Fabric network, each Channel has its own chain of blocks, called the channel ledger, which contains a specific set of transactions. The channel ledger is maintained by the Peer Nodes that have joined the Channel and is kept separate from the ledgers maintained by other Channels in the network.

The Ordering Service in Hyperledger Fabric operates at the Channel level, ordering transactions within the Channel and distributing the ordered transactions to the participating Peer Nodes. This enables transactions within a Channel to be processed efficiently and consistently, while maintaining the privacy and confidentiality of the transaction details.

For a more detailed explanation check [Channel documentation.](https://hyperledger-fabric.readthedocs.io/en/release-2.2/channels.html)

## Smart Contract and Chaincode

A Smart Contract in Hyperledger Fabric is a set of instructions and rules (program) that runs on the blockchain and defines the terms of an agreement between parties. It contains the business logic for executing transactions and updating the ledger, and provides a secure and transparent way for parties to interact with the blockchain.

In Hyperledger Fabric, Smart Contracts are implemented as Chaincode, which is a program written in a supported programming language, such as Go or Node.js. Chaincode contains the logic for executing transactions, updating the ledger, and responding to queries from client applications.

Chaincode is deployed to the Peer Nodes in a network and is executed by the Peer Nodes on behalf of client applications. When a client application submits a transaction proposal to the network, the endorsing Peer Nodes execute the Chaincode to simulate the proposed transaction and produce a set of read/write sets that reflect the proposed changes to the ledger.

Chaincode also provides a secure and transparent way for parties to interact with the blockchain. Transactions are processed in a secure and auditable environment, with Chaincode providing the business logic for executing transactions and updating the ledger.

For a more detailed explanation check [Smart Contract documentation.](https://hyperledger-fabric.readthedocs.io/en/release-2.2/smartcontract/smartcontract.html)

## Application

Last but not least, an Application in Hyperledger Fabric is the client software that provides a connection between users and the blockchain network. These applications allow users to perform transactions, retrieve information from the ledger, and receive updates on the network's state. Transactions initiated by the application are processed by the Peer Nodes and recorded on the ledger.

Applications use certificates to authenticate themselves and establish trust with the Peer Nodes and Certificate Authorities. They can connect to multiple Channels in the network, each of which represents a separate ledger, and submit transactions to be processed by the Chaincode deployed on the Peer Nodes.

The Application also receives updates from the network, such as transaction confirmations and ledger updates, allowing it to track the state of the blockchain and take appropriate actions based on the data it receives.

In summary, Applications in Hyperledger Fabric provide the interface between the blockchain network and the users of the network, enabling them to interact with the blockchain and perform transactions, query the ledger, and receive updates from the network.

For a more detailed explanation check [Hyperledger Fabric documentation.](https://hyperledger-fabric.readthedocs.io/en/release-2.2/developapps/application.html?highlight=application)
