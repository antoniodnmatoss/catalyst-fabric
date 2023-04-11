# Commit a Chaincode to the Channel

Chaincode is a smart contract of Hyperledger Fabric that handles the business logic of a particular application. A smart contract programmatically accesses two distinct pieces of the ledger – a blockchain, which immutably records the history of all transactions and a world state that holds a cache of the current value of these states.&#x20;

In other words, a chaincode is programmable code that runs on the peers and facilitates transactions on a Hyperledger Fabric blockchain application allowing the users to update the world state of the assets.

The Catalyst Blockchain Manager Hyperledger Fabric service supports Hyperledger Fabric v2.0 capabilities with the following [chaincode lifecycle:](https://hyperledger-fabric.readthedocs.io/en/release-2.2/chaincode\_lifecycle.html#fabric-chaincode-lifecycle)

![Committing chaincode to the channel flow](<../.gitbook/assets/image (158).png>)

&#x20;1\. [**Add a chaincode**](../cc-mngmnt/chaincode-management.md#how-to-add-a-chaincode-in-catalyst-blockchain-platform)

The current version of Catalyst Blockchain Manager supports external chaincodes. Organizations do not need to package the chaincode, rather they just need to create a Docker image and provide a link to the Docker repository while adding chaincode in Catalyst Blockchain Manager.

2\. [**Install the chaincode on your peers.**](../cc-mngmnt/chaincode-management.md#how-to-install-chaincode-on-a-peer)

Every organization that will use the chaincode to endorse a transaction or query the ledger needs to complete this step.

3\. [**Approve a chaincode definition for your organization.**](../cc-mngmnt/chaincode-management.md#how-to-approve-chaincode-on-a-channel)

Every organization that will use the chaincode needs to complete this step. The chaincode definition needs to be approved by a sufficient number of organizations with endorser rights to satisfy the channel’s policy before the chaincode can be started on the channel.

4\. [**Commit the chaincode definition.**](../cc-mngmnt/chaincode-management.md#how-to-commit-a-chaincode)

Any organization could commit a chaincode if all necessary approvals were done.

Detailed information about the chaincode lifecycle in Catalyst Blockchain Manager can be found [here.](../cc-mngmnt/chaincode-management.md#chaincodes-lifecycle)
