# Create a Channel and Join the Channel

After successfully bootstrapping the network, you can create a channel. Channels are a private layer of communication between specific organizations and are invisible to other network members. Each channel consists of a separate ledger that can only be read and written to by channel members, who can join their peers to the channel and receive new blocks of transactions from the ordering service. While peers, nodes, and Certificate Authorities (CA) form the network's physical infrastructure, channels are the process by which organizations connect and interact.

![Joining a channel flow](<../.gitbook/assets/image (125).png>)

**Create a channel**

Only consortium members can create a channel and be added during channel creation. Non-consortium organizations can be added after the channel creation. You can find details about how to create a channel [here.](../network-and-node-mngmnt/channel-management.md#how-to-create-a-channel)

**Add an organization to the channel**

Adding an organization to the channel is subject to the channel’s policy. If the channel policy is ALL or MAJORITY and there is more than one admin organization on the channel, a proposal will be created. The organization will join the channel only after all of the approvals according to the channel policy are performed. If the channel policy is ANY or there is only one organization with admin rights, a proposal is not created and a new organization can join the channel.

{% hint style="info" %}
_Info:_ Starting from Catalyst Blockchain Manager v. 2.3.0 a non-admin organization can also initiate a proposal.&#x20;
{% endhint %}

You can add an organization to a channel either when creating a channel or to an existing channel.

{% hint style="info" %}
_Info:_ Starting from Catalyst Blockchain Manager v. 2.3.0 you can add an organization even if it is not a part of a system channel. In this case, it won't be added to the Orderers organization list and can join a channel only with an external orderer.
{% endhint %}

**Join a channel**

After your organization was added to the channel as described in the above steps, you can join your peer to the channel. Following this, you can add as many peers and consenters as you need to the channel. It is recommended that you update the anchor peer for the service discovery purpose as well.&#x20;

See the [“Join a channel”](../network-and-node-mngmnt/channel-management.md#how-to-join-a-channel) section to learn more.

