# Join a Network

After successfully bootstrapping the network, other organizations can join it. Joining the network means joining your ordering nodes to the ordering system channel and fetching or uploading its genesis block.

{% hint style="info" %}
_Info:_ Starting from Catalyst Blockchain Manager v. 2.3 you can onboard a new organization to the network even if it doesn’t host any ordering node. Please, refer to [this section](join-a-network.md#joining-a-system-channel-with-an-external-orderer) to read how to join a network with an external orderer.
{% endhint %}

![Joining a network flow](https://lh4.googleusercontent.com/7-dSKJuyYy1f522diZ\_HxKUj1IdCD1d0fUYUquqHnh\_l9GYlNe3PMN9ftTwT\_-rS4EJ1SDkhCtyX1fG6lQcb9QehuCtnBCrYGi6R\_ch-F\_C8zHWAH9qiZmPZL8-BCPXoleXiijKR)

First, you need to create your organization as described in the [“Create an Organization”](build-a-network.md#create-an-organization) section.

After that, you need to do the following:

&#x20;1\. Export MSP definition and ordering node JSON profile.

2\. Share your organization MSP and orderer JSON profiles to Organization 1.

{% hint style="info" %}
_Note:_ You can create as many ordering nodes as you want but share only one of the ordering nodes' profiles with Organization 1 during this step.&#x20;
{% endhint %}

4\. Wait until Organization 1 [adds you as a partner](../network-and-node-mngmnt/msps-and-partners.md#how-to-add-organization-as-a-partner) and shares its MSP definition with you with a link to one of its ordering nodes or with a last system channel’s genesis block protobuf file. &#x20;

5\. Add Organization 1 as a partner (add its MSP to partners MSPs).

6\. Add your ordering node(s) to the system channel by [fetching the genesis block or by uploading the genesis block.](../network-and-node-mngmnt/ordering-service.md#how-to-add-a-genesis-block)

{% hint style="info" %}
_Note:_ You can add all of your ordering nodes at once or just one-by-one. Note that only consenters, which are a part of a system channel, can be added to the application channels.
{% endhint %}

7\. Wait a few minutes until the ordering node(s) sync with the network.

Your organization is a part of the blockchain network now and you can add as many ordering nodes as you want.

**Meanwhile, Organization 1 (any network member) should perform the following steps:**

1. Add your organization's MSP definition to the partners list. See details [here.](../network-and-node-mngmnt/msps-and-partners.md#how-to-add-organization-as-a-partner)
2. Add your organization as an orderer organization. See details [here.](../network-and-node-mngmnt/ordering-service.md#how-to-add-an-organization-as-an-orderer-organization)
3. Copy an external link from one of the ordering nodes connected to the system channel and share it with your organization. Or download the system channel’s last block and share it with you.
4. Your organization can also be [added to the members of a consortium](../network-and-node-mngmnt/ordering-service.md#how-to-add-an-organization-to-the-consortium) if needed.

{% hint style="info" %}
_Tip:_ A consortium defines a set of organizations that can create application channels and join them during creation.
{% endhint %}

Upon completion of the steps by both organizations, your consenters and peers can be added to application channels (or create application channels if your organization was added to a consortium).

### Joining a system channel with an external orderer

You can join a system channel with no ordering nodes deployed by your organization. In this case, you need to ask any orderer organization to send you the link to any consenter from a system channel and add it as an external orderer.&#x20;

![Join a system channel with an external orderer](<../.gitbook/assets/image (81).png>)

1. Create your organization as described in the “Create an Organization” section. \
   Note that you don’t need to create ordering sets with orderers in this case.
2. Export the MSP definition.
3. Share your organization MSP to Organization 1.&#x20;
4. Wait until Organization 1 adds you as a partner and shares its MSP definition with you with a link to one of the consenters.
5. Add Organization 1 as a partner (add its MSP to partners MSPs).
6. Create an external orderer with the provided link.

Your organization is a part of the blockchain network now.

Meanwhile, Organization 1 (any network member) should perform the following steps:&#x20;

1. Add your organization's MSP definition to the partners list. See details [here.](../network-and-node-mngmnt/msps-and-partners.md#how-to-add-organization-as-a-partner)
2. Add your organization as an orderer organization. See details [here.](../network-and-node-mngmnt/ordering-service.md#how-to-add-an-organization-as-an-orderer-organization)
3. Copy an external link from one of the consenters of the system channel and share it with your organization.&#x20;
4. Your organization can also be [added to the members of a consortium](../network-and-node-mngmnt/ordering-service.md#how-to-add-an-organization-to-the-consortium) if needed.

Upon completion of the steps by both organizations, your peers can be added to application channels (or create application channels if your organization was added to a consortium).

