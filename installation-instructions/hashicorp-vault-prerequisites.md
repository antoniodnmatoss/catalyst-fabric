# Hashicorp Vault (prerequisites)

#### 1. Deploy Hashicorp Vault.&#x20;

{% hint style="info" %}
Supported versions: 1.8 and later.
{% endhint %}

Installation manuals:[ https://www.vaultproject.io/docs/install](https://www.vaultproject.io/docs/install)\


#### 2. Create a set of policies with the following rules

```
path "{{PATH_PREFIX}}/*" {
    capabilities = ["read", "delete", "list", "create", "update"]
}

path "auth/approle/role/*" {
    capabilities = ["read"]
}

path "auth/token/lookup-self" {
    capabilities = ["read"]
}

path "auth/approle/role/+/secret-id" {
    capabilities = ["update"]
}

path "auth/token/renew-self" {
    capabilities = ["update"]
}

path "auth/token/create" {
    capabilities = ["update"]
}
```

where \{{<mark style="color:blue;">`PATH_PREFIX}`</mark> refers to the folder where all the secrets of this role will be stored.



To create it from a CLI use the following:

<mark style="color:blue;background-color:blue;">`$ vault policy write ${{POLICIES_NAME}} ./catalyst.hcl`</mark>

where <mark style="color:blue;">`{{POLICIES_NAME}}`</mark>– any name you give, supposing you’ve created a file “catalyst.hcl” with the policies listed above.

#### 3. Enable the “AppRole” auth method

Catalyst Blockchain Platform must be able to authenticate into Vault for managing the secrets and mounting them into pods. In the current version, only [AppRole](https://www.vaultproject.io/docs/auth/approle) is supported.&#x20;

`$ vault auth approle enable`

#### 4. Create an AppRole

<mark style="color:blue;">`$ vault write auth/approle/role/{{ROLE_NAME}} token_policies={{POLICIES_NAME}} token_ttl=5m token_max_ttl=10m token_no_default_policy=true`</mark>

where:

* <mark style="color:blue;">\{{POLICIES\_NAME\}}</mark> refers to the policy set created in [step 2.](hashicorp-vault-prerequisites.md#2.-create-a-set-of-policies-with-the-following-rules)
* <mark style="color:blue;">\{{ROLE\_NAME\}}</mark> – can be any.

#### 5. Read the ID of the created role

<mark style="color:blue;">`$ vault read auth/approle/role/{{ROLE_NAME}}/role-id -format=json`</mark>

#### 6. Generate a secret for the created role and persist the output

<mark style="color:blue;">`$ vault write -f auth/approle/role/{{ROLE_NAME}}/secret-id -format=json`</mark>

{% hint style="info" %}
NOTE: In case of a leak of the secret, type the following command:

<mark style="color:blue;">`$ vault write auth/approle/role/{{ROLE_NAME}}/secret-id-accessor/destroy secret_id_accessor={{SECRET_ID_ACCESSOR}}`</mark>

where <mark style="color:blue;">`{{SECRET_ID_ACCESSOR}}`</mark> is obtained from the output of the command for generating a secret for the role.

After that follow step 5 and update the [helm chart values.](./#configure-helm-chart-values)
{% endhint %}

#### 7. Put the Vault TLS certificate to the Kubernetes secret&#x20;

Put your Vault TLS certificate with the trust chain to the Kubernetes secret called `“vault-tls”`.

{% hint style="info" %}
Note: The Kubernetes secret name is specified in the [helm chart values.](./#configure-helm-chart-values)
{% endhint %}
