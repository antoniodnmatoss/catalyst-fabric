# Create Your Application

## **Writing Simple “Hello World” Chaincode**

Catalyst Blockchain Manager runs chaincodes as external services, meaning that this must be considered during deployment.

A detailed guide on how to write external chaincode can be found in the official [Hyperledger Fabric documentation.](https://hyperledger-fabric.readthedocs.io/en/release-2.2/cc\_service.html#writing-chaincode-to-run-as-an-external-service)

Here, we will be using an already created simple “Hello World” chaincode example written in Go to show specific details of how to run smart contracts in Catalyst Blockchain Manager.

The source code of this chaincode can be found following the [link.](https://github.com/catalyst-bp/fabric-externalcc)

The key part of this example chaincode is the function where its server starts:

```go
func main() {
  ccid := os.Getenv("CHAINCODE_ID")
  address := os.Getenv("CHAINCODE_ADDRESS")
 
  server := &shim.ChaincodeServer{
     CCID: ccid,
     Address: address,
     CC: &HelloWorld{},
     TLSProps: shim.TLSProperties{
     Disabled: true,
     },
  }
 
  if err := server.Start(); err != nil {
  fmt.Printf("Error starting HelloWorld chaincode: %s", err)
  }
}
```

To run chaincode in Catalyst Blockchain Manager, you need to set _`CCID`_ and _`Address`_ fields of _`shim.ChaincodeServer`_ as environment variables _`CHAINCODE_ID`_ and _`CHAINCODE_ADDRESS,`_ respectively.\
\
These two environment variables will be passed during the creation of the chaincode external service, _`CHAINCODE_ID`_matches chaincode package id after its installation on a peer and _`CHAINCODE_ADDRESS`_ is the listen address of a chaincode server that was used to build the chaincode server endpoint accessible from a peer in _connection.json._

## **Communication with** Catalyst Blockchain Manager&#x20;

There are two ways to communicate with Catalyst Blockchain Manager: Hyperledger Fabric SDK and Catalyst Blockchain Manager API.

### Hyperledger Fabric SDK

There are many different Hyperledger Fabric resources and guides on configuring the SDK for interaction with Hyperledger Fabric, and these can be utilized as well.

To complete SDK integration with Catalyst Blockchain Manager:

&#x20;1\. Register a client signing identity and TLS identity for the business application on behalf of your Catalyst Blockchain Manager user.

2\. Enroll the registered identities through SDK CA client from your business application or Hyperledger Fabric CA Client in CLI.

{% hint style="info" %}
The detailed guide on how to enroll an identity on the client side you can find [here.](enroll-an-identity-for-your-application.md)&#x20;
{% endhint %}

SDK method allows you to fully integrate with Catalyst Blockchain Manager and interact with all network components. However, this method is complicated. Instead of using Hyperledger Fabric SDK, you can use [Catalyst Blockchain Manager](../cc-mngmnt/catbp-api.md)[ API.](../cc-mngmnt/catbp-api.md)

### **Catalyst Blockchain** Manager **API**

For communication between your business application and a chaincode, Catalyst Blockchain Manager provides you with an API to invoke, query deployed chaincodes, and subscribe for chaincode's events.

Endpoints and required parameters are listed [here.](../cc-mngmnt/catbp-api.md)
