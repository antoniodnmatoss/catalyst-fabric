# Catalyst Blockchain Manager API

For communication between your business application and a chaincode, Catalyst Blockchain Manager provides you with an API to invoke/query deployed chaincodes and subscribe for chaincode events.

{% hint style="info" %}
_Note:_ Catalyst Blockchain Manager API can be used by the users who have an on-premise installation of Catalyst Blockchain Manager. The users who use Catalyst Blockchain Manager as a BaaS will be able to use the API in future releases.
{% endhint %}

## **Invoke** endpoint

{% swagger method="post" path="/invoke" baseUrl="{domain}/api/v1/channels/{channelID}/cc/{chaincodeID}" summary="" %}
{% swagger-description %}
Content-Type — application/json
{% endswagger-description %}

{% swagger-parameter in="path" name="domain" required="true" type="String" %}
The name of the domain, where the Catalyst Blockchain Platform Hyperledger Fabric service is 

[deployed.](../installation-instructions/#5-create-an-a-record-in-a-zone-in-your-domains-dns-management-panel-and-assign-it-to-the-load-balancer-created-upon-traefik-or-openshift-installation)


{% endswagger-parameter %}

{% swagger-parameter in="path" name="channelID" required="true" type="String" %}
The name of the channel, where the chaincode is committed.
{% endswagger-parameter %}

{% swagger-parameter in="path" name="chaincodeID" required="true" type="String" %}
The chaincode name.
{% endswagger-parameter %}

{% swagger-parameter in="body" required="true" name="function" type="String" %}
The name of a chaincode function 
{% endswagger-parameter %}

{% swagger-parameter in="body" name="args" type="Array" required="true" %}
An array of function arguments
{% endswagger-parameter %}

{% swagger-parameter in="body" name="transient_args" type="Object" required="false" %}
Transient args will reach the chaincode but will not stay in the transaction records.
{% endswagger-parameter %}

{% swagger-parameter in="body" name="is_init" type="Boolean" required="false" %}
Indicates whether init function call is required, and can be set to true only during the first operation with chaincode.
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```javascript
  {
  "proposal_response": {
		"chaincode_status": 0, //200 or 500
		"payload": "", //base64
    "errors": [
      {
        "code": 0,
        "message": "string"
      }
    ]
  },
  "tx": {
    "id": "string",
    "validation_code": 0
  }
}
```
{% endswagger-response %}

{% swagger-response status="400: Bad Request" description="" %}
```javascript
```
{% endswagger-response %}

{% swagger-response status="500: Internal Server Error" description="" %}
```javascript
```
{% endswagger-response %}
{% endswagger %}

### Request&#x20;

Example of the request body for invocation of a chaincode:

```json
{
    "args": ["data"],
    "transient_args": {
        "asset_properties": {
            "assetID": "99",
            "objectType": "art",
            "color": "black",
            "size": 500,
            "appraisedValue": 999
        }
    },
    "function": "CreateAsset",
    "is_init": false,
}
```

### **Response**

```json
{
  "proposal_response": {
		"chaincode_status": 0, //200 or 500
		"payload": "", //base64
    "errors": [
      {
        "code": 0,
        "message": "string"
      }
    ]
  },
  "tx": {
    "id": "string",
    "validation_code": 0
  }
}
```

#### **Proposal response**

* `chaincode_status` - shows the response from a chaincode. Possible values in the current version: `200` and `500`. \
  The`500` status means that execution failed for some reason. Note that it might be a business error like thrown exception “entity not found”. There can be multiple reasons of failure due to proposals being sent to multiple peers. The reasons can be found in `errors`.
* `errors` - a status error returned from a peer.\
  The `message` is equal to the text in exception chaincode throws. It is suggested that instead of plain string in the message you should use a data structure with all the info about what happened and some custom codes.
* `payload` - data returned on successful invocation/query from a chaincode in `base64`.

#### Tx

* `id` - transaction id that is generated for each request, must be unique.
* `tx_validation_code` - validation code of a transaction set by ordering service. It makes sense to check this code only if `chaincode_status` is 200. Even if `chaincode_status`is `200`, it does not mean that the transaction was approved and valid. It’s important that the validation code must be `0` for any valid transaction. \
  All possible codes are listed below.

```
TxValidationCode_VALID 			      TxValidationCode = 0
TxValidationCode_NIL_ENVELOPE                 TxValidationCode = 1
TxValidationCode_BAD_PAYLOAD                  TxValidationCode = 2
TxValidationCode_BAD_COMMON_HEADER            TxValidationCode = 3
TxValidationCode_BAD_CREATOR_SIGNATURE        TxValidationCode = 4
TxValidationCode_INVALID_ENDORSER_TRANSACTION TxValidationCode = 5
TxValidationCode_INVALID_CONFIG_TRANSACTION   TxValidationCode = 6
TxValidationCode_UNSUPPORTED_TX_PAYLOAD       TxValidationCode = 7
TxValidationCode_BAD_PROPOSAL_TXID            TxValidationCode = 8
TxValidationCode_DUPLICATE_TXID               TxValidationCode = 9
TxValidationCode_ENDORSEMENT_POLICY_FAILURE   TxValidationCode = 10
TxValidationCode_MVCC_READ_CONFLICT           TxValidationCode = 11
TxValidationCode_PHANTOM_READ_CONFLICT        TxValidationCode = 12
TxValidationCode_UNKNOWN_TX_TYPE              TxValidationCode = 13
TxValidationCode_TARGET_CHAIN_NOT_FOUND       TxValidationCode = 14
TxValidationCode_MARSHAL_TX_ERROR             TxValidationCode = 15
TxValidationCode_NIL_TXACTION                 TxValidationCode = 16
TxValidationCode_EXPIRED_CHAINCODE            TxValidationCode = 17
TxValidationCode_CHAINCODE_VERSION_CONFLICT   TxValidationCode = 18
TxValidationCode_BAD_HEADER_EXTENSION         TxValidationCode = 19
TxValidationCode_BAD_CHANNEL_HEADER           TxValidationCode = 20
TxValidationCode_BAD_RESPONSE_PAYLOAD         TxValidationCode = 21
TxValidationCode_BAD_RWSET                    TxValidationCode = 22
TxValidationCode_ILLEGAL_WRITESET             TxValidationCode = 23
TxValidationCode_INVALID_WRITESET             TxValidationCode = 24
TxValidationCode_INVALID_CHAINCODE            TxValidationCode = 25
TxValidationCode_NOT_VALIDATED                TxValidationCode = 254
TxValidationCode_INVALID_OTHER_REASON         TxValidationCode = 255
```

## **Query** endpoint

### Request&#x20;

{% swagger method="post" path="query" baseUrl="{domain}/api/v1/channels/{channelID}/cc/{chaincodeID}" summary="" %}
{% swagger-description %}
Content-Type — application/json
{% endswagger-description %}

{% swagger-parameter in="path" name="domain" required="true" type="String" %}
The name of the domain, where the Catalyst Blockchain Platform Hyperledger Fabric service is 

[deployed.](../installation-instructions/#5-create-an-a-record-in-a-zone-in-your-domains-dns-management-panel-and-assign-it-to-the-load-balancer-created-upon-traefik-or-openshift-installation)


{% endswagger-parameter %}

{% swagger-parameter in="path" name="channelID" required="true" type="String" %}
The name of the channel, where the chaincode is committed.
{% endswagger-parameter %}

{% swagger-parameter in="path" name="chaincodeID" required="true" type="String" %}
The chaincode name.
{% endswagger-parameter %}

{% swagger-parameter in="body" required="true" name="function" type="String" %}
The name of a chaincode function 
{% endswagger-parameter %}

{% swagger-parameter in="body" name="args" type="Array" required="true" %}
An array of function arguments
{% endswagger-parameter %}

{% swagger-parameter in="body" name="transient_args" type="Object" required="false" %}
Transient args will reach the chaincode but will not stay in the transaction records.
{% endswagger-parameter %}

{% swagger-parameter in="body" name="is_init" type="Boolean" required="true" %}
Indicates whether init function call is required, and can be set to true only during the first operation with chaincode.
{% endswagger-parameter %}

{% swagger-parameter in="body" name="use_local_peers" type="Boolean" %}
Set to 

`true`

 if you want to use only those peers that belong to your organization to process the request.
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```javascript
  {
  "proposal_response": {
		"chaincode_status": 0, //200 or 500
		"payload": "", //base64
    "errors": [
      {
        "code": 0,
        "message": "string"
      }
    ]
  },
  "tx": {
    "id": "string",
    "validation_code": 0
  }
}
```
{% endswagger-response %}

{% swagger-response status="400: Bad Request" description="" %}
```javascript
```
{% endswagger-response %}

{% swagger-response status="500: Internal Server Error" description="" %}
```javascript
```
{% endswagger-response %}
{% endswagger %}

Example of the request body for the query of a chaincode:

```json
{
    "args": ["data"],
    "transient_args": {
        "asset_properties": {
            "assetID": "99",
            "objectType": "art",
            "color": "black",
            "size": 500,
            "appraisedValue": 999
        }
    },
    "function": "CreateAsset",
    "is_init": false,
    "use_local_peers": false
}
```

### **Response**

[Same ](catbp-api.md#response)as for the `invoke`.

## **Subscribe** endpoint

Server-Sent Events (SSE) is a server push technology enabling a client to receive automatic updates from a server via an HTTP connection. This specific endpoint allows you to subscribe to an event stream for a specific chaincode.&#x20;

### Request&#x20;

{% swagger method="get" path="/subscribe?startFrom={startFromValue}&eventFilter={eventFilterValue} " baseUrl="{domain}/api/v1/channels/{channelID}/cc/{chaincodeID}" summary="" %}
{% swagger-description %}
Content-Type — text/event-stream
{% endswagger-description %}

{% swagger-parameter in="path" name="domain" required="true" type="String" %}
The name of the domain, where the Catalyst Blockchain Manager Hyperledger Fabric service is 

[deployed.](../installation-instructions/#5-create-an-a-record-in-a-zone-in-your-domains-dns-management-panel-and-assign-it-to-the-load-balancer-created-upon-traefik-or-openshift-installation)


{% endswagger-parameter %}

{% swagger-parameter in="path" name="channelID" required="true" type="String" %}
The name of the channel, where the chaincode is committed.
{% endswagger-parameter %}

{% swagger-parameter in="path" name="chaincodeID" required="true" type="String" %}
The chaincode name.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="{startFromValue}" type="Integer" %}
Block number (the last block is set by default).
{% endswagger-parameter %}

{% swagger-parameter in="query" name="{eventFilterValue}" type="String" %}
A regexp filter to filter out only needed events
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```javascript
"tx_id": "string",
"chaincode_id": "string",
"event_name": "string",
"payload": "", //base64
"block_number": 0
```
{% endswagger-response %}
{% endswagger %}

### Response

```json
"tx_id": "string",
"chaincode_id": "string",
"event_name": "string",
"payload": "", //base64
"block_number": 0
```

* `tx_id` — the id of the transaction in which the event was set.&#x20;
* `chaincode_id` — the id of the chaincode that set the event.&#x20;
* `event_name` — the name of the chaincode event.&#x20;
* `payload` — contains the payload of the chaincode event is base64.&#x20;
* `block_number` — contains the block number in which the chaincode event was committed.&#x20;

## Examples

{% hint style="info" %}
Catalyst Blockchain Manager supports two **authorization** types: Basic and OpenID. \
The user chooses the authorization type during the Catalyst Blockchain Manager Hyperledger Fabric service [installation.](../installation-instructions/#configure-helm-chart-values)
{% endhint %}

**Invoke example**

**The name of the domain,** where Catalyst Blockchain Manager service is deployed: [https://chaincodeinvokeexample.com](https://chaincodeinvokeexample.com) \
**The channel name:** catbp \
**The chaincode name:** helloworld \
**Auth type:** basic auth (login: login, password: password)

```
curl -X POST https://chaincodeinvokeexample.com/api/v1/channels/catbp/cc/helloworld/invoke
 -H 'Content-Type: application/json'
 -H 'Authorization: Basic bG9naW46cGFzc3dvcmQK'
 -d '{"function":"save","args":["John"],"is_init":false}'
```

* where`bG9naW46cGFzc3dvcmQK`— base64 authorization token.

{% hint style="info" %}
You can generate the token using the following command `echo {your_login}:{your_password} | base64`
{% endhint %}

**Query example**

**The name of the domain,** where Catalyst Blockchain Manager is deployed: https://chaincodequeryexample.com \
**The channel name:** catbp \
**The chaincode name:** helloworld \
**Auth type:** OpenID

```
curl -X POST https://chaincodequeryexample.com/api/v1/channels/catbp/cc/helloworld/query
 -H 'Content-Type: application/json'
 -H 'Authorization: Bearer b3BlbmlkOm9wZW5pZAo='
 -d '{"function":"get","args":["John"],"is_init":false}'
```

* where `b3BlbmlkOm9wZW5pZAo=` is your OpenID token.

**Subscribe example**

**The name of the domain,** where Catalyst Blockchain Manager is deployed: https://chaincodesubscriptionexample.com\
**The channel name:** catbp \
**The chaincode name:** helloworld \
**Auth type:** OpenID

```
curl -X GET https://chaincodesubscriptionexample.com/api/v1/channels/catbp/cc/helloworld/subscribe
 -H 'Content-Type: text/event-stream'
 -H 'Authorization: Bearer b3BlbmlkOm9wZW5pZAo='
```

* where `b3BlbmlkOm9wZW5pZAo=` is your OpenID token.
