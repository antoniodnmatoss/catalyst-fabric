# AWS

## Prerequisites

### &#x20;1. Setup Kubernetes cluster

{% hint style="success" %}
Supported version of **Kubernetes:** 1.17 and later.



You can use an existing cluster or create a new one using [managed service EKS](https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html) or provision one manually with kops on EC2 machines.

* Make your default storage class underlying provider resizable, so all PVCs for Hyperledger Fabric nodes that will be created by Catalyst Blockchain Platform can be resized when needed.&#x20;
* Add zone labels to Kubernetes nodes when a cluster is stretched across multiple availability zones to be able to schedule a Hyperledger Fabric node in a specific zone.
{% endhint %}

Define your **cluster size** considering the following minimum requirements and your business needs:

&#x20;1\. Minimal requirements for the Catalyst Blockchain Platform Hyperledger Fabric service for one organization — 1 instance with:

* 2 core CPU
* 4GB RAM
* 10GB disk space

2\. Each node (CA, orderer, or peer) that will be deployed consumes additional resources. Minimal requirements for one node:

| **Node** | **CPU** | **Memory, Mi** | **Storage, Gi** |
| -------- | ------- | -------------- | --------------- |
| CA       | 0.1     | 128            | 1               |
| Peer     | 0.1     | 128            | 1               |
| Orderer  | 0.1     | 128            | <p>1<br></p>    |

{% hint style="info" %}
_Note:_ Deciding on the size of the cluster, please consider the expected load of the nodes and increase these values accordingly.
{% endhint %}

3\. Each chaincode installed to a peer runs as a separate pod and consumes additional resources (CPU and RAM).

### **2. Install Helm to your workstation**

Installation manuals: [https://helm.sh/docs/intro/install/](https://helm.sh/docs/intro/install/)\
No customization is needed.&#x20;

{% hint style="success" %}
Supported version of Helm: 3.\*.
{% endhint %}

### 3. Configure ingress and DNS

#### Ingress controller

{% hint style="success" %}
We recommend using the **Traefik** ingress controller.&#x20;

Supported version of Traefik: 2.3.

Installation manuals: [https://github.com/traefik/traefik-helm-chart](https://github.com/traefik/traefik-helm-chart)
{% endhint %}

The ingress-controller is needed for traffic routing to expose Hyperledger Fabric nodes (peer, orderer, CA) as well as API and UI of the Catalyst Blockchain Platform Hyperledger Fabric service. All components are exposed through the port :443.

* Ingress resources for Hyperledger Fabric nodes will be provisioned automatically by the Catalyst Blockchain Platform Hyperledger Fabric service operator upon creation/deletion of a node. These nodes require TLS passthrough enabled because of mutual TLS.
* Ingress resources for API and UI will be created as a part of the helm package during the installation process. It can be configured in the [helm values.](aws.md#configure-helm-chart-values)

While Hyperledger Fabric nodes have self-signed TLS certificates managed by the Catalyst Blockchain Platform Hyperledger Fabric service operator, API and UI require a trusted TLS certificate.

You can choose any of the following options:&#x20;

1. **Single load balancer** of a network load balancer (NLB) type which does TLS passthrough. \
   TLS certificate is provisioned by [cert-manager](https://cert-manager.io/docs/installation/helm/), Traefik is responsible for TLS termination for API and UI.
2. **Two load balancers.** \
   The first one is a network load balancer (NLB) with AWS certificate manager (ACM) configured that is responsible for TLS termination and the second one is NLB with TLS passthrough. In this scenario, additional DNS configuration is needed to route API and UI Traefik through the first LB and Hyperledger Fabric nodes traefik through the second one.&#x20;

{% tabs %}
{% tab title="Single load balancer: NLB with TLS passthrough" %}
No additional customization is needed.
{% endtab %}

{% tab title="Two load balancers: NLB1 and NLB2 + ACM " %}
Traefik helm chart installs a single load balancer of NLB type by default, the second one (NLB + ACM) needs to be provisioned manually as a k8s resource:

```
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-connection-idle-timeout: "120"
    service.beta.kubernetes.io/aws-load-balancer-name: <provide a name>
    service.beta.kubernetes.io/aws-load-balancer-ssl-cert: <provide a resource name, e.g. arn:aws:acm:eu-west-1:1111111111:certificate/acdcd668-f2c5-4652-90ed-1a28762a7039>
    service.beta.kubernetes.io/aws-load-balancer-ssl-negotiation-policy: ELBSecurityPolicy-TLS-1-2-2017-01
    service.beta.kubernetes.io/aws-load-balancer-ssl-ports: "443"
    service.beta.kubernetes.io/aws-load-balancer-type: nlb
  finalizers:
  - service.kubernetes.io/load-balancer-cleanup
  labels:
    app.kubernetes.io/instance: traefik
  name: traefik-with-acm
  namespace: <change to the namespace with traefik-ingress installed>
spec:
  externalTrafficPolicy: Cluster
  ports:
  - name: web
    nodePort: 32481
    port: 80
    protocol: TCP
    targetPort: web
  - name: websecure
    nodePort: 31118
    port: 443
    protocol: TCP
    targetPort: websecure
  selector:
    app.kubernetes.io/instance: traefik
    app.kubernetes.io/name: traefik
  sessionAffinity: None
  type: LoadBalancer
```
{% endtab %}
{% endtabs %}

#### **Create a DNS record**

DNS configuration differs depending on your choice:

|                           |                                                                             |                                                                                                                         |
| ------------------------- | --------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| **Option**                | **DNS records to be put**                                                   | **Comments**                                                                                                            |
| #1. Single load balancer  | A \*.example.com  -> NLB address                                            | API, UI and nodes will go through this single record.                                                                   |
| #2. Two load balancers    | <p>A example.com  -> NLB1 address</p><p>A *.example.com -> NLB2 address</p> | <p>NLB1 handles API and UI and does TLS termination using  ACM.<br>NLB2 handles nodes traffic with TLS passthrough.</p> |

With the following configuration each component is exposed on:

|                    |                             |
| ------------------ | --------------------------- |
| UI                 | example.com:443             |
| API                | example.com:443/api         |
| Any HL Fabric node | \<nodeName>.example.com:443 |

### **4. Create a namespace for the Catalyst Blockchain Platform Hyperledger Fabric service application**

```
kubectl create ns ${ns_name}
```

where _`${ns_name}`_ — name of namespace (can be any).\
\
4.1. Get the credentials to the Helm repository in the JFrog artifactory provided by the IntellectEU admin team.

4.2. Add the repo to Helm with the username and password provided:

```
helm repo add catbp <https://intellecteu.jfrog.io/artifactory/catbp-helm> --username ${ARTIFACTORY_USERNAME} --password ${ARTIFACTORY_PASSWORD}
```

As a result: _`"catbp" has been added to your repositories`_

### **5. Create an ImagePullSecret to access the Catalyst Blockchain Platform Hyperledger service deployable images**

For example, create this Secret, naming it `intellecteu-jfrog-access`:

```
kubectl create secret intellecteu-jfrog-access regcred --docker-server=intellecteu-catbp-docker.jfrog.io --docker-username=${your-name} --docker-password=${your-password} --docker-email=${your-email} -n ${ns_name}
```

where:

* `${your-name}` — Docker username provided by IntellectEU.
* `${your-password}` — Docker password provided by IntellectEU.
* `${your-email}` — your email.
* `${ns_name`} — the namespace created for the Catalyst Blockchain Platform Hyperledger Fabric service on the previous step.

### **6. Deploy a message broker**

Message broker is needed by the Catalyst Blockchain Platform Hyperledger Fabric service to schedule commands, emit events and control workflows.

{% hint style="success" %}
Currently, only **RabbitMQ** is supported.&#x20;

**Version:** 3.7 and later.

We recommend using **Amazon MQ** managed service**.**

* No specific configurations are needed.&#x20;
* 1GB RAM is recommended as a minimum setup.
* Usually, load on the message broker is low so it does not require much resources.\
  t3.micro can be selected as a machine.
{% endhint %}

The Catalyst Blockchain Platform Hyperledger Fabric service requires a vhost and a user with full access for the vhost. Single queue will be propagated upon the Catalyst Blockchain Platform Hyperledger Fabric service startup.

{% hint style="warning" %}
Default configuration comes with TLS enabled, even for private VPC, make sure you enable `amqp.tls` option in[ helm values.](aws.md#configure-helm-chart-values)
{% endhint %}

{% hint style="info" %}
Backups are not required.
{% endhint %}

### &#x20;**7. Deploy a database**

{% hint style="success" %}
We recommend using **Amazon RDS** managed service.

* 1GB RAM is recommended as a minimum setup.
* No specific configurations are needed.&#x20;
* db.t3.small can be selected as a machine.
{% endhint %}

A database is required by the Catalyst Blockchain Platform Hyperledger Fabric service to support internal architecture for workflows as well as store users action logs.

{% hint style="info" %}
_Info:_ No secure data is stored in the database.
{% endhint %}

The Catalyst Blockchain Platform Hyperledger Fabric service requires a database and a user with full read/write access to the database. Database tables will be provisioned in default schema on application startup.

In this example we will use PostgreSQL. Schema is ‘public’ by default. \
Run these commands to provision a database on the recently deployed server:

```
CREATE DATABASE "catbp-org1";
CREATE USER "catbp" WITH ENCRYPTED PASSWORD 'catbp';
GRANT ALL PRIVILEGES ON DATABASE "catbp-org1" to "catbp";
```

{% hint style="danger" %}
_Important:_ Make sure to set up automatic backups so that all action logs of users won’t be lost in case of failure.
{% endhint %}

Catalyst Blockchain Platform supports [AWS IAM authentication](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.IAMDBAuth.html) into the database. It’s useful when default basic auth isn’t suitable. To enable AWS IAM authentication the following values must be provided in the helm chart.

<pre class="language-yaml"><code class="lang-yaml"><strong># -- enables the use of aws sdk
</strong>awsIAM:
  enabled: true

database:
  # -- database type. `postgres` or `mysql` can be specified here
  type: postgres
  # -- example values for postgres database. change them for your env
  host: "postgresql.postgresql"
  tls: true
  port: "5432"
  username: "test1"
  dbname: "test1"
  # -- ignore password and use AWS IAM authentication into the database
  authAws: true
  
</code></pre>

It’s expected that the Catalyst Blockchain Platform API pod has these environment variables set by AWS service account.

```bash
AWS_DEFAULT_REGION
AWS_REGION
AWS_ROLE_ARN
AWS_WEB_IDENTITY_TOKEN_FILE
```

## Setup

### Configure helm chart values

```yaml
# -- address where application will be hosted. All created nodes (peers, orderers, cas) will have <NodeName>.proxy.<domainName> address
domainName: example.com
# -- auth config
auth:
  method: basic
  basic:
   username: <change to your console_login>
   password: <change to your console_pwd>
# -- external RabbitMQ Message broker parameters
amqp:
  host: <change to your amqp host>
  vhost: catbp-org1
  username: catbp
  password: catbp
  port: 5671
  tls: true #Amazon MQ has TLS enabled by default
  # -- external database parameters
database:
  dbname: catbp-org1
  host: <change to your db host>
  username: catbp
  password: catbp
  port: 5432
  tls: false #Amazon RDS has TLS disabled by default
  type: postgres
  # -- ignore password and use AWS IAM authentication into the database
  authAws: false
# -- enables the use of aws sdk 
awsIAM:
  enabled: false
# -- ingressConfig addd Ingress resources for API and UI. By default, traefik is used.
ingressConfig:
  enabled: true 
  tls:
    enabled: false # TLS termination is done by NLB2+ACM
api:
  image:
    repository: intellecteu-catbp-docker.jfrog.io/catbp/fabric-platform/fabric-console
    tag: "2.5"
  imagePullSecrets:
  - name: intellecteu-jfrog-access
operator:
  image:
   repository: intellecteu-catbp-docker.jfrog.io/catbp/fabric-platform/fabric-console
   tag: "2.5"
  imagePullSecrets:
  - name: intellecteu-jfrog-access
ui:
  image:
    repository: intellecteu-catbp-docker.jfrog.io/catbp/fabric-platform/fabric-console-ui
    tag: "2.5"
  imagePullSecrets:
  - name: intellecteu-jfrog-access
```

{% hint style="info" %}
In case of using [a single load balancer ](aws.md#3.-configure-ingress-and-dns)change ingressConfig to:
{% endhint %}

```yaml
ingressConfig:
  # -- specify whether to create IngresRoute resource
  enabled: true
  tls:
    enabled: true
    certManager:
      enabled: true
      email: "<change to your EMAIL>"
      server: "https://acme-v02.api.letsencrypt.org/directory"
```

You can configure other helm chart values if needed. \
You can see the full list of values [here.](https://docs.catalyst.intellecteu.com/v/hyperledger-fabric-service-2.2.0/operations-guide/installation-instructions#configure-helm-chart-values)&#x20;

### **Install the Catalyst Blockchain Platform Hypeledger Fabric service**&#x20;

Use the following command:

```
helm upgrade --install ${fabric_release_name} catbp/fabric-console --values values.yaml -n ${ns_name} --version 2.5
```

where:

* _`${fabric_release_name}`_ — name of the Catalyst Blockchain Platform Hypeledger Fabric service release. You can choose any name/alias. It is used to address for updating, deleting the Helm chart.
* _`catbp/fabric-console`_— chart name, where “catbp” is a repository name, “fabric-console” is the chart name.
* _`values.yaml`_ — a values file.
* _`${ns_name}`_ — name of the namespace you've created [before.](aws.md#4.-create-a-namespace-for-the-catalyst-blockchain-platform-hyperledger-fabric-service-application)

You can **check the status** of the installation by using these commands:

* _`helm ls`_— check the "status" field of the installed chart.

{% hint style="success" %}
Status “deployed” should be shown.
{% endhint %}

* _`kubectl get pods`_— get the status of applications separately.

{% hint style="success" %}
All pods statuses must be “running.”
{% endhint %}

* _`kubectl describe pod $pod_name`_ — get detailed information about pods.

{% hint style="info" %}
The following rbac will be created:
{% endhint %}

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: {{ include "fabric-console.fullname" . }}
  labels:
    {{- include "fabric-console.labels" . | nindent 4 }}
rules:
- apiGroups:
  - "*"
  resources:
  - namespaces
  - pods
  - services
  - persistentvolumes
  - persistentvolumeclaims
  - persistentvolumeclaims/finalizers
  - events
  - secrets
  - customresourcedefinitions
  - deployments
  - peersets
  - peersets/finalizers
  - peers
  - peers/status
  - peers/finalizers
  - orderingservices
  - orderingservices/finalizers
  - orderers
  - orderers/status
  - orderers/finalizers
  - chaincodeservices
  - chaincodeservices/status
  - chaincodeservices/finalizers
  - fabriccas
  - fabriccas/status
  - fabriccas/finalizers
  - configmaps
  {{- if .Values.openshiftRoute.enabled }}
  - routes
  - routes/custom-host
  {{- else }}
  - ingressroutetcps
  - ingressroutes
  {{- end }}
  verbs:
  - get
  - watch
  - list
  - create
  - update
  - patch
  - delete
  - create
```
