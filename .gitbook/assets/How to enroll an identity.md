Each member of a blockchain network must have an identity enrolled by a trusted CA to communicate with a network. You can read more about identities in the official Hyperledger Fabric documentation here.

Catalyst Blockchain Platform allows you to create identities easily using the UI. You can register and enroll identities, for example, for your peer and ordering nodes and use it within your network. These identities are stored in a secure environment (Kubernetes secrets or Hashicorp Vault) and private keys cannot be exported due to security reasons.

Meanwhile, each business application should also have an identity to communicate within a network and process transactions. For this case, Users should enroll an identity for their business applications as well.

## 1. Enroll using fabric-ca-client

**Prerequisites:**

- A CA was deployed
- A CA admin was enrolled

### 1.1 Register identity using the Catalyst Blockckhain Platform UI

- Go to the identities tab on the CA details page
- Click *Add Identity*
- Provide name & password (remember these)
- Choose type *Client*
- **Do not** select *Enroll Identity*
- Click *Register*

### 1.2 Enroll the registered identity using the fabric-ca-client

Follow the instructions from the official Hyperledger Fabric [documentation](https://hyperledger-fabric.readthedocs.io/en/latest/install.html) to get the binary file.

- Get the Root TLS CA Certificate from the Catalyst Blockchain platform

<img src="../_resources/79239f6bc94b7833d38f4e4b1aaa0275" width="624" height="307" style="margin-left: 0px; margin-top: 0px;" class="jop-noMdConv">

- Get the url for the CA
- Enroll the identity using the following command:

`./fabric-ca-client enroll -u https://<ENROLL_ID>:<ENROLL_SECRET>@<CA_HOST>:<PORT> --tls.certfiles <PATH_TO_TLS_CERT> --mspdir <MSP_FOLDER>`

Where:

- &lt;ENROLL\_ID&gt; & &lt;ENROLL\_SECRET&gt;: Name and password provided when registering
- &lt;CA_HOST&gt; & &lt;PORT&gt;: host & port from CA url
- &lt;PATH\_TO\_TLS_CERT&gt;: The relative path to the TLS CA root signed certificate of the TLS CA associated with this organization
- &lt;MSP_FOLDER&gt;: Specify a directory where the certs and private keys will be stored after enrollment

## 2\. Enroll using the fabric SDK

You can register & enroll users using one of the official fabric SDKs.

- Hyperledger Fabric Node SDK documentation
- Hyperledger Fabric Java SDK documentation
- Hyperledger Fabric Go SDK documentation

### 2.1 Node SDK

Register a new identity using the Catalyst Blockchain Platform (see 1.1) or by leveraging the sdk

*Remember the username and password, as they need to be provided as enrollmentID and enrollment secret during enrollment.*

#### 2.1.1 Enroll with a CSR

- First, create the CSR. A common way is using the openssl command.

> Notice the CSR must contain the information "common name" and the "common name" must be same as the "enrollmentID" at the register step.

An example of how this could look:

`openssl req -nodes -newkey rsa:2048 -keyout test.key -out test.csr`

- Next, call enroll with the CSR:

<img src="../_resources/Screenshot 2022-08-29 at 12.24.14.png" alt="Screenshot 2022-08-29 at 12.24.14.png" width="563" height="240" class="jop-noMdConv">

- The returned Enrollment object contains the **certificate**.

#### 2.1.2 Enroll without CSR

- Call enroll without a CSR

<img src="../_resources/Screenshot 2022-08-29 at 12.25.18.png" alt="Screenshot 2022-08-29 at 12.25.18.png" width="562" height="192">

- The returned Enrollment object contains the **certificate** and **private key**

### 2.2 Java SDK

Register a new identity using the Catalyst Blockchain Platform (see 1.1) or by leveraging the sdk

*Remember the username and password, as they need to be provided as enrollmentID and enrollment secret during enrollment.*

#### 2.2.1 Enroll with a CSR

- First, create the CSR. A common way is using the openssl command.

>Notice the CSR must contain the information "common name" and the "common name" must be same as the "enrollmentID" at the register step.

An example of how this could look:

`openssl req -nodes -newkey rsa:2048 -keyout test.key -out test.csr`

- Next, call enroll with the CSR:
![Screenshot 2022-08-29 at 12.30.03.png](../_resources/Screenshot 2022-08-29 at 12.30.03.png)
- The returned Enrollment object contains the **certificate**

#### 2.1.2 Enroll without CSR

- Call enroll without a CSR
![Screenshot 2022-08-29 at 13.16.24.png](../_resources/Screenshot 2022-08-29 at 13.16.24.png)
- The returned Enrollment object contains the **certificate** and **private key**

