# Enroll an Identity for Your Application

Each member of a blockchain network must have an identity enrolled by a trusted CA to communicate with a network. You can read more about identities in the official Hyperledger Fabric documentation [here.](https://hyperledger-fabric.readthedocs.io/en/latest/identity/identity.html)

Catalyst Blockchain Manager allows you to create identities easily using the UI. You can register and enroll identities, for example, for your peer and ordering nodes and use it within your network. These identities are stored in a secure environment (Kubernetes secrets or Hashicorp Vault) and private keys cannot be exported due to security reasons.

Meanwhile, each business application should also have an identity to communicate within a network and process transactions. For this case, Users should enroll an identity for their business applications as well.

## 1. Enroll using fabric-ca-client

**Prerequisites:**

* A CA was deployed
* A CA admin was enrolled

### 1.1. Register an identity using the Catalyst Blockchain Manager UI

* Go to the identities tab on the CA details page
* Click _Add Identity_
* Provide name & password (remember these)
* Choose type _Client_
* **Do not** select _Enroll Identity_
* Click _Register_

### 1.2. Enroll the registered identity using the fabric-ca-client

Follow the instructions from the official Hyperledger Fabric [documentation](https://hyperledger-fabric.readthedocs.io/en/latest/install.html) to get the binary file.

* Get the Root TLS CA Certificate from the Catalyst Blockchain Manager
* Get the URL for the CA
* Enroll the identity using the following command:

`./fabric-ca-client enroll -u https://<ENROLL_ID>:<ENROLL_SECRET>@<CA_HOST>:<PORT> --tls.certfiles <PATH_TO_TLS_CERT> --mspdir <MSP_FOLDER>`

Where:

* <mark style="color:blue;">`<ENROLL_ID>`</mark> & <mark style="color:blue;">`<ENROLL_SECRET>`</mark>: Name and password provided when registering
* <mark style="color:blue;">`<CA_HOST>`</mark> & <mark style="color:blue;">`<PORT>`</mark>: host & port from CA url
* <mark style="color:blue;">`<PATH_TO_TLS_CERT>`</mark>: The relative path to the TLS CA root signed certificate of the TLS CA associated with this organization
* <mark style="color:blue;">`<MSP_FOLDER>`</mark>: Specify a directory where the certs and private keys will be stored after enrollment

## 2. Enroll using the fabric SDK

You can register & enroll users using one of the official fabric SDKs.

* [Hyperledger Fabric Node SDK documentation](https://hyperledger.github.io/fabric-sdk-node/)
* [Hyperledger Fabric Java SDK documentation](https://hyperledger.github.io/fabric-gateway-java/)
* [Hyperledger Fabric Go SDK documentation](https://pkg.go.dev/github.com/hyperledger/fabric-sdk-go)

### 2.1. Node SDK

Register a new identity using the Catalyst Blockchain Manager (see 1.1) or by leveraging the SDK

_Remember the username and password, as they need to be provided as enrollmentID and enrollment secret during enrollment._

#### **2.1.1. Enroll with a CSR**

* First, create the CSR. A common way is using the openssl command.

{% hint style="warning" %}
The CSR must contain the information "common name" and the "common name" must be same as the "enrollmentID" at the register step.
{% endhint %}

An example of how this could look:

`openssl req -nodes -newkey rsa:2048 -keyout test.key -out test.csr`

* Next, call enroll with the CSR:

```javascript
const csr = fs.readFileSync('path to csr', 'utf8')
const req = {
	enrollmentID: enrollmentID,
	enrollmentSecret: enrollmentSecret,
	csr: csr
}

const caClient = new FabricCAServices("url of the ca")

const enrollment = await caClient.enroll(req)
```

* The returned Enrollment object contains the **certificate**.

**2.1.2 Enroll without CSR**

* Call enroll without a CSR:

```javascript
const req = {
	enrollmentID: enrollmentID,
	enrollmentSecret: enrollmentSecret,
}

const caClient = new FabricCAServices("url of the ca")

const enrollment = await caClient.enroll(req)
```

* The returned Enrollment object contains the **certificate** and **private key.**

### 2.2. Java SDK

Register a new identity using the Catalyst Blockchain Manager ([see 1.1](enroll-an-identity-for-your-application.md#1.1-register-an-identity-using-the-catalyst-blockchain-platform-ui)) or by leveraging the SDK.

_Remember the username and password, as they need to be provided as enrollmentID and enrolment secret during enrolment._

#### **2.2.1. Enroll with a CSR**

* First, create the CSR. A common way is using the openssl command.

{% hint style="warning" %}
The CSR must contain the information "common name" and the "common name" must be same as the "enrollmentID" at the register step.
{% endhint %}

An example of how this could look:

`openssl req -nodes -newkey rsa:2048 -keyout test.key -out test.csr`

* Next, call enroll with the CSR:

```java
HFAClient caClient = HFAClient.createNewInstance("url of the ca", null);
CryptoSuite cryptoSuite = CryptoSuiteFactory.getDefault().getCryptoSuite();
caClient.setCryptoSuite(cryptoSuite);

String csr = Files.readString(Path.of("path to csr"), StandardCharsets.UTF_8);

EnrollmentRequest enrollmentReq = new EnrollmentRequest();
enrollmentReq.setCsr(csr);

Enrollment enrollment = caClient.enroll(enrollmentID, enrollmentSecret, enrollmentReq)
```

* The returned Enrollment object contains the **certificate.**

#### **2.1.2. Enroll without a CSR**

* Call enroll without a CSR:

```java
HFAClient caClient = HFAClient.createNewInstance("url of the ca", null);
CryptoSuite cryptoSuite = CryptoSuiteFactory.getDefault().getCryptoSuite();
caClient.setCryptoSuite(cryptoSuite);

Enrollment enrollment = caClient.enroll(enrollmentID, enrollmentSecret)
```

* The returned Enrollment object contains the **certificate** and **private key.**
