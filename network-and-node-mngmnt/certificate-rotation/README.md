# Certificate Rotation

Certificates are issued by Certificate Authorities to every participant. Each Certificate represents a member's identity and every component in the network (whether it is a Peer, Orderer, etc.) must have an identity in order to communicate, authenticate and transact in the network. These certificates can be one of three types:

* Signing Identity
* TLS Identity
* CA Identity

[More information about Fabric certificates.](https://hyperledger-fabric.readthedocs.io/en/latest/certs\_management.html#certificate-types)

### **Why to rotate Certificates?**

Certificate rotation refers to the practice of replacing the existing certificate with new ones. This is done when:

* The certificate is about to expire.
* A security breach has occurred, therefore a Certificate can no longer be trusted.
* New constraints have to be applied.
* A new Certificate Authority takes over.

### **Certificate Renewal**

It is recommended that you renew the certificates before they expire in order to avoid disruption of service, and each certificate must be issued targeting an expiration date by the corresponding Certificate Authority.

### **Enrollment and Reenrollment**&#x20;

Enrolling a certificate is the process by which members request CAs (Certificate Authorities) to provide them with a new certificate. To do this, a client creates a Certificate Signing Request, that is then signed by the Certificate Authority.

However, by Reenrolling participants are allowed to reuse an existing private key without the need of creating a new one. (This is especially important for orderer TLS certificates).

[More information about Fabric Enrollment and Reenrollment](https://hyperledger-fabric-ca.readthedocs.io/en/latest/deployguide/use\_CA.html)

### **Certificate Revocation List**&#x20;

When a participant needs to verify another participant’s identity, they must be aware of whether that certificate is still valid and has not been revoked. To do so the CRL maintains a list of all the certificates that have been previously revoked and are no longer trusted by the network, so each time a participant is checked, it is first verified against the issuing Certificate Authority’s CRL.&#x20;

**Note:** The CRL is also present in the channels where the Participant takes place in, therefore it must be in sync in order to maintain an operational network.

[More Information about Fabric CRL](https://hyperledger-fabric.readthedocs.io/en/latest/identity/identity.html?highlight=crl#certificate-revocation-lists)

The processes beneath certificate rotation are explained and some examples are illustrated in the following sections:

1. [Certificate revocation](1.-certificate-revokation.md)
2. [Peer certificates rotation](2.-peer-certificates-rotation.md)
3. [TLS Identities rotation](3.-tls-identities-rotation.md)
4. [Orderer rotation](4.-orderer-rotation.md)
5. [Client identity rotation](5.-client-identity-rotation.md)
6. [Signing identity rotation](6.-signing-identities-revocation.md)
7. [Signing identity certificate rotation](7.-signing-identity-rotation.md)
8. [MSP admin rotation](8.-msp-admin-rotation.md)



