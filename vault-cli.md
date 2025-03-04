---

copyright:
  years: 2020, 2022
lastupdated: "2022-01-11"

keywords: Vault CLI, use Secrets Manager with Vault CLI, CLI commands, create secret with CLI, log in to Vault

subcollection: secrets-manager

---

{:codeblock: .codeblock}
{:screen: .screen}
{:download: .download}
{:external: target="_blank" .external}
{:faq: data-hd-content-type='faq'}
{:gif: data-image-type='gif'}
{:important: .important}
{:note: .note}
{:pre: .pre}
{:tip: .tip}
{:preview: .preview}
{:deprecated: .deprecated}
{:beta: .beta}
{:term: .term}
{:shortdesc: .shortdesc}
{:script: data-hd-video='script'}
{:support: data-reuse='support'}
{:table: .aria-labeledby="caption"}
{:troubleshoot: data-hd-content-type='troubleshoot'}
{:help: data-hd-content-type='help'}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:tsSymptoms: .tsSymptoms}
{:video: .video}
{:step: data-tutorial-type='step'}
{:tutorial: data-hd-content-type='tutorial'}
{:api: .ph data-hd-interface='api'}
{:cli: .ph data-hd-interface='cli'}
{:ui: .ph data-hd-interface='ui'}
{:curl: .ph data-hd-programlang='curl'}
{:java: .ph data-hd-programlang='java'}
{:ruby: .ph data-hd-programlang='ruby'}
{:c#: .ph data-hd-programlang='c#'}
{:objectc: .ph data-hd-programlang='Objective C'}
{:python: .ph data-hd-programlang='python'}
{:javascript: .ph data-hd-programlang='javascript'}
{:php: .ph data-hd-programlang='PHP'}
{:swift: .ph data-hd-programlang='swift'}
{:curl: .ph data-hd-programlang='curl'}
{:dotnet-standard: .ph data-hd-programlang='dotnet-standard'}
{:go: .ph data-hd-programlang='go'}
{:unity: .ph data-hd-programlang='unity'}
{:release-note: data-hd-content-type='release-note'}

# Vault CLI commands
{: #vault-cli}

You can use the HashiCorp Vault command-line interface (CLI) to interact with {{site.data.keyword.secrets-manager_full}}.
{: shortdesc}

{{site.data.keyword.secrets-manager_short}} uses a custom version of open source HashiCorp Vault. This custom version adds the IBM Cloud IAM `auth` method and a set of secret engines to support operations in {{site.data.keyword.secrets-manager_short}} for various secret types.

Before you get started, [configure the Vault CLI](/docs/secrets-manager?topic=secrets-manager-configure-vault-cli) so that you're able to access your {{site.data.keyword.secrets-manager_short}} instance by using Vault commands. To learn more about using the Vault CLI, check out the [Vault documentation](https://www.vaultproject.io/docs/commands){: external}.
{: note}

## Log in
{: #vault-cli-login}

### Configure a login token
{: #vault-cli-write-token-config}

Use this command to configure the time-to-live (TTL) and lifespan (MaxTTL) of a Vault login token.

```sh
vault write auth/ibmcloud/manage/login [token_ttl=DURATION] [token_max_ttl=MAX_DURATION]
```
{: codeblock}

#### Prerequisites
{: #vault-cli-write-token-config-prereqs}

You need the [**Manager** service role](/docs/secrets-manager?topic=secrets-manager-iam) to manage the configuration of login tokens.

#### Command options
{: #vault-cli-write-token-config-options}

token_ttl
:   The initial time-to-live (TTL) of the login token to generate. Default is `1h`.

token_max_ttl
:   The maximum lifespan of the login token. Default is `24h`. This value can't exceed the Vault `MaxLeaseTTL` value.

#### Examples
{: #vault-cli-write-token-config-examples}

Configure a Vault login token by entering `30m` for the initial time-to-live and `2h` for the maximum lifespan.

```sh
vault write auth/ibmcloud/manage/login token_ttl=30m token_max_ttl=2h
```
{: pre}

#### Output
{: #vault-cli-write-token-config-output}

The command returns the following output:
```plaintext
Success! Data written to: auth/ibmcloud/manage/login
```
{: screen}

### View the configuration of a token
{: #vault-cli-read-token-config}

Use this command to view the configuration of a Vault login token.

```sh
vault read [-format=FORMAT] auth/ibmcloud/manage/login
```
{: codeblock}

#### Prerequisites
{: #vault-cli-read-token-config-prereqs}

You need the [**Manager** service role](/docs/secrets-manager?topic=secrets-manager-iam) to manage the configuration of login tokens.

#### Command options
{: #vault-cli-read-token-config-options}

-format
:   Prints the output in the format that you specify. Valid formats are `table`, `json`, and `yaml`. The default is `table`. You can also set the output format by using the `VAULT_FORMAT` environment variable.

#### Examples
{: #vault-cli-read-token-config-examples}

View the login configuration of a Vault token in JSON format.

```sh
vault read -format=json auth/ibmcloud/manage/login
```
{: pre}

#### Output
{: #vault-cli-read-token-config-output}

The command returns the following output:
```json
{
    "request_id": "4dec6b8a-a277-0755-617c-97e40bcc7c3e",
    "lease_id": "",
    "lease_duration": 0,
    "renewable": false,
    "data": {
        "login": {
        "token_max_ttl": "2h0m0s",
        "token_ttl": "30m0s"
    }
    },
    "warnings": null
}
```
{: screen}

## Secret groups
{: #vault-cli-secret-groups}

### Create a secret group
{: #vault-cli-create-secret-group}

Use this command to create a secret group.

```sh
vault write [-format=FORMAT] auth/ibmcloud/manage/groups name=NAME [description="DESCRIPTION"]
```
{: codeblock}

#### Prerequisites
{: #vault-cli-create-secret-group-prereqs}

You need the [**Manager** service role](/docs/secrets-manager?topic=secrets-manager-iam) to create secret groups.

#### Command options
{: #vault-cli-create-secret-group-options}

name
:   The human-readable alias that you want to assign to the secret group. Required.

description
:   An extended description of the secret group.

-format
:   Prints the output in the format that you specify. Valid formats are `table`, `json`, and `yaml`. The default is `table`. You can also set the output format by using the `VAULT_FORMAT` environment variable.

#### Examples
{: #vault-cli-create-secret-group-examples}

Create a secret group with a name and description.

```sh
vault write auth/ibmcloud/manage/groups name="my-secret-group" description="A group of secrets."
```
{: pre}

#### Output
{: #vault-cli-create-secret-group-output}

The command returns the following output:
```plaintext
Key            Value
---            -----
created_at     2020-10-05T17:43:49Z
description    A group of secrets.
id             9c6d20ad-779e-27c5-3842-2a20b19abfcf
name           my-secret-group
type           application/vnd.ibm.secrets-manager.secret.group+json
updated_at     n/a
```
{: screen}

### List secret groups
{: #vault-cli-list-secret-groups}

Use this command to list the secret groups that are available if your {{site.data.keyword.secrets-manager_short}} instance.

```sh
vault read [-format=FORMAT] auth/ibmcloud/manage/groups
```
{: codeblock}

#### Prerequisites
{: #vault-cli-list-secret-groups-prereqs}

You need the [**Reader** service role](/docs/secrets-manager?topic=secrets-manager-iam) to list secret groups.

#### Command options
{: #vault-cli-list-secret-groups-options}

-format
:   Prints the output in the format that you specify. Valid formats are `table`, `json`, and `yaml`. The default is `table`. You can also set the output format by using the `VAULT_FORMAT` environment variable.

#### Examples
{: #vault-cli-list-secret-groups-examples}

Retrieve a list of secret groups in JSON format.

```sh
vault read -format=json auth/ibmcloud/manage/groups
```
{: pre}

#### Output
{: #vault-cli-list-secret-groups-output}

The command returns the following output:
```json
{
    "request_id": "62051bde-9703-101c-a328-90a377a8bb77",
    "lease_id": "",
    "lease_duration": 0,
    "renewable": false,
    "data": {
        "groups": [
        {
        "created_at": "2020-10-05T17:43:49Z",
        "description": "A group of secrets.",
        "id": "9c6d20ad-779e-27c5-3842-2a20b19abfcf",
        "name": "my-secret-group",
        "type": "application/vnd.ibm.secrets-manager.secret.group+json",
        "updated_at": ""
        }
    ]
    },
    "warnings": null
}
```
{: screen}

### Get a secret group
{: #vault-cli-get-secret-group}

Use this command to get the details of a secret group.

```sh
vault read [-format=FORMAT] auth/ibmcloud/manage/groups/SECRET_GROUP_ID
```
{: codeblock}

#### Prerequisites
{: #vault-cli-get-secret-group-prereqs}

You need the [**Reader** service role](/docs/secrets-manager?topic=secrets-manager-iam) to get the details of a secret group.

#### Command options
{: #vault-cli-get-secret-group-options}

-format
:   Prints the output in the format that you specify. Valid formats are `table`, `json`, and `yaml`. The default is `table`. You can also set the output format by using the `VAULT_FORMAT` environment variable.


#### Examples
{: #vault-cli-get-secret-group-examples}

Get the details of a specific secret group in JSON format.

```sh
vault read -format=json auth/ibmcloud/manage/groups/9c6d20ad-779e-27c5-3842-2a20b19abfcf
```
{: pre}

#### Output
{: #vault-cli-get-secret-group-output}

The command returns the following output:
```json
{
    "request_id": "ab6b22d9-8e42-d23f-31d8-a4865b5a40e7",
    "lease_id": "",
    "lease_duration": 0,
    "renewable": false,
    "data": {
        "created_at": "2020-10-05T17:43:49Z",
    "description": "A group of secrets.",
    "id": "9c6d20ad-779e-27c5-3842-2a20b19abfcf",
    "name": "my-secret-group",
    "type": "application/vnd.ibm.secrets-manager.secret.group+json",
    "updated_at": ""
    },
    "warnings": null
}
```
{: screen}

### Update a secret group
{: #vault-cli-update-secret-group}

Use this command to update a secret group.

```sh
vault write [-format=FORMAT] auth/ibmcloud/manage/groups/SECRET_GROUP_ID name=NAME [description="DESCRIPTION"]
```
{: codeblock}

#### Prerequisites
{: #vault-cli-update-secret-group-prereqs}

You need the [**Manager** service role](/docs/secrets-manager?topic=secrets-manager-iam) to update secret groups.

#### Command options
{: #vault-cli-update-secret-group-options}


name
:   The human-readable alias that you want to assign to the secret group. Required.

description
:   An extended description of the secret group.

-format
:   Prints the output in the format that you specify. Valid formats are `table`, `json`, and `yaml`. The default is `table`. You can also set the output format by using the `VAULT_FORMAT` environment variable.


#### Examples
{: #vault-cli-update-secret-group-examples}

Update the name and description of a secret group.

```sh
vault write auth/ibmcloud/manage/groups/9c6d20ad-779e-27c5-3842-2a20b19abfcf name="my-updated-secret-group" description="An updated group of secrets."
```
{: pre}

#### Output
{: #vault-cli-update-secret-group-output}

The command returns the following output:
```plaintext
Key            Value
---            -----
created_at     2020-10-05T17:43:49Z
description    An updated group of secrets.
id             9c6d20ad-779e-27c5-3842-2a20b19abfcf
name           my-updated-secret-group
type           application/vnd.ibm.secrets-manager.secret.group+json
updated_at     2020-10-05T17:56:56Z
```
{: screen}

### Delete a secret group
{: #vault-cli-delete-secret-group}

Use this command to delete a secret group.

```sh
vault delete auth/ibmcloud/manage/groups/SECRET_GROUP_ID
```
{: codeblock}

#### Prerequisites
{: #vault-cli-delete-secret-group-prereqs}

You need the [**Manager** service role](/docs/secrets-manager?topic=secrets-manager-iam) to delete secret groups.


#### Examples
{: #vault-cli-delete-secret-group-examples}

Delete a secret group by its assigned ID.

```sh
vault delete auth/ibmcloud/manage/groups/9c6d20ad-779e-27c5-3842-2a20b19abfcf
```
{: pre}

#### Output
{: #vault-cli-delete-secret-group-output}

The command returns the following output:
```plaintext
Success! Data deleted (if it existed) at: auth/ibmcloud/manage/groups/9c6d20ad-779e-27c5-3842-2a20b19abfcf
```
{: screen}

## Static secrets
{: #vault-cli-static-secrets}

### Create a secret
{: #vault-cli-create-static-secret}

Use the following commands to add a static secret, such as a user credential or an arbitrary secret, to your {{site.data.keyword.secrets-manager_short}} instance. Allowable values for **`SECRET_TYPE`** include: `arbitrary`, `username_password`, `imported_cert`


Create a secret in the `default` secret group.
```sh
vault write [-format=FORMAT] ibmcloud/SECRET_TYPE/secrets name=NAME [description="DESCRIPTION"] [username=USERNAME] [password=USERNAME] [payload=DATA] [expiration_date=EXPIRATION] [certificate=CERTIFICATE_DATA] [private_key=PRIVATE_KEY_DATA] [intermediate=INTERMEDIATE_CERTIFICATE_DATA] [ca=CA_CONFIGURATION_NAME] [dns=DNS_CONFIGURATION_NAME] [key_algorithm=KEY_ALGORITHM] [labels=LABEL,LABEL]
```
{: codeblock}

Create a secret in a specified secret group.
```sh
vault write [-format=FORMAT] ibmcloud/SECRET_TYPE/secrets/groups/SECRET_GROUP_ID name=NAME [description="DESCRIPTION"] [username=USERNAME] [password=USERNAME] [payload=DATA] [expiration_date=EXPIRATION] [certificate=CERTIFICATE_DATA] [private_key=PRIVATE_KEY_DATA] [intermediate=INTERMEDIATE_CERTIFICATE_DATA] [ca=CA_CONFIGURATION_NAME] [dns=DNS_CONFIGURATION_NAME] [key_algorithm=KEY_ALGORITHM] [labels=LABEL,LABEL]
```
{: codeblock}


#### Prerequisites
{: #vault-cli-create-static-secret-prereqs}

You need the [**Writer** service role](/docs/secrets-manager?topic=secrets-manager-iam) to create secrets.

#### Command options
{: #vault-cli-create-static-secret-options}

name
:   The human-readable alias that you want to assign to the secret. Required.

description
:   An extended description to assign to the secret.

expiration_date
:   The expiration date that you want to assign to the secret. Supported for the `arbitrary` and `username_password` secret types. The date format follows [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339).

labels
:   Labels that you can use to group and search for similar secrets in your instance.

payload
:   The data that you want to store for an `arbitrary` secret. Only text-based payloads are supported. If you need to store a binary file, be sure to base64 encode it before saving it to {{site.data.keyword.secrets-manager_short}}. For more information, see [Examples](#vault-cli-create-static-secret-examples).

username
:   The username that you want to assign to a `username_password` secret.

password
:   The password that you want assign to a `username_password` secret.

certificate
:   The certificate data that you want to store for an `imported_cert` secret. Supported file type is `.pem`.

private_key
:   The private key data to store for an `imported_cert` secret. Supported file type is `.pem`.

intermediate
:   Intermediate certificate data to store for an `imported_cert` secret. Supported file type is `.pem`.

ca
:   The [certificate authority configuration](/docs/secrets-manager?topic=secrets-manager-add-certificate-authority) to be used for ordering a `public_cert` secret.

dns
:   The [DNS provider configuration](/docs/secrets-manager?topic=secrets-manager-add-dns-provider) to be used for ordering a `public_cert` secret.

key_algorithm
:   The key algorithm to be used for signing and issuing a `public_cert` secret. Allowable values include: `RSA2048`, `RSA4096`, `ECDSA256`, `ECDSA384`

-format
:   Prints the output in the format that you specify. Valid formats are `table`, `json`, and `yaml`. The default is `table`. You can also set the output format by using the `VAULT_FORMAT` environment variable.


#### Examples
{: #vault-cli-create-static-secret-examples}

Create a user credential with an expiration date and two labels.

```sh
vault write -format=json ibmcloud/username_password/secrets name="my-test-user-credential" expiration_date="2020-12-31T23:59:59Z" username="user123" password="window-steel-dogs-coffee" labels=label-1,label-2
```
{: pre}

Create an arbitrary secret with an expiration date and two labels.

```sh
vault write -format=json ibmcloud/arbitrary/secrets name="my-test-arbitrary-secret" expiration_date="2020-12-31T23:59:59Z" payload="this is my secret data" labels=label-1,label-2
```
{: pre}

Create an arbitrary secret with binary payload.

```sh
base64 -w0 <filename> | vault write ibmcloud/arbitrary/secrets name="my-test-arbitrary-secret" payload=- labels="encode:base64"
```
{: pre}

Import a TLS certificate with a matching private key.

```sh
vault write -format=json ibmcloud/imported_cert/secrets name="my-test-imported-certificate" certificate=@cert.pem private_key=@key.pem
```
{: pre}

Order a TLS certificate by specifying a certificate authority and DNS provider configuration.

```sh
vault write -format=json ibmcloud/public_cert/secrets name="my-test-public-certificate" "ca="my-configured-certificate-authority" dns="my-configured-dns-provider" common_name="example.com" key_algorithm=RSA2048
```
{: pre}


#### Output
{: #vault-cli-create-static-secret-output}

The command to create a `username_password` secret returns the following output:
```json
{
    "request_id": "c8edf459-cc26-d3f9-19e4-a24d899573f4",
    "lease_id": "",
    "lease_duration": 0,
    "renewable": false,
    "data": {
        "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
    "creation_date": "2020-10-05T21:52:29Z",
    "crn": "crn:v1:bluemix:public:secrets-manager:us-south:a/791f5fb10986423e97aa8512f18b7e65:e415e570-f073-423a-abdc-55de9b58f54e:secret:a6067f1c-98cf-9379-6188-a94a58222f5d",
    "expiration_date": "2020-12-31T23:59:59Z",
    "id": "a6067f1c-98cf-9379-6188-a94a58222f5d",
    "labels": [
        "label-1",
        "label-2"
    ],
    "last_update_date": "2020-10-05T21:52:29Z",
    "name": "my-test-user-credential",
    "secret_data": {
        "password": "window-steel-dogs-coffee",
        "username": "user123"
    },
    "secret_type": "USERNAME_PASSWORD",
    "state": 1,
    "state_description": "Active",
    "versions": [
        {
        "auto_rotated": false,
        "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
        "creation_date": "2020-10-05T21:52:29Z",
        "id": "f53b8061-359e-3236-5bcc-fb120e170c87"
        }
    ]
    },
    "warnings": null
}
```
{: screen}

The command to create an `arbitrary` secret returns the following output:
```json
{
    "request_id": "56f8532d-cd2b-372c-7b14-5a5875d1c6e6",
    "lease_id": "",
    "lease_duration": 0,
    "renewable": false,
    "data": {
        "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
    "creation_date": "2020-10-05T21:47:27Z",
    "crn": "crn:v1:bluemix:public:secrets-manager:us-south:a/791f5fb10986423e97aa8512f18b7e65:e415e570-f073-423a-abdc-55de9b58f54e:secret:2ca56a3b-a6e8-d2e2-5377-b6559babfac0",
    "expiration_date": "2020-12-31T23:59:59Z",
    "id": "2ca56a3b-a6e8-d2e2-5377-b6559babfac0",
    "labels": [
        "label-1",
        "label-2"
    ],
    "last_update_date": "2020-10-05T21:47:27Z",
    "name": "my-test-arbitrary-secret",
    "secret_data": {
        "payload": "this is my secret data"
    },
    "secret_type": "ARBITRARY",
    "state": 1,
    "state_description": "Active",
    "versions": [
        {
        "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
        "creation_date": "2020-10-05T21:47:27Z",
        "id": "88473a6c-4877-5079-f999-c9a39e3407ea"
        }
    ]
    },
    "warnings": null
}
```
{: screen}



The command to import an `imported_cert` secret returns the following output:

```json
{
    "request_id": "7b41ad30-8098-9f57-62cf-bd757d43238f",
    "lease_id": "",
    "lease_duration": 0,
    "renewable": false,
    "data": {
        "algorithm": "RSA",
    "common_name": "example.com",
    "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
    "creation_date": "2021-06-03T22:41:56Z",
    "crn": "crn:v1:bluemix:public:secrets-manager:us-south:a/791f5fb10986423e97aa8512f18b7e65:e415e570-f073-423a-abdc-55de9b58f54e:secret:2ca56a3b-a6e8-d2e2-5377-b6559babfac0",
    "expiration_date": "2021-06-04T15:25:44Z",
    "id": "2ca56a3b-a6e8-d2e2-5377-b6559babfac0",
    "intermediate_included": false,
    "issuer": "US Texas Austin Example Corp. Example Org example.com",
    "key_algorithm": "SHA256-RSA",
    "labels": [],
    "last_update_date": "2021-06-03T22:41:56Z",
    "name": "my-test-imported-certificate",
    "private_key_included": false,
    "secret_type": "imported_cert",
    "serial_number": "fc:22:29:7e:57:25:8a:05",
    "state": 1,
    "state_description": "Active",
    "validity": {
        "not_after": "2021-06-04T15:25:44Z",
        "not_before": "2021-06-03T15:25:44Z"
    },
    "versions": [
        {
        "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
        "creation_date": "2021-06-03T22:41:56.354781389Z",
        "expiration_date": "2021-06-04T15:25:44Z",
        "id": "2fae17bf-106a-1c31-55d9-e642ee3803ae",
        "serial_number": "fc:22:29:7e:57:25:8a:05",
        "validity": {
          "not_after": "2021-06-04T15:25:44Z",
          "not_before": "2021-06-03T15:25:44Z"
        }
        }
    ]
    },
    "warnings": null
}
```
{: screen}

The command to order a `public_cert` secret returns the following output:

```json
{
    "request_id": "64824dec-c53e-8e49-dbe7-6c44c0dcd7a0",
    "lease_id": "",
    "lease_duration": 0,
    "renewable": false,
    "data": {
        "alt_names": [],
        "common_name": "example.com",
        "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
        "creation_date": "2021-10-08T03:25:51Z",
        "crn": "crn:v1:bluemix:public:secrets-manager:us-south:a/791f5fb10986423e97aa8512f18b7e65:e415e570-f073-423a-abdc-55de9b58f54e:secret:2ca56a3b-a6e8-d2e2-5377-b6559babfac0",
        "downloaded": false,
        "id": "2c08d55d-1854-4dbd-9c25-bcd26828fe77",
        "issuance_info": {
            "auto_rotated": false,
            "bundle_certs": true,
            "ca": "my-configured-certificate-authority",
            "dns": "my-configured-dns-provider",
            "ordered_on": "2021-10-08T03:25:51Z",
            "state": 0,
            "state_description": "Pre-activation"
        },
        "key_algorithm": "RSA2048",
        "labels": [],
        "last_update_date": "2021-10-08T03:25:51Z",
        "name": "my-test-public-certificate",
        "rotation": {
            "auto_rotate": false,
            "rotate_keys": false
        },
        "secret_type": "public_cert",
        "state": 0,
        "state_description": "Pre-activation",
        "versions": [],
        "versions_total": 1
    },
    "warnings": null
}
```
{: screen}

### List secrets
{: #vault-cli-list-static-secrets}

Use the following commands to list the static secrets in your {{site.data.keyword.secrets-manager_short}} instance. Allowable values for **`SECRET_TYPE`** include: `arbitrary`, `username_password`, `imported_cert`

List secrets by type.
```sh
vault read [-format=FORMAT] ibmcloud/SECRET_TYPE/secrets
```
{: codeblock}

List secrets by secret group.
```sh
vault read [-format=FORMAT] ibmcloud/SECRET_TYPE/secrets/groups/ID
```
{: codeblock}

#### Prerequisites
{: #vault-cli-list-static-secrets-prereqs}

You need the [**Reader** service role](/docs/secrets-manager?topic=secrets-manager-iam) to list secrets.

#### Command options
{: #vault-cli-list-static-secrets-options}

-format
:   Prints the output in the format that you specify. Valid formats are `table`, `json`, and `yaml`. The default is `table`. You can also set the output format by using the `VAULT_FORMAT` environment variable.

#### Examples
{: #vault-cli-list-static-secrets-examples}

Retrieve a list of available user credential secrets in the `default` secret group.

```sh
vault read -format=json ibmcloud/username_password/secrets
```
{: pre}

Retrieve a list of arbitrary secrets that are assigned to a specified secret group.

```sh
vault read -format=json ibmcloud/arbitrary/secrets/groups/9ab2250f-a369-4e07-ade7-d417d63ad587
```
{: pre}

#### Output
{: #vault-cli-list-static-secrets-by-type-output}

The command returns the following output:
```json
{
    "request_id": "65689e3a-7cc6-990e-4f0e-8480edd244ed",
    "lease_id": "",
    "lease_duration": 0,
    "renewable": false,
    "data": {
        "secrets": [
        {
        "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
        "creation_date": "2020-10-06T05:31:05Z",
        "crn": "crn:v1:bluemix:public:secrets-manager:us-south:a/791f5fb10986423e97aa8512f18b7e65:e415e570-f073-423a-abdc-55de9b58f54e:secret:1cf95413-4c10-a5fa-e824-b5106375b129",
        "expiration_date": "2020-12-31T23:59:59Z",
        "id": "1cf95413-4c10-a5fa-e824-b5106375b129",
        "labels": [],
        "last_update_date": "2020-10-06T05:31:05Z",
        "name": "my-test-arbitrary-secret",
        "secret_group_id": "9ab2250f-a369-4e07-ade7-d417d63ad587",
        "secret_type": "ARBITRARY",
        "state": 1,
        "state_description": "Active"
        },
        {
        "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
        "creation_date": "2020-10-06T03:54:26Z",
        "crn": "crn:v1:bluemix:public:secrets-manager:us-south:a/791f5fb10986423e97aa8512f18b7e65:e415e570-f073-423a-abdc-55de9b58f54e:secret:285d83ce-4a26-c5c3-8e58-48d3140a2415",
        "id": "285d83ce-4a26-c5c3-8e58-48d3140a2415",
        "labels": [],
        "last_update_date": "2020-10-06T03:54:26Z",
        "name": "another-test-arbitrary-secret",
        "secret_group_id": "9ab2250f-a369-4e07-ade7-d417d63ad587",
        "secret_type": "ARBITRARY",
        "state": 1,
        "state_description": "Active"
        }
    ],
    "secrets_total": 2
    },
    "warnings": null
}
```
{: screen}

If the secrets belong to a secret group, the `data.secrets.secret_group_id` value is included in the response to identify the secret group assignment.

### Get a secret
{: #vault-cli-get-static-secret}

Use the following commands to retrieve a secret and its details. Allowable values for **`SECRET_TYPE`** include: `arbitrary`, `username_password`, `imported_cert`

Get a secret that is in the `default` secret group.
```sh
vault read [-format=FORMAT] ibmcloud/SECRET_TYPE/secrets/SECRET_ID
```

Get a secret that's assigned to a specified secret group.
```sh
vault read [-format=FORMAT] ibmcloud/SECRET_TYPE/secrets/groups/SECRET_GROUP_ID/SECRET_ID
```

#### Prerequisites
{: #vault-cli-get-static-secret-prereqs}

You need the [**SecretsReader** or **Writer** service role](/docs/secrets-manager?topic=secrets-manager-iam) to retrieve secrets.

#### Command options
{: #vault-cli-get-static-secret-options}

-format
:   Prints the output in the format that you specify. Valid formats are `table`, `json`, and `yaml`. The default is `table`. You can also set the output format by using the `VAULT_FORMAT` environment variable.

#### Examples
{: #vault-cli-get-static-secret-examples}

Retrieve an arbitrary secret, including its payload, that's assigned to the `default` secret group.

```sh
vault read -format=json ibmcloud/arbitrary/secrets/71539dff-9e84-804a-debb-ab3eb3d8afce
```
{: pre}

#### Output
{: #vault-cli-get-static-secret-output}

The command returns the following output:
```json
{
    "request_id": "025df8ac-b926-6153-3f5b-cd2364b5f85e",
    "lease_id": "",
    "lease_duration": 0,
    "renewable": false,
    "data": {
        "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
    "creation_date": "2020-10-20T16:55:41Z",
    "crn": "crn:v1:bluemix:public:secrets-manager:us-south:a/791f5fb10986423e97aa8512f18b7e65:e415e570-f073-423a-abdc-55de9b58f54e:secret:71539dff-9e84-804a-debb-ab3eb3d8afce",
    "id": "71539dff-9e84-804a-debb-ab3eb3d8afce",
    "labels": [],
    "last_update_date": "2020-10-20T16:55:41Z",
    "name": "my-test-arbitrary-secret",
    "secret_data": {
        "payload": "This is the data for my secret."
    },
    "secret_type": "ARBITRARY",
    "state": 1,
    "state_description": "Active",
    "versions": [
        {
        "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
        "creation_date": "2020-10-20T16:55:41Z",
        "id": "cc2c795e-0072-8074-9824-b6efd5050232"
        }
    ]
    },
    "warnings": null
}
```
{: screen}

### Update a secret
{: #vault-cli-update-static-secret}

Use this command to update the metadata of a secret, such as its name or description. Allowable values for **`SECRET_TYPE`** include: `arbitrary`, `username_password`, `imported_cert`

```sh
vault write [-format=FORMAT] ibmcloud/SECRET_TYPE/secrets/SECRET_ID/metadata name=NAME [description="DESCRIPTION"][expiration_date=EXPIRATION] [labels=LABEL,LABEL]
```
{: codeblock}

#### Prerequisites
{: #vault-cli-update-static-secret-prereqs}

You need the [**Writer** service role](/docs/secrets-manager?topic=secrets-manager-iam) to update secrets.

#### Command options
{: #vault-cli-update-static-secret-options}

name
:   The human-readable alias that you want to assign to the secret.

description
:   An extended description to assign to the secret.

expiration_date
:   The expiration date that you want to assign to the secret. The date format follows [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339).

labels
:   Labels that you can use to group and search for similar secrets in your instance.
-format
:   Prints the output in the format that you specify. Valid formats are `table`, `json`, and `yaml`. The default is `table`. You can also set the output format by using the `VAULT_FORMAT` environment variable.

#### Examples
{: #vault-cli-update-static-secret-examples}

Update the name of an arbitrary secret.

```sh
vault write -format=json ibmcloud/arbitrary/secrets/fe874c2b-e8fd-bbb6-9f19-e91bbe744735/metadata name="updated-name-arbitrary-secret"
```
{: pre}

#### Output
{: #vault-cli-update-static-secret-output}

The command returns the following output:
```json
{
    "request_id": "f361132f-a0e3-eab0-52b8-4d992074b411",
    "lease_id": "",
    "lease_duration": 0,
    "renewable": false,
    "data": {
        "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
    "creation_date": "2020-10-22T14:26:44Z",
    "crn": "crn:v1:bluemix:public:secrets-manager:us-south:a/791f5fb10986423e97aa8512f18b7e65:e415e570-f073-423a-abdc-55de9b58f54e:secret:fe874c2b-e8fd-bbb6-9f19-e91bbe744735",
    "id": "fe874c2b-e8fd-bbb6-9f19-e91bbe744735",
    "labels": [],
    "last_update_date": "2020-10-22T14:54:25Z",
    "name": "updated-name-arbitrary-secret",
    "secret_type": "ARBITRARY",
    "state": 1,
    "state_description": "Active"
    },
    "warnings": null
}
```
{: screen}

### Rotate a secret
{: #vault-cli-rotate-static-secret}

Use this command to rotate a secret. Allowable values for **`SECRET_TYPE`** include: `arbitrary`, `username_password`, `imported_cert`

```sh
vault write [-format=FORMAT] [-force] ibmcloud/SECRET_TYPE/secrets/SECRET_ID/rotate [payload="SECRET_DATA"] [password=PASSWORD] [certificate=CERTIFICATE_DATA] [private_key=PRIVATE_KEY_DATA] [intermediate=INTERMEDIATE_CERTIFICATE_DATA]
```
{: codeblock}


#### Prerequisites
{: #vault-cli-rotate-static-secret-prereqs}

You need the [**Writer** service role](/docs/secrets-manager?topic=secrets-manager-iam) to rotate secrets.

#### Command options
{: #vault-cli-rotate-static-secret-options}

payload
:   The new data to store for an `arbitrary` secret. Only text-based payloads are supported. If you need to store a binary file, be sure to base64 encode it before saving it to {{site.data.keyword.secrets-manager_short}}. For more information, see [Examples](#vault-cli-create-static-secret-examples).

password
:   The new password to assign to a `username_password` secret.

certificate
:   The new certificate data to store for an `imported_cert` secret. Supported file type is `.pem`.

private_key
:   The new private key data to store for an `imported_cert` secret. Supported file type is `.pem`.

intermediate
:   The new intermediate certificate data to store for an `imported_cert` secret. Supported file type is `.pem`.

-format
:   Prints the output in the format that you specify. Valid formats are `table`, `json`, and `yaml`. The default is `table`. You can also set the output format by using the `VAULT_FORMAT` environment variable.

-force
:   Replaces the password that is stored for a `username_password` secret with a randomly generated, 32-character password that contains uppercase letters, lowercase letters, digits, and symbols.


#### Examples
{: #vault-cli-rotate-static-secret-examples}

Manually rotate the secret data that is stored for an arbitrary secret.

```sh
vault write -format=json ibmcloud/arbitrary/secrets/fe874c2b-e8fd-bbb6-9f19-e91bbe744735/rotate payload="Updated secret data."
```
{: pre}

Manually rotate the password that is stored for a `username_password` secret.

```sh
vault write -format=json ibmcloud/username_password/secrets/cb32abc1-2a4b-e0fd-f403-233e5249e130/rotate password="my-updated-password"
```
{: pre}

Replace the password that is stored for a `username_password` secret with a randomly generated 32-character password.

```sh
vault write -format=json -force ibmcloud/username_password/secrets/cb32abc1-2a4b-e0fd-f403-233e5249e130/rotate
```
{: pre}

#### Output
{: #vault-cli-rotate-static-secret-output}

The command to manually rotate a `username_password` secret with a user-provided password returns the following output:
```json
{
    "request_id": "9cb258e5-fbc9-7a37-f8c9-c5ab1dd7b823",
    "lease_id": "",
    "lease_duration": 0,
    "renewable": false,
    "data": {
        "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
    "creation_date": "2020-10-22T15:09:19Z",
    "crn": "crn:v1:bluemix:public:secrets-manager:us-south:a/791f5fb10986423e97aa8512f18b7e65:e415e570-f073-423a-abdc-55de9b58f54e:secret:cb32abc1-2a4b-e0fd-f403-233e5249e130",
    "id": "cb32abc1-2a4b-e0fd-f403-233e5249e130",
    "labels": [],
    "last_update_date": "2020-10-22T15:10:34Z",
    "name": "new-username-password",
    "secret_data": {
        "password": "my-updated-password",
        "username": "my-username"
    },
    "secret_type": "USERNAME_PASSWORD",
    "state": 1,
    "state_description": "Active"
    },
    "warnings": null
}
```
{: screen}

The command to manually rotate a `username_password` secret with a service-generated password returns the following output:
```json
{
    "request_id": "67992946-3fd7-8cbe-9464-f5bc0cc8254e",
    "lease_id": "",
    "lease_duration": 0,
    "renewable": false,
    "data": {
        "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
    "creation_date": "2020-10-22T15:09:19Z",
    "crn": "crn:v1:bluemix:public:secrets-manager:us-south:a/791f5fb10986423e97aa8512f18b7e65:e415e570-f073-423a-abdc-55de9b58f54e:secret:cb32abc1-2a4b-e0fd-f403-233e5249e130",
    "id": "cb32abc1-2a4b-e0fd-f403-233e5249e130",
    "labels": [],
    "last_update_date": "2020-10-22T16:25:55Z",
    "name": "new-username-password",
    "secret_data": {
        "password": "TYRodi/HX7s095UpQ38)L1z(t4\u003ccG6!2",
        "username": "my-username"
    },
    "secret_type": "USERNAME_PASSWORD",
    "state": 1,
    "state_description": "Active"
    },
    "warnings": null
}
```
{: screen}

### Delete a secret
{: #vault-cli-delete-static-secret}

Use this command to delete a secret. Allowable values for **`SECRET_TYPE`** include: `arbitrary`, `username_password`, `imported_cert`

Delete a secret in the `default` secret group.

```sh
vault delete ibmcloud/SECRET_TYPE/secrets/SECRET_ID
```
{: codeblock}

Delete a secret in an existing secret group.

```sh
vault delete ibmcloud/SECRET_TYPE/secrets/groups/SECRET_GROUP_ID/SECRET_ID
```
{: codeblock}

#### Prerequisites
{: #vault-cli-delete-static-secret-prereqs}

You need the [**Manager** service role](/docs/secrets-manager?topic=secrets-manager-iam) to delete secrets.

#### Command options
{: #vault-cli-delete-static-secret-options}

-format
:   Prints the output in the format that you specify. Valid formats are `table`, `json`, and `yaml`. The default is `table`. You can also set the output format by using the `VAULT_FORMAT` environment variable.


#### Examples
{: #vault-cli-delete-static-secret-examples}

Delete an arbitrary secret by its assigned ID.

```sh
vault delete ibmcloud/arbitrary/secrets/d26702aa-77ae-400e-4f25-9790a9cabf9c
```
{: pre}

#### Output
{: #vault-cli-delete-static-secret-output}

The command returns the following output:
```plaintext
Success! Data deleted (if it existed) at: ibmcloud/arbitrary/secrets/d26702aa-77ae-400e-4f25-9790a9cabf9c
```
{: screen}

## Dynamic secrets
{: #vault-cli-dynamic-secrets}

Dynamic secrets are single-use credentials that are generated only when they are read or accessed.

To create a dynamic secret by using the Vault CLI, use the `role` command to scope the secret with the wanted level of permissions in your {{site.data.keyword.cloud_notm}} account. Then, use the `creds` command to generate credentials for the role.

### Create a role
{: #vault-cli-create-role}

Use the following commands to register a role for a [secrets engine that supports dynamic secrets](/docs/secrets-manager?topic=secrets-manager-what-is-secret#secret-types). After you create a role, you can [generate credentials](/docs/secrets-manager?topic=secrets-manager-vault-cli#vault-cli-create-iam-creds-for-role) for it. The configuration that you define for role, such as its name, lease duration, and access permissions, is inherited by the generated credentials.


Create a role in the `default` secret group.
```sh
vault write [-format=FORMAT] ibmcloud/SECRET_TYPE/roles/ROLE_NAME access_groups=ACCESS_GROUP_ID,ACCESS_GROUP_ID ttl=LEASE_DURATION [description="DESCRIPTION"] [labels=LABEL,LABEL]
```
{: codeblock}

Create a role in a specified secret group.
```sh
vault write [-format=FORMAT] ibmcloud/SECRET_TYPE/roles/groups/SECRET_GROUP_ID/ROLE_NAME access_groups=ACCESS_GROUP_ID,ACCESS_GROUP_ID ttl=LEASE_DURATION [description="DESCRIPTION"] [labels=LABEL,LABEL]
```
{: codeblock}

#### Prerequisites
{: #vault-cli-create-role-prereqs}

You need the [**Writer** service role](/docs/secrets-manager?topic=secrets-manager-iam) to create secrets.

#### Command options
{: #vault-cli-create-role-options}

SECRET_TYPE
:   The type of secret that you want to create. Currently, `iam_credentials` is supported.

SECRET_GROUP_ID
:   The ID of the secret group that you want to assign to the role and its credentials.

ROLE_NAME
:   The human-readable alias that you want to assign to the role and its credentials.

access_groups
:   The ID of the access group that determines the scope of access to assign to the role and its credentials.

ttl
:   The time-to-live (TTL) that determines how long a role's generated credentials can exist. Use a duration string such as `300s` or `1h30m`. Valid time units are `s`, `m`, and `h`.

description
:   An extended description to assign to the role and its credentials.

labels
:   Labels that you can use to group and search for similar secrets in your instance.

-format
:   Prints the output in the format that you specify. Valid formats are `table`, `json`, and `yaml`. The default is `table`. You can also set the output format by using the `VAULT_FORMAT` environment variable.


#### Examples
{: #vault-cli-create-role-examples}

Configure an IAM credential with a lease duration of 1 hour and assign it to the `default` secret group.

```sh
vault write -format=json ibmcloud/iam_credentials/roles/test-iam-credentials access_groups=AccessGroupId-985dc0a3-857b-48bd-b6d6-33819da7ba42 ttl=1h description="My test IAM credential." labels=test,us-south
```
{: pre}

Configure an IAM credential with a lease duration of 1 hour and assign it to a specified secret group.

```sh
vault write -format=json ibmcloud/iam_credentials/roles/groups/9ab2250f-a369-4e07-ade7-d417d63ad587/test-iam-credential-in-group access_groups=AccessGroupId-985dc0a3-857b-48bd-b6d6-33819da7ba42 ttl=1h description="My test IAM credential that is assigned to a secret group." labels=test,us-south
```
{: pre}

#### Output
{: #vault-cli-create-role-output}

The command returns the following output:
```json
{
    "request_id": "d4150a28-1184-8864-dcd3-15b0d18da7c1",
    "lease_id": "",
    "lease_duration": 0,
    "renewable": false,
    "data": {
        "access_groups": [
        "AccessGroupId-985dc0a3-857b-48bd-b6d6-33819da7ba42"
    ],
    "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
    "creation_date": "2020-10-09T17:13:47Z",
    "crn": "crn:v1:bluemix:public:secrets-manager:us-south:a/791f5fb10986423e97aa8512f18b7e65:e415e570-f073-423a-abdc-55de9b58f54e::",
    "description": "My test IAM credential that is assigned to a secret group.",
    "id": "091ca93f-5c99-4078-9d7e-4801143030fd",
    "labels": [
        "test",
        "us-south"
    ],
    "last_update_date": "2020-10-09T17:13:47Z",
    "name": "test-iam-credential-in-group",
    "secret_group_id": "9ab2250f-a369-4e07-ade7-d417d63ad587",
    "secret_type": "IAM_CREDENTIALS",
    "state": 1,
    "state_description": "Active",
    "ttl": 3600
    },
    "warnings": null
}
```
{: screen}

If the role is created in a secret group, the `data.secret_group_id` value is included in the response to identify the secret group assignment.

### Generate IAM credentials
{: #vault-cli-create-iam-creds-for-role}

Use the following commands to generate an API key for a role. This command creates a service ID, adds the service ID to the access group that you configured for the role, and then generates an API key for the service ID.

Generate an API key for a role in the `default` secret group.
```sh
vault read [-format=FORMAT] ibmcloud/iam_credentials/creds/ROLE_ID
```
{: codeblock}

Generate an API key for a role in a specified secret group.
```sh
vault read [-format=FORMAT] ibmcloud/iam_credentials/creds/groups/SECRET_GROUP_ID/ROLE_ID
```
{: codeblock}

The generated API keys are renewable and have a time-to-live (TTL) as defined by the role or the system default.

#### Prerequisites
{: #vault-cli-create-iam-creds-for-role-prereqs}

You need the [**Writer** service role](/docs/secrets-manager?topic=secrets-manager-iam) to create secrets.

#### Command options
{: #vault-cli-create-iam-creds-for-role-options}

SECRET_GROUP_ID
:   The ID of the secret group that you want to assign to this secret.

ROLE_ID
:   The ID or name that is assigned to the role for this secret.

-format
:   Prints the output in the format that you specify. Valid formats are `table`, `json`, and `yaml`. The default is `table`. You can also set the output format by using the `VAULT_FORMAT` environment variable.


#### Examples
{: #vault-cli-create-iam-creds-for-role-examples}

Generate an IAM credential for a secret that is assigned to the `default` secret group.

```sh
vault read -format=json ibmcloud/iam_credentials/creds/test-iam-credentials
```
{: pre}

Generate an IAM credential for a secret that is assigned to a specified secret group.

```sh
vault read -format=json ibmcloud/iam_credentials/creds/groups/9ab2250f-a369-4e07-ade7-d417d63ad587/test-iam-credential-in-group
```
{: pre}

#### Output
{: #vault-cli-create-iam-creds-for-role-output}

The command returns the following output:
```json
{
    "request_id": "48d14d52-ce92-6efc-aeaa-b49cc11eabd6",
    "lease_id": "",
    "lease_duration": 0,
    "renewable": false,
    "data": {
        "access_groups": [
        "AccessGroupId-985dc0a3-857b-48bd-b6d6-33819da7ba42"
    ],
    "api_key": "Cg7l3kJveurEry_P7_fLPIBR....(truncated)",
    "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
    "creation_date": "2020-10-09T17:13:47Z",
    "crn": "crn:v1:bluemix:public:secrets-manager:us-south:a/791f5fb10986423e97aa8512f18b7e65:e415e570-f073-423a-abdc-55de9b58f54e::",
    "description": "My test IAM credential that is assigned to a secret group.",
    "id": "091ca93f-5c99-4078-9d7e-4801143030fd",
    "labels": [
        "test",
        "us-south"
    ],
    "last_update_date": "2020-10-09T17:53:23Z",
    "name": "test-iam-credential-in-group",
    "secret_group_id": "9ab2250f-a369-4e07-ade7-d417d63ad587",
    "secret_type": "IAM_CREDENTIALS",
    "service_id": "ServiceId-ae06783c-0ab4-4b02-a78a-8dff7d0634c6",
    "state": 1,
    "state_description": "Active",
    "ttl": 3600
    },
    "warnings": null
}
```
{: screen}

If the role belongs to a secret group, the `data.secret_group_id` value is included in the response to identify the secret group assignment.

### List roles
{: #vault-cli-list-roles}

Use the following commands to list the roles or secrets in your {{site.data.keyword.secrets-manager_short}} instance.

List roles by type.
```sh
vault read [-format=FORMAT] ibmcloud/SECRET_TYPE/roles
```
{: codeblock}

List roles by secret group ID.
```sh
vault read [-format=FORMAT] ibmcloud/SECRET_TYPE/roles/groups/SECRET_GROUP_ID
```
{: codeblock}

#### Prerequisites
{: #vault-cli-list-roles-prereqs}

You need the [**Reader** service role](/docs/secrets-manager?topic=secrets-manager-iam) to list secrets.

#### Command options
{: #vault-cli-list-roles-options}

SECRET_TYPE
:   The type of secret that you want to list. Currently, `iam_credentials` is supported.

SECRET_GROUP_ID
:   The ID of the secret group.

-format
:   Prints the output in the format that you specify. Valid formats are `table`, `json`, and `yaml`. The default is `table`. You can also set the output format by using the `VAULT_FORMAT` environment variable.


#### Examples
{: #vault-cli-list-roles-examples}

Retrieve a list of IAM credentials that are assigned to the `default` secret group.

```sh
vault read -format=json ibmcloud/iam_credentials/roles
```
{: pre}

Retrieve a list of IAM credentials that belong to a specified secret group.

```sh
vault read -format=json ibmcloud/iam_credentials/roles/groups/9ab2250f-a369-4e07-ade7-d417d63ad587
```
{: pre}

#### Output
{: #vault-cli-list-dynamic-secrets-output}

The command returns the following output:
```json
{
    "request_id": "d567207f-b5e6-fc35-086e-fbc465bf3678",
    "lease_id": "",
    "lease_duration": 0,
    "renewable": false,
    "data": {
        "roles": [
        {
        "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
        "creation_date": "2020-10-09T17:13:47Z",
        "crn": "crn:v1:bluemix:public:secrets-manager:us-south:a/791f5fb10986423e97aa8512f18b7e65:e415e570-f073-423a-abdc-55de9b58f54e::",
        "description": "My test IAM credential that is assigned to a secret group.",
        "id": "091ca93f-5c99-4078-9d7e-4801143030fd",
        "labels": [
          "test",
          "us-south"
        ],
        "last_update_date": "2020-10-09T17:13:47Z",
        "name": "test-iam-credential-in-group",
        "secret_group_id": "9ab2250f-a369-4e07-ade7-d417d63ad587",
        "secret_type": "IAM_CREDENTIALS",
        "state": 1,
        "state_description": "Active",
        "ttl": 3600
        },
        {
        "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
        "creation_date": "2020-10-09T17:05:21Z",
        "crn": "crn:v1:bluemix:public:secrets-manager:us-south:a/791f5fb10986423e97aa8512f18b7e65:e415e570-f073-423a-abdc-55de9b58f54e:secret:a810998d-2912-4865-b4da-8dcc7465d784",
        "description": "My test IAM credential.",
        "id": "a810998d-2912-4865-b4da-8dcc7465d784",
        "labels": [
          "test",
          "us-south"
        ],
        "last_update_date": "2020-10-09T17:05:21Z",
        "name": "test-iam-credentials",
        "secret_type": "IAM_CREDENTIALS",
        "state": 1,
        "state_description": "Active",
        "ttl": 3600
        }
    ],
    "roles_total": 2
    },
    "warnings": null
}
```
{: screen}

If the role belongs to a secret group, the `roles.data.secret_group_id` value is included in the response to identify the secret group assignment.

### Read the metadata of a role
{: #vault-cli-read-role-metadata}

Use the following commands to view details about a registered role or secret, such as its name and history.

View the details of a role in the `default` secret group.

```sh
vault read [-format=FORMAT] ibmcloud/SECRET_TYPE/roles/ROLE_ID/metadata
```
{: codeblock}

View the details of a role that's assigned to a secret group.
```sh
vault read [-format=FORMAT] ibmcloud/SECRET_TYPE/roles/groups/GROUP_ID/ROLE/metadata
```
{: codeblock}

#### Prerequisites
{: #vault-cli-read-role-metadata-prereqs}

You need the [**Reader** service role](/docs/secrets-manager?topic=secrets-manager-iam) to view the metadata of a secret.

#### Command options
{: #vault-cli-read-role-metadata-options}

SECRET_TYPE
:   The type of secret that you want to view. Currently, `iam_credentials` is supported.

SECRET_GROUP_ID
:   The ID of the secret group that is assigned to the role and its credentials

ROLE_ID
:   The ID that is assigned to the role for this secret.

-format
:   Prints the output in the format that you specify. Valid formats are `table`, `json`, and `yaml`. The default is `table`. You can also set the output format by using the `VAULT_FORMAT` environment variable.


#### Examples
{: #vault-cli-read-role-metadata-examples}

View the details of a role that's assigned to a secret group.

```sh
vault read -format=json ibmcloud/iam_credentials/roles/groups/9ab2250f-a369-4e07-ade7-d417d63ad587/091ca93f-5c99-4078-9d7e-4801143030fd/metadata
```
{: pre}

#### Output
{: #vault-cli-read-role-metadata-output}

The command returns the following output:
```json
{
    "request_id": "cb4672e2-51d9-3a83-f8a2-717afe16e24a",
    "lease_id": "",
    "lease_duration": 0,
    "renewable": false,
    "data": {
        "access_groups": [
        "AccessGroupId-985dc0a3-857b-48bd-b6d6-33819da7ba42"
    ],
    "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
    "creation_date": "2020-10-09T17:13:47Z",
    "crn": "crn:v1:bluemix:public:secrets-manager:us-south:a/791f5fb10986423e97aa8512f18b7e65:e415e570-f073-423a-abdc-55de9b58f54e::",
    "description": "My test IAM credential that is assigned to a secret group.",
    "id": "091ca93f-5c99-4078-9d7e-4801143030fd",
    "labels": [
        "test",
        "us-south"
    ],
    "last_update_date": "2020-10-09T17:13:47Z",
    "name": "test-iam-credential-in-group",
    "secret_group_id": "9ab2250f-a369-4e07-ade7-d417d63ad587",
    "secret_type": "IAM_CREDENTIALS",
    "state": 1,
    "state_description": "Active",
    "ttl": 3600
    },
    "warnings": null
}
```
{: screen}

### Update the metadata of a role
{: #vault-cli-update-role-metadata}

Use the following commands to view details about a registered role or secret, such as its name and history.

Update the details of a role in the `default` secret group.

```sh
vault write [-format=FORMAT] ibmcloud/SECRET_TYPE/roles/ROLE_ID/metadata [name="ROLE_NAME"] [access_groups=ACCESS_GROUP_ID,ACCESS_GROUP_ID] [ttl=LEASE_DURATION] [description="DESCRIPTION"] [labels=LABEL,LABEL]
```
{: codeblock}

Update the details of a role that's assigned to a secret group.
```sh
vault write [-format=FORMAT] ibmcloud/SECRET_TYPE/roles/groups/SECRET_GROUP_ID/ROLE_ID/metadata [name="ROLE_NAME"] [access_groups=ACCESS_GROUP_ID,ACCESS_GROUP_ID] [ttl=LEASE_DURATION] [description="DESCRIPTION"] [labels=LABEL,LABEL]
```
{: codeblock}

#### Prerequisites
{: #vault-cli-update-role-metadata-prereqs}

You need the [**Writer** service role](/docs/secrets-manager?topic=secrets-manager-iam) to update the metadata of a secret.

#### Command options
{: #vault-cli-update-role-metadata-options}

SECRET_TYPE
:   The type of secret that you want to update. Currently, `iam_credentials` is supported.

SECRET_GROUP_ID
:   The ID of the secret group that is assigned to the role and its credentials.

ROLE_ID
:   The ID that assigned to this secret.

access_groups
:   The ID of the access group that determines the scope of access to assign to the role and its credentials.

ttl
:   The time-to-live (TTL) that determines how long a role's generated credentials can exist. Use a duration string such as `300s` or `1h30m`. Valid time units are `s`, `m`, and `h`.

name
:   The new name that you want to assign for this secret.

description
:   An extended description to assign to the role and its credentials.

labels
:   Labels that you can use to group and search for similar secrets in your instance.

-format
:   Prints the output in the format that you specify. Valid formats are `table`, `json`, and `yaml`. The default is `table`. You can also set the output format by using the `VAULT_FORMAT` environment variable.


#### Examples
{: #vault-cli-update-role-metadata-examples}

Update the details of a role that's assigned to the `default` group.

```sh
vault write -format=json ibmcloud/iam_credentials/roles/091ca93f-5c99-4078-9d7e-4801143030fd/metadata name="new-credential-name"
```
{: pre}

#### Output
{: #vault-cli-update-role-metadata-output}

The command returns the following output:
```json
{
    "request_id": "cb4672e2-51d9-3a83-f8a2-717afe16e24a",
    "lease_id": "",
    "lease_duration": 0,
    "renewable": false,
    "data": {
        "access_groups": [
        "AccessGroupId-985dc0a3-857b-48bd-b6d6-33819da7ba42"
    ],
    "created_by": "iam-ServiceId-b7ebcf90-c7a9-495b-8ce8-bbf33cb95ca0",
    "creation_date": "2020-10-09T17:13:47Z",
    "crn": "crn:v1:bluemix:public:secrets-manager:us-south:a/791f5fb10986423e97aa8512f18b7e65:e415e570-f073-423a-abdc-55de9b58f54e::",
    "description": "My test IAM credential.",
    "id": "091ca93f-5c99-4078-9d7e-4801143030fd",
    "labels": [
        "test",
        "us-south"
    ],
    "last_update_date": "2020-10-09T17:13:47Z",
    "name": "new-credential-name",
    "secret_type": "IAM_CREDENTIALS",
    "state": 1,
    "state_description": "Active",
    "ttl": 3600
    },
    "warnings": null
}
```
{: screen}

### Delete a role
{: #vault-cli-delete-role}

Use the following commands to delete a role.

Delete a role in the `default` secret group.

```sh
vault delete ibmcloud/SECRET_TYPE/roles/ROLE_ID
```
{: codeblock}

Delete a role that's assigned to a secret group.
```sh
vault delete ibmcloud/SECRET_TYPE/roles/groups/SECRET_GROUP_ID/ROLE_ID
```
{: codeblock}

#### Prerequisites
{: #vault-cli-delete-role-prereqs}

You need the [**Writer** service role](/docs/secrets-manager?topic=secrets-manager-iam) to update the metadata of a secret.

#### Command options
{: #vault-cli-delete-role-options}

SECRET_TYPE
:   The type of secret that you want to delete. Currently, `iam_credentials` is supported.

SECRET_GROUP_ID
:   The ID of the secret group that is assigned to the role and its credentials.

ROLE_ID
:   The ID that assigned to this secret.


#### Examples
{: #vault-cli-delete-role-examples}

Delete a role that's assigned to the `default` group.

```sh
vault delete ibmcloud/iam_credentials/roles/091ca93f-5c99-4078-9d7e-4801143030fd
```
{: pre}

#### Output
{: #vault-cli-delete-role-output}

The command returns the following output:
```plaintext
Success! Data deleted (if it existed) at: ibmcloud/iam_credentials/roles/091ca93f-5c99-4078-9d7e-4801143030fd
```
{: screen}



