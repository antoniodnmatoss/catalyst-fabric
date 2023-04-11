# Build a Network

The blockchain network is fully decentralized and can be managed by each member. However, there should be one organization that bootstraps the network.

To build a network, you should do the following:

1. Create an organization.
2. Bootstrap the system channel (in other words, generate the genesis block for the system channel).

![Build a network](https://lh5.googleusercontent.com/MFuIK9b3R4YwU-lvYsokZTjb2C8xrDfGxrYthXIZdgG\_N1IZN-86lmrzw3l3T0o9cCG33Od7MCP7AQuElbhHZ\_hm0ZYV-PmLT7voN7C5VeZc5EsssKy0lynytepda1g7UFDFHgVa)

## **Create an Organization**

Every organization should have its own CA(s), MSP definition, ordering service with ordering node(s), and peer set(s) with peer node(s).

{% hint style="info" %}
_Info:_ Starting from Catalyst Blockchain Manager v. 2.3.0 you can onboard a new organization to a network even if it doesn’t host any ordering node. Please, refer to[ this section ](join-a-network.md#joining-a-system-channel-with-an-external-orderer)to read more.
{% endhint %}

![Creating an organization flow](https://lh3.googleusercontent.com/9Kgqmca9Jm5uIMBvaHsV2AmG7v1WTkxZD--qy\_k6Q-KvN1rW3\_hQSjwbg2lqcYatGKVSPfxOb-kPGqdGvbLGpwSyMW459tT728gATZ4kT5YF-UaNyyNb\_4oiDn34WRxOhhu\_qG4b)

To create an organization, you need to do the following.

&#x20;**1. Create a Certificate Authority (CA).**

CA is the first entity to be created while bootstrapping a network. Each organization needs to have its own CA. A CA issues certificates to other entities on the network, which define the identity of an entity on the blockchain.

You can create a root (self-signed) CA or create an intermediate CA instead and sign its certificate by any trusted CA. If you want to create an intermediate CA, you need to create a CA and then follow the steps shown in the diagram below.

![Intermediate CA creation flow](<../.gitbook/assets/image (63).png>)

You can see detailed information about how to create and sign a CA in [this section.](../network-and-node-mngmnt/certificate-authority.md#how-to-create-a-ca)

**2. Create an Organization's MSP Definition**

The Membership Service Provider (MSP) identifies which CAs are accepted to define the members of a trusted domain by listing their members' identities or identifying which CAs are authorized to issue their members' valid identities.

An ordering service with ordering nodes and peer sets with peer nodes belong to your organization. Therefore, an organization's MSP needs to be created before the ordering service and peer sets. To learn more about MSPs and how to create an MSP, please refer to [this section.](../network-and-node-mngmnt/msps-and-partners.md#how-to-create-an-organizations-msp)

**3. Create Ordering Set(s)**

By creating an ordering set, you can standardize the desired configurations. By simply adding a new orderer to this ordering set, you can quickly create any number of orderers with the predefined set of configurations. For a detailed guide, refer to [this section.](../network-and-node-mngmnt/ordering-service.md#how-to-create-an-ordering-set)

{% hint style="info" %}
_Info:_ Starting from Catalyst Blockchain Manager v. 2.3.0 you can onboard a new organization to a network even if it doesn’t host any ordering node. Please, refer to[ this section ](join-a-network.md#joining-a-system-channel-with-an-external-orderer)to read more.
{% endhint %}

**4. Create Ordering Node(s)**

Ordering nodes (or orderers) are responsible for maintaining the system and application channels, ordering transactions, and packaging them into blocks.

{% hint style="warning" %}
_Note:_ We recommend creating at least three ordering nodes while bootstrapping the network in order to be crash fault-tolerant.
{% endhint %}

{% hint style="danger" %}
_Warning:_ If you add other organizations at the system channel's creation step, your organization needs to have more orderers than the number of organizations you are going to add. If this occurs, a quorum will not be achieved.
{% endhint %}

See details [here.](../network-and-node-mngmnt/ordering-service.md#how-to-create-an-orderer)

**5. Create Peer Set**

Just like an ordering set for orderers, a peer set is a template for peers. For a detailed guide, refer to [this section.](../network-and-node-mngmnt/peer-set.md#how-to-create-a-peer-set)

**6. Create Peer Node(s)**

Peers are the nodes or participants in a blockchain network that host a copy of the distributed ledger and smart contracts. There is no limit on peers' number. See how to create a peer [here.](../network-and-node-mngmnt/peer-set.md#how-to-create-a-peer)

{% hint style="info" %}
Even though an organization was created, a network is not set up yet. You need to bootstrap a system channel or join one if it already exists.
{% endhint %}

## **Bootstrapping the System Channel (Generating the Genesis Block)**

Every network begins with an ordering system channel. The policies in the ordering system channel configuration blocks govern the consensus used by the ordering service and define how new blocks are created.

The system channel also contains the organizations that are the members of the ordering service (orderer organizations) and those allowed to create new channels (consortium organizations).

While creating a system channel you need to:

1. Add orderer organizations.&#x20;
2. Create a consortium.&#x20;

{% hint style="info" %}
_Info:_ Please make sure that your organization is a member of orderer organizations and consortium. You can add other organizations as an orderer organization and a consortium member during this step or after the channel creation.
{% endhint %}

3\. Configure the system channel and Raft options.\
\
See detailed flow [here.](../network-and-node-mngmnt/ordering-service.md#how-to-create-a-system-channel)
