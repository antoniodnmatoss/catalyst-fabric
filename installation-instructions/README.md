# Installation Instructions

## Prerequisites

### &#x20;1. Setup Kubernetes **or OpenShift** cluster

{% hint style="info" %}
Supported version of **OpenShift:** 4.7.\
Supported version of **Kubernetes:** 1.17 and later.\
\
We recommend AWS (EKS) or Google Cloud (GKE), but you can install it on a standalone cluster as well.&#x20;
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

{% hint style="info" %}
Supported version of Helm: 3.\*.
{% endhint %}

### **3. Install Traefik ingress**

The ingress-controller is needed for traffic routing to expose nodes (peer, CA, orderer). The Catalyst Blockchain Platform Hyperledger Fabric service creates a CRD resource (IngressRouteTCP in case of using Traefik or Route in case of using OpenShift), that is automatically started and deleted along with each node.&#x20;

Installation manuals: [https://github.com/traefik/traefik-helm-chart\
](https://github.com/traefik/traefik-helm-chart)No customization is needed, the default port ( :443 ) for HTTPS traffic will be used.

{% hint style="info" %}
_Note:_ We recommend installing Traefik to a separate namespace from the application (creation of a namespace for the Catalyst Blockchain Platform Hyperledger Fabric service is described in step 6).
{% endhint %}

{% hint style="info" %}
Supported version of Traefik: 2.3.
{% endhint %}

In case of using **OpenShift,** you should skip this step and specify it in the Helm chart values later (Helm chart values are described in the Setup section), because OpenShift has a built-in ingress-controller server.&#x20;

### **4. Install cert-manager** **to create TLS certificate**

TLS certificate is needed for secured communication between a User and the Сatalyst Blockchain Platform Hyperledger Fabric service components.&#x20;

Installation manuals: [https://cert-manager.io/docs/installation/helm/\
](https://cert-manager.io/docs/installation/helm/)We recommend using the last release of the official helm chart.

{% hint style="info" %}
_Note:_ You can skip this step and specify your TLS certificate and key as a Kubernetes secret in Helm chart values instead later (Helm chart values are described in the Setup section). You can find the manual on how to create a Kubernetes secret here: https://kubernetes.io/docs/concepts/configuration/secret/#tls-secrets
{% endhint %}

### **5. Create an A-record in a zone in your domain's DNS management panel and assign it to the load balancer created upon Traefik or OpenShift installation**

Catalyst Blockchain Platform Hyperledger Fabric service needs a wildcard record `*.<domain>` to expose nodes. All created nodes (peers, orderers, CAs) will have a `<NodeName>.<domainName>` address.

For example, in case you are using AWS, follow these steps:

1. Go to the Route53 service.
2. Create a new domain or choose the existing domain.
3. Create an A record.
4. Switch “alias” to ON.
5. In the “Route traffic to” field select “Alias to application and classic load balancer.”
6. Select your region (where the cluster is installed).
7. Select an ELB balancer from the drop-down list.\*

\*Choose the ELB balancer, which was automatically configured upon the Traefik chart installation as described in step 3 (or upon OpenShift installation in case of using OpenShift). You can check the ELB by the following command:

```
kubectl get svc -n ${ingress-namespace}
```

where `${ingress-namespace}` — the name of the namespace, where the ingress was installed.\
ELB is displayed in the `EXTERNAL-IP` field.

### **6. Create a namespace for the Catalyst Blockchain Platform Hyperledger Fabric service application**

```
kubectl create ns ${ns_name}
```

where _<mark style="color:blue;">`${ns_name`</mark>`}`_ — name of namespace (can be any).\
\
6.1. Get the credentials to the Helm repository in the JFrog artifactory provided by the IntellectEU admin team.

6.2. Add the repo to Helm with the username and password provided:

```
helm repo add catbp <https://intellecteu.jfrog.io/artifactory/catbp-helm> --username ${ARTIFACTORY_USERNAME} --password ${ARTIFACTORY_PASSWORD}
```

As a result: _`"catbp" has been added to your repositories`_

### **7. Create an ImagePullSecret to access the Catalyst Blockchain Platform Hyperledger service deployable images**

For example, create this Secret, naming it `intellecteu-jfrog-access`:

```
kubectl create secret intellecteu-jfrog-access regcred --docker-server=intellecteu-catbp-docker.jfrog.io --docker-username=${your-name} --docker-password=${your-password} --docker-email=${your-email} -n ${ns_name}
```

where:

* <mark style="color:blue;">`${your-name}`</mark> — your Docker username.
* <mark style="color:blue;">`${your-password}`</mark> — your Docker password.
* <mark style="color:blue;">`${your-email}`</mark> — your Docker email.
* <mark style="color:blue;">`${ns_name`</mark><mark style="color:blue;">}</mark> — the namespace created for the Catalyst Blockchain Platform Hyperledger Fabric service on the previous step.

### **8. Deploy a message broker**

A message broker is needed by the Catalyst Blockchain Platform Hyperledger Fabric service to schedule commands, emit events, and control workflows.

{% hint style="info" %}
Currently, only **RabbitMQ** is supported.&#x20;

**Version:** 3.7 and later.
{% endhint %}

No specific configurations are needed. You can check the official production checklist: [https://www.rabbitmq.com/production-checklist.html](https://www.rabbitmq.com/production-checklist.html)

We recommend 1GB RAM as a minimum setup.

{% hint style="info" %}
_Info:_ In case you want to use a readiness check and use a private repository for the image, you should create a “secret” file with your credentials in Kubernetes/OpenShift for further specifying it in the Helm chart upon Catalyst Blockchain Platform installation. Please refer to the official Kubernetes documentation: [https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)

Helm chart configuration instructions you will find [here. ](./#configure-helm-chart-values)
{% endhint %}

### **9. Deploy a database**

A database is required by the Catalyst Blockchain Platform Hyperledger Fabric service to support internal architecture for workflows as well as store users' action logs.

{% hint style="info" %}
_Info:_ No secure data is stored in the database.
{% endhint %}

Catalyst Blockchain Platform supports PostgreSQL and MySQL. You can use any.&#x20;

{% hint style="info" %}
Supported version of PostgreSQL: 12.8 and later.

Supported version of MySQL: 8.0.21 and later.
{% endhint %}

No specific configurations are needed. You can use the official manuals:&#x20;

* MySQL: [https://dev.mysql.com/doc/mysql-getting-started/en/#mysql-getting-started-installing](https://dev.mysql.com/doc/mysql-getting-started/en/#mysql-getting-started-installing)
* PostgreSQL: [https://www.postgresql.org/docs/12/tutorial-install.html](https://www.postgresql.org/docs/12/tutorial-install.html)

We recommend 1GB RAM as a minimum setup.

{% hint style="info" %}
_Info:_ In case you want to use a readiness check and use a private repository for the image, you should create a “secret” file with your credentials in Kubernetes/OpenShift for further specifying it in the Helm chart upon Catalyst Blockchain Platform installation. Please refer to the official Kubernetes documentation: [https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)

Helm chart configuration instructions you will find [here. ](./#configure-helm-chart-values)
{% endhint %}

### **10. Setup secret storage (optional)**

A digital identity is represented as a private key and x509 certificate. All identities that were enrolled through Catalyst Blockchain Platform are stored in secret storage. Underlying providers of the storage are **Kubernetes Secrets** and **Hashicorp Vault.**&#x20;

1. **Kubernetes** is enabled by default. It means that for each enrolled identity there is a corresponding Kubernetes secret. If you want to proceed with Kubernetes secret, you can skip this section and use the default configuration for secret storage in the helm chart [values. ](./#configure-helm-chart-values)
2. The other option allows users to store generated identities in **Hashicorp Vault,** thus having more control over them, better encryption, backups, and other benefits. If you want to use Hashicorp Vault instead of Kubernetes secrets, please check the prerequisites [here](hashicorp-vault-prerequisites.md).

{% hint style="info" %}
#### Operator:

While the mounting process of a Kubernetes secret is straightforward it is not the same for a Hashicorp Vault secret. Catalyst Blockchain Platform operator is responsible for that. All pods that require secrets from Hashicorp Vault have an additional init container <mark style="color:blue;">`vault-env`</mark>which is based on <mark style="color:blue;">`hashicorp/consul-template.`</mark>The operator creates a temporal short-lived token and config for <mark style="color:blue;">`consule-template`</mark><mark style="color:blue;">.</mark> The last one authenticates using the token, fetches the secret, and places it into a volume, that is shared with the main container.

#### API:

Each action with Hyperledger Fabric SDK requires an identity and all identities are stored in Hashicorp Vault. It might affect the performance of the API, that’s why caching mechanism was introduced. Whenever an identity is loaded from Hashicorp Vault it will be placed into the cache for TTL. Default TTL is 10 seconds.
{% endhint %}

### **As a result, you will get:**

1. Kubernetes (or Openshift) cluster deployed.&#x20;
2. Helm installed to your workstation.
3. Traefik ingress installed to your Kubernetes cluster.\
   In case of using OpenShift, you should skip this step.
4. Cert-manager installed to your cluster or TLS certificate prepared.
5. A-record created, for example, in your account on AWS or Google Cloud.
6. Namespace created in your cluster and Helm repository added to your workstation.
7. Kubernetes (OpenShift) secret created in the namespace on your Kubernetes (OpenShift) cluster.
8. A message broker (RabbitMQ) deployed.
9. A database deployed.
10. (optional) Hashicorp Vault installed.

## Setup

### Configure helm chart values

The following values are needed to be configured.

#### `1. domainName`

```yaml
# -- address where the application will be hosted. All created nodes (peers, orderers, cas) will have <NodeName>.proxy.<domainName> address
domainName: ""
```

#### `2. auth`&#x20;

{% hint style="info" %}
You can choose one of two possible methods:&#x20;

* `basicAuth`&#x20;
* `openID`&#x20;
{% endhint %}

```yaml
# -- auth config
auth:
  # -- enabled auth for api/v1 endpoints
  enabled: true
  # -- available methods are: 'basic', 'openid'
  method: basic
  # -- BasicAuth
  basic:
    ## -- BasicAuth username
    username: ""
    ## -- BasicAuth password
    password: ""
    ## -- Or specify secure credentials using Kubernetes secret
    # -- Create a new Secret using these keys:
    # username
    # password
    authSecret: ""  
  
  # -- OpenID authorization scheme. Only public access type is supported. 
  openid:
    ## --OpenID provider endpoint for obtaining access token
    url: ""
    ## -- OpenID configuration is a Well-known URI Discovery Mechanism
    wellKnownURL: ""
    ## - OpenID client ID
    clientID: ""
    ## -- Enable role based auth for openId client
    roleBasedAuthEnabled: false
    # # - OpenID client secret
    # clientSecret: ""
```

#### `3. ingressConfig`&#x20;

```yaml
# -- Ingress for any ingress controller. 
ingressConfig:
  provider:
    # -- #Currently supported traefik openshift ingress controller and istio: [traefik, openshift, istio]
    name: traefik #openshift, istio
    traefik:
      ingressClass: ""
    traefikCRD:
      tlsStore:
        enabled: false
        name: default
    openshift:
    istio:
      # -- Istio gateway name
      gateway: ""
      # -- match port for tls config
      port: 443
  # -- specify whether to create Ingres resources for API and UI
  enabled: false
  tls:
    enabled: false
    # -- Certificate and Issuer will be created with Cert-Manager. Names will be autogenerated.
    # if `certManager.enabled` `ingressConfig.tls.secretName` will be ignored
    certManager:
      enabled: false
      email: "your-email@example.com"
      server: "https://acme-staging-v02.api.letsencrypt.org/directory"
    # -- secret name with own tls certificate to use with ingress
    secretName: ""
```

#### `4. amqp`

Configure connection settings to [your message broker.](./#8.-deploy-a-message-broker)

```yaml
# -- external RabbitMQ Message broker parameters
amqp:
  readinessCheck:
    # -- Whether to perform readiness check with initContainer. Simple `nc` command
    enabled: true
    # -- which image to use for initContainer performing readiness check
    initContainer:
      image:
        repository: busybox
        pullPolicy: IfNotPresent
        tag: latest
  # -- example values for rabbitmq queue. change them for your env
  host: "rabbitmq.rabbitmq"
  port: "5672"
  # -- Specify the Secret name if using Kubernetes Secret to provide credentials
  # -- Create a new Secret using these keys:
  #  username
  #  password
  credentialsSecret:
  # -- Specify username & password if using Helm values file to provide credentials
  username: "test1"
  password: "Abcd1234"
  vhost: "test1"
```

{% hint style="info" %}
_Info:_ In case of using a private repository specify the secret you [created before](./#8.-deploy-a-message-broker) in the api.imagePullSecrets section:
{% endhint %}

```yaml
api:
  imagePullSecrets:
    - name: mysecret1 # for registry with api images
    - name: mysecret2 # for registry with busybox images

```

#### `5. database`

Configure connection settings to [your database.](./#9.-deploy-a-database)

```yaml
# -- external database parameters
database:
  readinessCheck:
    # -- Whether to perform readiness check with initContainer. Simple `nc` command
    enabled: true
    # -- which image to use for initContainer performing readiness check
    initContainer:
      image:
        repository: busybox
        pullPolicy: IfNotPresent
        tag: latest
  # -- database type. `postgres` or `mysql` can be specified here
  type: postgres
  # -- example values for postgres database. change them for your env
  host: "postgresql.postgresql"
  port: "5432"
  # -- Specify the Secret name if using Kubernetes Secret to provide credentials
  # -- Create a new Secret using these keys:
  #  username
  #  password
  credentialsSecret:
  # -- Specify username & password if using Helm values file to provide credentials
  username: "test1"
  password: "Abcd1234"
  dbname: "test1"
  # -- ignore password and use AWS IAM authentication into the database
  authAws: false

```

{% hint style="info" %}
_Info:_ In case of using a private repository specify the secret you [created before ](./#9.-deploy-a-database)in the api.imagePullSecrets section:
{% endhint %}

```yaml
api:
  imagePullSecrets:
    - name: mysecret1 # for registry with api images
    - name: mysecret2 # for registry with busybox images

```

#### `6. identityStore`&#x20;

(in case of using [Hashicorp Vault](./#10.-setup-secret-storage-optional)).

```yaml
#by default it's k8s
identityStore: vault 
vault:
	#enable usage of vault client
  enabled: true
	# AppRole id
  roleId: <role_id>
  # AppRole secret or wrapped token with secret
  secretId: <secret_id>
  # Vault address 
  address: https://vault.address.io
	# Prefix for all secrets
  pathPrefix: secret/apps/fabric/org1
	# Signals to client that secretId is a wrapped token 
  withWrappingToken: false
	# vault-env init container config
	vaultenv:
    # docker image for vault-env init container, default value is hashicorp/consul-template
    image:
  tls:
		# force to skip TLS verification at handshake
    skipVerify: true
```

You can configure other helm chart values if needed.&#x20;

#### The full list of the helm chart values

```yaml
## -- Declare variables to be passed into your templates.

# -- address where application will be hosted. All created nodes (peers, orderers, cas) will have <NodeName>.<domainName> address
domainName: ""
logs:
  level: info
# -- auth config
auth:
  # -- enabled auth for api/v1 endpoints
  enabled: true
  # -- available methods are: `basic`, `openid`
  method: basic
  # -- BasicAuth
  basic:
    ## -- BasicAuth username
    username: ""
    ## -- BasicAuth password
    password: ""
    ## -- Or specify secure credentials using Kubernetes secret
    # -- Create a new Secret using these keys:
    # username
    # password
    authSecret: ""
  # -- OpenID authorization mechanism
  openid:
    ## --OpenID provider endpoint for obtaining access token
    url: ""
    ## -- OpenID configuration is a Well-known URI Discovery Mechanism
    wellKnownURL: ""
    ## - OpenID client ID
    clientID: ""
    ## -- Enable role based auth for openId client
    roleBasedAuthEnabled: false
    # # - OpenID client secret
    # clientSecret: ""
## -- Identity Store provider defines where to store digital identities of an organization. Supported providers are: k8s(default), vault.
identityStore: k8s
## -= HashicorpVault client configuration    
vault:
  ## -- Enable usage of Vault client
  enabled: false
  ## -- AppRole ID
  roleId: <approle roleID>
  ## -- AppRole Secret
  secretId: <approle secretID>
  ## - Flag that tells secretId is a wrapped token
  withWrappingToken: false
  ## -- address https://vault.example.com
  address: https://vault.example.com
  ## -- path prefix for secrets
  pathPrefix: secrets/fabric-console
  ## -- TLS configuration
  tls:
    ## -- Do not verify certificate presented by Vault server
    skipVerify: false
    ## - Secret name with CA trust chain
    certsSecretName: vault-tls
# -- message bus configuration
messageBus:
  queue: message_bus
  topic: message_bus_exchange
# -- this module enabled integration with prometheus-operator. Fetches metrics from all the peers, orderers and CAs in the system
monitoring:
  # -- specify whether to create monitoring resources
  # prometheus operator and grafana need to be installed beforehand
  enabled: false
  # -- configuration for ServiceMonitor resource
  serviceMonitor:
    enabled: false
    # -- how often to pull metrics from resources
    interval: 15s
    # -- HTTP path to scrape for metrics
    path: /metrics
    # -- RelabelConfigs to apply to samples before scraping
    relabelings: []
    # -- MetricRelabelConfigs to apply to samples before ingestion
    metricRelabelings: []
  grafana:
    # -- grafana default admin username and email. Grafana is authenticated through default API authentication automatically.
    user: admin
    email: admin@domain.com
    # -- grafana defaul path to dashboard
    dashboardPath: "/grafana/d/pUnN6JgWz/hyperledger-fabric-monitoring?orgId=1&refresh=30s&kiosk&var-namespace="
    # -- grafana service and port for ingress
    service:
      name: grafana
      namespace: monitoring
      port: 80
# -- Ingress for any ingress controller. 
ingressConfig:
  provider:
    # -- #Currently supported traefik openshift ingress controller and istio: [traefik, openshift, istio]
    name: traefik #openshift, istio
    traefik:
      ingressClass: ""
    traefikCRD:
      tlsStore:
        enabled: false
        name: default
    openshift:
    istio:
      # -- Istio gateway name
      gateway: ""
      # -- match port for tls config
      port: 443
  # -- specify whether to create Ingres resources for API and UI
  enabled: false
  tls:
    enabled: false
    # -- Certificate and Issuer will be created with Cert-Manager. Names will be autogenerated.
    # if `certManager.enabled` `ingressConfig.tls.secretName` will be ignored
    certManager:
      enabled: false
      email: "services.cat-bp@intellecteu.com"
      server: "https://acme-staging-v02.api.letsencrypt.org/directory"
    # -- secret name with own tls certificate to use with ingress
    secretName: ""
# -- Configuration options to control how public and private docker repositories handled by api and operator
imageVerification:
  # -- Do not verify image existence in docker registry for Public type
  disabled: false
rbac:
  # -- Whether to create RBAC Resourses (Role, SA, RoleBinding)
  enabled: true
  # -- Service Account Name to use for api, ui, operator, consumer
  serviceAccountName: fabric-console
  # -- Automount API credentials for a Service Account.
  automountServiceAccountToken: false
# operator component values
operator:
  # -- number of operator pods to run
  replicaCount: 1
  # -- operator image settings
  image:
    repository: intellecteu-catbp-docker.jfrog.io/catbp/fabric-platform/fabric-console
    tag: 2.6
  # -- operator image pull secrets
  imagePullSecrets:
    - name: intellecteu-jfrog-access
  labels: {}
  # -- image for init MSP container used in peers and orderer. Default value in application is intellecteu/msp-init:1.0
  mspInitContainerImage:
  # -- Configs for controlled CRDS
  crd:
    serviceAccount:
    # -- PodSecurityContext that will be applied into all pods created from CRDs 
    podSecurityContext:
        # runAsNonRoot: true
        # runAsUser: 4444
        # runAsGroup: 5555
        # fsGroup: 4444
  # -- annotations for operator pods
  podAnnotations: {}
  # -- Automount API credentials for a Service Account.
  automountServiceAccountToken: true
  # -- security context on a pod level
  podSecurityContext:
    # runAsNonRoot: true
    # runAsUser: 4444
    # runAsGroup: 5555
    # fsGroup: 4444
  # -- security context on a container level
  securityContext: {}
  # -- CPU and Memory requests and limits
  resources:
    limits:
      cpu: "150m"
      memory: "300Mi"
    requests:
      cpu: "100m"
      memory: "100Mi"
  # -- Specify Node Labels to place operator pods on
  nodeSelector: {}
  # -- https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/
  tolerations: []
  # -- https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#node-affinity
  affinity: {}
  # -- metrics server and Prometheus Operator configuration
  metrics:
    # -- should metrics server be enabled
    enabled: false
    # -- service port for metrics server
    servicePort: 8082
    # -- container port for metrics server
    containerPort: 8082
    # -- HTTP path to scrape for metrics.
    path: /metrics
    serviceMonitor:
      # -- should ServiceMonitor be created
      enabled: false
      # -- how often to pull metrics from resources
      interval: 30s
      # -- RelabelConfigs to apply to samples before scraping
      relabelings: []
      # -- MetricRelabelConfigs to apply to samples before ingestion.
      metricRelabelings: []
## api component values
api:
  # -- api autoscaling settings
  autoscaling:
    enabled: false
    minReplicas: 1
    maxReplicas: 5
    targetCPUUtilizationPercentage: 80
    # targetMemoryUtilizationPercentage: 80
  # -- number of api pods to run
  replicaCount: 1
  # -- api image settings
  image:
    repository: intellecteu-catbp-docker.jfrog.io/catbp/fabric-platform/fabric-console
    tag: 2.6
  # -- api image pull secrets
  imagePullSecrets:
    - name: intellecteu-jfrog-access
  labels: {}
  # -- api service port and name
  service:
    port: 8000
    portName: http
  # -- annotations for api pods
  podAnnotations: {}
  # -- Automount API credentials for a Service Account.
  automountServiceAccountToken: true
  # -- securtiry context on a pod level
  podSecurityContext:
    # runAsNonRoot: true
    # runAsUser: 4444
    # runAsGroup: 5555
    # fsGroup: 4444
  # -- security context on a container level
  securityContext: {}
  # -- CPU and Memory requests and limits
  resources:
    limits:
      cpu: "150m"
      memory: "500Mi"
    requests:
      cpu: "100m"
      memory: "200Mi"
  # -- Specify Node Labels to place api pods on
  nodeSelector: {}
  # -- https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/
  tolerations: []
  # -- https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#node-affinity
  affinity: {}
  # -- metrics server and Prometheus Operator configuration
  metrics:
    # -- should metrics server be enabled
    enabled: false
    # -- service port for metrics server
    servicePort: 8082
    # -- container port for metrics server
    containerPort: 8082
    # -- HTTP path to scrape for metrics.
    path: /metrics
    serviceMonitor:
      # -- should ServiceMonitor be created
      enabled: false
      # -- how often to pull metrics from resources
      interval: 30s
      # -- RelabelConfigs to apply to samples before scraping
      relabelings: []
      # -- MetricRelabelConfigs to apply to samples before ingestion.
      metricRelabelings: []
ui:
  # -- number of ui pods to run
  replicaCount: 1
  # -- ui image settings
  image:
    repository: intellecteu-catbp-docker.jfrog.io/catbp/fabric-platform/fabric-console
    tag: 2.6
  # -- api image pull secrets
  imagePullSecrets:
    - name: intellecteu-jfrog-access
  # -- ui service port and name
  service:
    port: 3001
    portName: http
  # -- annotations for consumer pods
  podAnnotations: {}
  # -- security context on a pod level
  podSecurityContext:
    # runAsNonRoot: true
    # runAsUser: 101
    # runAsGroup: 101
    # fsGroup: 101
  # -- security context on a container level
  securityContext: {}
  labels: {}
  # -- CPU and Memory requests and limits
  resources:
    limits:
      cpu: "100m"
      memory: "100Mi"
    requests:
      cpu: "30m"
      memory: "50Mi"
  # -- Specify Node Labels to place ui pods on
  nodeSelector: {}
  # -- https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/
  tolerations: []
  # -- https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#node-affinity
  affinity: {}
# -- external RabbitMQ Message broker parameters
amqp:
  readinessCheck:
    # -- Whether to perform readiness check with initContainer. Simple `nc` command
    enabled: true
    # -- which image to use for initContainer performing readiness check
    initContainer:
      image:
        repository: busybox
        pullPolicy: IfNotPresent
        tag: latest
  # -- example values for rabbitmq queue. change them for your env
  host: "rabbitmq.rabbitmq"
  tls: false
  port: "5672"
  # -- Specify the Secret name if using Kubernetes Secret to provide credentials
  # -- Create a new Secret using these keys:
  #  username
  #  password
  credentialsSecret:
  # -- Specify username & password if using Helm values file to provide credentials
  username: "test1"
  password: "Abcd1234"
  vhost: "test1"
# -- external database parameters
database:
  readinessCheck:
    # -- Whether to perform readiness check with initContainer. Simple `nc` command
    enabled: true
    # -- which image to use for initContainer performing readiness check
    initContainer:
      image:
        repository: busybox
        pullPolicy: IfNotPresent
        tag: latest
  # -- database type. `postgres` or `mysql` can be specified here
  type: postgres
  # -- example values for postgres database. change them for your env
  host: "postgresql.postgresql"
  tls: false
  port: "5432"
  # -- Specify the Secret name if using Kubernetes Secret to provide credentials
  # -- Create a new Secret using these keys:
  #  username
  #  password
  credentialsSecret:
  # -- Specify username & password if using Helm values file to provide credentials
  username: "test1"
  password: "Abcd1234"
  dbname: "test1"
  # -- ignore password and use AWS IAM authentication into the database
  authAws: false

# -- enables the use of aws sdk 
awsIAM:
  enabled: false
```

### **Install the Catalyst Blockchain Platform Hypeledger Fabric service**&#x20;

Use the following command:

```
helm upgrade --install ${fabric_release_name} catbp/fabric-console --values values.yaml -n ${ns_name}
```

where:

*   _<mark style="color:blue;">`${fabric_release_name}`</mark>_ — name of the Catalyst Blockchain Platform Hypeledger Fabric service&#x20;

    &#x20;release. You can choose any name/alias. It is used to address for updating, deleting the Helm chart.
* _<mark style="color:blue;">`catbp/fabric-console`</mark>_— chart name, where “catbp” is a repository name, “fabric-console” is the chart name.
* _<mark style="color:blue;">`values.yaml`</mark>_ — a values file.
* _<mark style="color:blue;">`${ns_name}`</mark>_ — name of the namespace you've created [before. ](./#6.-create-a-namespace-for-the-catalyst-blockchain-platform-hyperledger-fabric-service-application)

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
