# MSP and Partners

## What is an MSP?

While every entity on the network can have a digital certificate and a valid identity, they must have specific roles and permissions. Although one user has an identity on one network, that does not mean that they can communicate with users on another network. This characteristic of Hyperledger Fabric is possible due to the Membership Service Provider or MSP.  At a very basic level, the MSP provides information, which is used for authenticating users in a particular network by mapping their identities to their roles in the network.

**What role does an organization play in an MSP?**

An organization in an MSP is the collection of members grouped under the same identity. The MSP establishes the relationship between the member and the organization by linking the member's identity to the organization.

{% hint style="info" %}
_Tip:_ It is a standard naming convention to name the MSP after the organization.
{% endhint %}

You can read more about the MSP in the official Hyperledger Fabric documentation [here.](https://hyperledger-fabric.readthedocs.io/en/release-2.2/membership/membership.html)

## How to Create an Organization's MSP?

You have to go to the tab labeled "Your MSP" to create an MSP for your organization.

Under this tab, click the “Create organization” button in the upper right-hand corner.

![Create organization](<../.gitbook/assets/image (196).png>)

Upon clicking this button, a side window will appear, which asks for certain parameters to be provided in five sections.

![Create an MSP](<../.gitbook/assets/image (33).png>)

1. Provide the MSP name and MSP ID.&#x20;
2. Add root CA certificates and intermediate CA certificates - These CAs will be accepted by the network for registering and enrolling of signing identities. You can either upload a CA certificate from your computer or select among the CAs on Catalyst Blockchain Manager.&#x20;
3. Add root TLS CA certificates and intermediate TLS CA certificates- These CAs would be accepted by the network for registering and enrolling TLS certificates for the identities.&#x20;
4. Set your MSPs admin identity. You can either select an existing identity or generate a new one by providing its name and password. You can add other admin’s certificates by uploading the certificates.&#x20;
5. Create the TLS identity - The Platform needs this identity to function within the network under your MSP. You can either select an existing identity or create a new one. After clicking on “Next” in the fourth step, you can preview the details you have entered. If all details are correct, you can click on Submit, followed by a confirmation popup that your MSP has been created.

{% hint style="warning" %}
_Important:_&#x20;

* You have to add all the certificates of all the chain of trust for each intermediate CA you want to use for registering/enrolling identities.&#x20;
* If you add intermediate CAs, we recommend using only intermediate CAs for registering/enrolling identities, not root CAs.
* You must add at least one root CA certificate to both Root CA and TLS Root CA sections. If you have no intermediate CAs, just skip adding intermediate certificates to your MSP.
{% endhint %}

{% hint style="info" %}
_Note:_ You can add only one MSP for an organization.
{% endhint %}

The MSP will now be visible under the “Your MSPs” tab inside of a table. This table has the following details: Organization, MSP ID, certificates, and Actions. You can click the number of certificates to see the full certificates’ details.&#x20;

<figure><img src="../.gitbook/assets/image (86).png" alt=""><figcaption><p>See certificates</p></figcaption></figure>

Under actions, you can perform the following:

* Export - Download the JSON file of the MSP. This JSON file is needed to add an organization to the other organizations’ partner list as described [in this section.](msps-and-partners.md#how-to-add-organization-as-a-partner)
* Delete the MSP.

{% hint style="info" %}
_Note:_ If the MSP is deleted, you cannot use ordering and peer nodes created within this MSP. \
\
To restore it you can create a new MSP with the same name, ID, and certificates. You can add other certificates if needed.
{% endhint %}

## How to Add Organization as a Partner?

For the sake of example, if a second organization (Organization 2) wants to join your network or another organization's network and become a partner, the first thing it needs to do is create its MSP. The process of creating an MSP is explained in the [previous section](msps-and-partners.md#how-to-create-an-organizations-msp).

After creating the MSP, Organization 2 needs to export the JSON file of its MSP, which can be downloaded through the “Export” button available in the Actions column of the "Your MSPs" table.

![Export MSP JSON file](<../.gitbook/assets/image (102).png>)

They have to share this JSON file with the organization that is already a network member, for example, Organization 1.

Now, Organization 1 can add Organization 2 by clicking on the “Add partner” button under the “MSPs and Partners” tab and providing the JSON file of Organization 2’s MSP.

![Add partner](<../.gitbook/assets/image (113).png>)

{% hint style="info" %}
_Note:_ Partners can be added one at a time.
{% endhint %}

{% hint style="warning" %}
_Note:_ Organization 2 should also add Organization 1 to the partners list to further [join the network.](../getting-started/join-a-network.md)
{% endhint %}
