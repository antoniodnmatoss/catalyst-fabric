# Security

## Authentication and Authorization

For user login, Catalyst Blockchain Platform provides the following methods for the user's authentication and authorization.

* **Basic authentication -** It requires the user credentials that are specified during the installation of the Platform.
* **OpenID -** The user login can be integrated with OpenID protocol, which allows a third-party service to authenticate a user. This protocol removes the central dependency of storing credentials in a single place and enhances platform's security.

{% hint style="info" %}
_Note:_ It is the users’ choice to select either openID or basic authentication. See details[ here.](installation-instructions/#configure-helm-chart-values)
{% endhint %}

## Certificate Management

In Hyperledger Fabric, two entities, the MSP and the Certificate Authority (CA), are responsible for identity management. The CA issues certificates to other entities on the network and these certificates define the identity of an entity on the blockchain network. The CA in Hyperledger Fabric issues X.509 certificates, which is the most popular standard for SSL/TLS connections securing the network from malicious impersonators. For more information about CA, please refer to [this section.](network-and-node-mngmnt/certificate-authority.md)

The MSP, or Membership Service Provider, is responsible for authenticating the CA or the certificates issued by the CA for a particular network, defining the role and responsibilities of entities based on their certificates, which allows for unmatched data privacy. For more information, please refer to the [MSP section.](network-and-node-mngmnt/msps-and-partners.md)

In addition to this, Hyperledger Fabric uses TLS to establish secure communication between two entities based on the public key infrastructure.

## Key Management

Public and private keys are one of the major components of a Public Key Infrastructure (PKI). Effective management of these keys makes the blockchain network highly secure.

In the Hyperledger Fabric network, a certificate issued by a CA defines the identity of the entity. The public key identifies the entity on the network, while the private key authenticates transactions. The generated public and private keys are of ECDSA with Curve P256 standards.

Catalyst Blockchain platform generates private keys and keeps them in storage (Kubernetes Secret). These can not be imported or exported.
