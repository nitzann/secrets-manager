---

copyright:
  years: 2020, 2022
lastupdated: "2022-02-07"

keywords: rotate, manually rotate, renew, reimport, reorder, manual rotation

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

# Manually rotating secrets
{: #manual-rotation}

With {{site.data.keyword.secrets-manager_full}}, you can manually create new versions of a secret by using the UI or APIs.
{: shortdesc}

When you rotate a secret in your service instance, you create a new version of its value. Rotating your credentials limits how long a protected resource can be accessed by a single secret, which can protect your business against the risks that are associated with compromised credentials. Rotate your secrets regularly, for example every 30 or 60 days, so that you're always meeting best practices around secrets management.


## Before you begin
{: #before-manual-rotate}

Before you get started, be sure that you have the required level of access. To rotate secrets, you need the [**Writer** service role or higher](/docs/secrets-manager?topic=secrets-manager-iam).

### Supported secret types
{: #manual-rotate-by-type}

All of the secrets that you store in {{site.data.keyword.secrets-manager_short}} can be rotated and replaced on-demand. The way in which {{site.data.keyword.secrets-manager_short}} evaluates a request to rotate a secret depends on the secret type.

| Type | Rotation description |
| --- | --- |
| [Arbitrary secrets](/docs/secrets-manager?topic=secrets-manager-arbitrary-secrets) | Arbitrary secrets are immediately replaced with the data that you provide on rotation. |
| [IAM credentials](/docs/secrets-manager?topic=secrets-manager-iam-credentials) |IAM credentials, which consist of a service ID and API key, are immediately regenerated according to their initial configuration. If the IAM credentials secret was created by using an existing service ID in the account, only the API key is regenerated as part of a manual rotation. In contrast, if the secret was created by selecting an access group, both the service ID and API key values are regenerated when they're manually rotated.<p class="important">The **Reuse IAM credentials until lease expires** (`reuse_api_key`) option for an IAM credentials secret impacts whether it can be rotated manually. If this field is `false` or set to **Off** in the UI, manual rotation isn't supported. The API key that is dynamically generated for the secret on each read is already a single-use, ephemeral value.</p>|
| [Imported certificates](/docs/secrets-manager?topic=secrets-manager-certificates#import-certificates) | Certificates that were initially imported to a service instance are immediately replaced with the data that you reimport on rotation. |
| [Public certificates](/docs/secrets-manager?topic=secrets-manager-certificates#order-certificates) | Public certificates move to the **Active, Rotation pending** status to indicate that a request to rotate a certificate is being processed. {{site.data.keyword.secrets-manager_short}} sends the request to the configured certificate authority (CA), for example Let's Encrypt, to validate the ownership of your domains. If the validation completes successfully, a new certificate is issued. |
| [User credentials](/docs/secrets-manager?topic=secrets-manager-user-credentials) | Passwords that are associated with user credentials secrets are immediately replaced with the data that you provide on rotation. |
{: caption="Table 1. Describes how {{site.data.keyword.secrets-manager_short}} evaluates manual rotation by secret type" caption-side="top"}

## Manually rotating secrets in the UI
{: #manual-rotate-ui}
{: ui}

You can manually rotate your secrets and certificates by using the {{site.data.keyword.secrets-manager_short}} UI.

### Rotating arbitrary secrets
{: #manual-rotate-arbitrary-ui}
{: ui}

You can use the {{site.data.keyword.secrets-manager_short}} UI to manually rotate your arbitrary secrets.

1. In the console, click the **Menu** icon ![Menu icon](../icons/icon_hamburger.svg) **> Resource List**.
2. From the list of services, select your instance of {{site.data.keyword.secrets-manager_short}}.
3. In the {{site.data.keyword.secrets-manager_short}} UI, go to the **Secrets** list.
4. In the row for the secret that you want to rotate, click the **Actions** menu ![Actions icon](../icons/actions-icon-vertical.svg) **> Rotate**.
5. Select a new file or enter a new secret value.
6. To rotate the secret immediately, click **Rotate**.
7. Optional: Check the version history to view the latest updates.

   In the row of the secret that you rotated, click the **Actions** menu ![Actions icon](../icons/actions-icon-vertical.svg) **> Version history** to verify that a new version was created successfully.


### Rotating user credentials
{: #manual-rotate-user-credentials-ui}
{: ui}

You can use the {{site.data.keyword.secrets-manager_short}} UI to manually rotate the password values that are associated with a user credentials secret.

1. In the console, click the **Menu** icon ![Menu icon](../icons/icon_hamburger.svg) **> Resource List**.
2. From the list of services, select your instance of {{site.data.keyword.secrets-manager_short}}.
3. In the {{site.data.keyword.secrets-manager_short}} UI, go to the **Secrets** list.
4. In the row for the secret that you want to rotate, click the **Actions** menu ![Actions icon](../icons/actions-icon-vertical.svg) **> Rotate**.

5. Determine whether to enter your own password or generate a new one.

    If you choose to generate a new password, {{site.data.keyword.secrets-manager_short}} replaces the existing value with a randomly generated 32-character password that contains uppercase letters, lowercase letters, digits, and symbols.

6. To rotate the secret immediately, click **Rotate**.  
7. Optional: Check the version history to view the latest updates.

   In the row of the secret that you rotated, click the **Actions** menu ![Actions icon](../icons/actions-icon-vertical.svg) **> Version history** to verify that a new version was created successfully.


### Rotating imported certificates
{: #manual-rotate-imported-cert-ui}
{: ui}

When it's time to renew a certificate that was initially imported to the service, you can use the {{site.data.keyword.secrets-manager_short}} UI to manually reimport it. After a certificate is rotated, the previous version is retained in case you need it.

If the certificate that you are rotating was previously imported with an intermediate certificate and a private key, include an intermediate certificate and private key on rotation to avoid service disruptions.
{: important}

1. In the console, click the **Menu** icon ![Menu icon](../icons/icon_hamburger.svg) **> Resource List**.
2. From the list of services, select your instance of {{site.data.keyword.secrets-manager_short}}.
3. In the {{site.data.keyword.secrets-manager_short}} UI, go to the **Secrets** list.
4. In the row for the certificate that you want to rotate, click the **Actions** menu ![Actions icon](../icons/actions-icon-vertical.svg) **> Rotate**.
5. Select or enter the new certificate data.

   Keep in mind that manually rotating a certificate replaces the content of the certificate with the new data that you provide only. Private keys and intermediate certificates from previous versions are not retained. 
6. To rotate the certificate immediately, click **Rotate**.
7. Optional: Check the version history to view the latest updates.

   In the row of the secret that you rotated, click the **Actions** menu ![Actions icon](../icons/actions-icon-vertical.svg) **> Version history** to verify that a new version was created successfully.
8. Redeploy the latest certificate version to your TLS termination point.

   To access the current version, you can [download the certificate](/docs/secrets-manager?topic=secrets-manager-access-secrets) or retrieve it programmatically by using the [Get a secret](/apidocs/secrets-manager#get-secret) API.

### Rotating public certificates
{: #manual-rotate-public-cert-ui}
{: ui}

If your {{site.data.keyword.secrets-manager_short}} service instance is enabled for [public certificates](/docs/secrets-manager?topic=secrets-manager-certificates#order-certificates), you can manually renew a certificate that was previously ordered from a third-party certificate authority.

1. In the console, click the **Menu** icon ![Menu icon](../icons/icon_hamburger.svg) **> Resource List**.
2. From the list of services, select your instance of {{site.data.keyword.secrets-manager_short}}.
3. In the {{site.data.keyword.secrets-manager_short}} UI, go to the **Secrets** list.
4. In the row for the certificate that you want to rotate, click the **Actions** menu ![Actions icon](../icons/actions-icon-vertical.svg) **> Rotate**.
5. Click **Rotate**.

   A success message is displayed to indicate that your order is currently being processed. If the validation completes successfully, a new certificate is issued and the status of the certificate changes from **Active, Rotation pending** back to **Active**. If the validation doesn't complete successfully, the status of the certificate changes to **Active, Rotation failed**.

6. Optional: Check the issuance details of a certificate.

   You can check the issuance details of a public certificate by clicking the **Actions** icon ![Actions icon](../icons/actions-icon-vertical.svg) **> Details**. If there was an issue with the request, the Status field provides information about why the rotation couldn't complete successfully.

7. Redeploy the latest certificate version to your TLS termination point.

   To access the current version, you can [download the certificate](/docs/secrets-manager?topic=secrets-manager-access-secrets) or retrieve it programmatically by using the [Get a secret](/apidocs/secrets-manager#get-secret) API.




## Manually rotating secrets from the CLI
{: #manual-rotate-cli}
{: cli}

You can manually rotate your secrets and certificates by using the {{site.data.keyword.secrets-manager_short}} CLI plug-in.

### Rotating arbitrary secrets
{: #manual-rotate-arbitrary-cli}
{: cli}

To rotate an arbitrary secret by using the {{site.data.keyword.secrets-manager_short}} CLI plug-in, run the [**`ibmcloud secrets-manager secret-update`**](/docs/secrets-manager?topic=secrets-manager-cli-plugin-secrets-manager-cli#secrets-manager-cli-secret-update-command) command. You can specify the type of secret by using the `--secret-type arbitrary` option. For example, the following command rotates a secret and assigns `new-secret-data` as its new version.

```sh
ibmcloud secrets-manager secret-update --action rotate --id SECRET_ID --secret-type arbitrary --body '{"payload": "new-secret-data"}' --output json
```
{: codeblock}

The command outputs the value of the secret, along with other metadata. For more information about the command options, see [**`ibmcloud secrets-manager secret-update`**](/docs/secrets-manager?topic=secrets-manager-cli-plugin-secrets-manager-cli#secrets-manager-cli-secret-update-command).

### Rotating user credentials
{: #manual-rotate-user-credentials-cli}
{: cli}

To rotate a user credential secret using the {{site.data.keyword.secrets-manager_short}} CLI plug-in, run the [**`ibmcloud secrets-manager secret-update`**](/docs/secrets-manager?topic=secrets-manager-cli-plugin-secrets-manager-cli#secrets-manager-cli-secret-update-command) command. You can specify the type of secret by using the `--secret-type username_password` option. For example, the following command rotates a secret and assigns `new-password` as its new version.

```sh
ibmcloud secrets-manager secret-update --action rotate --id SECRET_ID --secret-type username_password --body '{"password": "new-password"}' --output json
```
{: codeblock}

To have the service generate and assign a random password to your credential, you can pass an empty string on the `password` field. For example, `{ "password": ""}`. {{site.data.keyword.secrets-manager_short}} replaces the existing value with a randomly generated 32-character password that contains uppercase letters, lowercase letters, digits, and symbols.
{: tip}

The command outputs the value of the secret, along with other metadata. For more information about the command options, see [**`ibmcloud secrets-manager secret-update`**](/docs/secrets-manager?topic=secrets-manager-cli-plugin-secrets-manager-cli#secrets-manager-cli-secret-update-command).



## Manually rotating secrets with the API
{: #manual-rotate-api}
{: api}

You can manually rotate your secrets and certificates by using the {{site.data.keyword.secrets-manager_short}} API.

### Rotating arbitrary secrets
{: #manual-rotate-arbitrary-api}
{: api}

You can rotate arbitrary secrets by calling the {{site.data.keyword.secrets-manager_short}} API.

The following example request creates a new version of your secret. When you call the API, replace the ID variables and IAM token with the values that are specific to your {{site.data.keyword.secrets-manager_short}} instance.
{: curl}



```bash
curl -X POST "https://{instance_ID}.{region}.secrets-manager.appdomain.cloud/api/v1/secrets/arbitrary/{id}?action=rotate" \
    -H "Authorization: Bearer $IAM_TOKEN" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d '{
        "payload": "new-secret-data"
    }'
```
{: codeblock}
{: curl}



A successful response returns the ID value for the secret, along with other metadata. For more information about the required and optional request parameters, check out the [API docs](/apidocs/secrets-manager#update-secret).

### Rotating user credentials
{: #manual-rotate-user-credentials-api}
{: api}

You can rotate secrets by calling the {{site.data.keyword.secrets-manager_short}} API.

The following example request creates a new version of your secret. When you call the API, replace the ID variables and IAM token with the values that are specific to your {{site.data.keyword.secrets-manager_short}} instance.
{: curl}



```bash
curl -X POST "https://{instance_ID}.{region}.secrets-manager.appdomain.cloud/api/v1/secrets/username_password/{id}?action=rotate" \
    -H "Authorization: Bearer $IAM_TOKEN" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d '{
        "password": "new-password"
    }'
```
{: codeblock}
{: curl}



To have the service generate and assign a random password to your credential, you can pass an empty string on the `password` field. For example, `{ "password": ""}`. {{site.data.keyword.secrets-manager_short}} replaces the existing value with a randomly generated 32-character password that contains uppercase letters, lowercase letters, digits, and symbols.
{: tip}

A successful response returns the ID value for the secret, along with other metadata. For more information about the required and optional request parameters, check out the [API docs](/apidocs/secrets-manager#update-secret).
