---

copyright:
  years: 2018, 2020
lastupdated: "2020-07-09"

keywords: root key, import key, key material, import key api, bring your own key, byok, symmetric key, import symmetric key, upload symmetric key, import root key, upload root key, import key-wrapping key, upload key-wrapping key, import crk

subcollection: hs-crypto
---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:external: target="_blank" .external}

# Importing root keys
{: #import-root-keys}

You can use {{site.data.keyword.cloud}} {{site.data.keyword.hscrypto}} to secure your existing root keys by using the {{site.data.keyword.hscrypto}} GUI, or programmatically with the {{site.data.keyword.hscrypto}} key management API.
{: shortdesc}

Root keys are symmetric key-wrapping keys that are used to protect the security of encrypted data in the cloud. For more information about importing root keys, see [Bringing your encryption keys to the cloud](/docs/hs-crypto?topic=hs-crypto-importing-keys).

<!-- Plan ahead for importing keys by [reviewing your options for creating and encrypting key material](/docs/hs-crypto?topic=hs-crypto-importing-keys#plan-ahead). For added security, you can enable the secure import of the key material by using an [import token](/docs/hs-crypto?topic=hs-crypto-importing-keys#using-import-tokens) to encrypt your key material before you bring it to the cloud.
{: note} -->

## Importing root keys with the GUI
{: #import-root-key-gui}

After you [create an instance of the service](/docs/hs-crypto?topic=hs-crypto-provision), complete the following steps to add your existing root key with the {{site.data.keyword.hscrypto}} GUI.

1. [Log in to the {{site.data.keyword.cloud_notm}} console](https://{DomainName}/){: external}.
2. Go to **Menu** &gt; **Resource List** to view a list of your resources.
3. From your {{site.data.keyword.cloud_notm}} resource list, select your provisioned instance of {{site.data.keyword.hscrypto}}.
4. To import a key, click **Add key** and select **Import a key**.

    Specify the key's details:

    <table>
      <tr>
        <th>Setting</th>
        <th>Description</th>
      </tr>
      <tr>
        <td>Name</td>
        <td>
          <p>A unique, human-readable alias for easy identification of your key.</p>
          <p>To protect your privacy, ensure that the key name does not contain personally identifiable information (PII), such as your name or location.</p>
        </td>
      </tr>
      <tr>
        <td>Key type</td>
        <td>The type of key that you would like to manage in {{site.data.keyword.hscrypto}}. From the list of key types, select <strong><a href="/docs/hs-crypto?topic=hs-crypto-understand-concepts#root-key-concept">Root key</a></strong>.</td>
      </tr>
      <tr>
        <td>Key material</td>
        <td>
          <p>The base64 encoded key material, such as an existing key-wrapping key, that you want to store and manage in the service.</p>
          <p>Ensure that the key material meets the following requirements:</p>
          <p>
            <ul>
              <li>The key must be 128, 192, or 256 bits.</li>
              <li>The bytes of data, for example 32 bytes for 256 bits, must be encoded by using base64 encoding.</li>
            </ul>
          </p>
        </td>
      </tr>
      <caption style="caption-side:bottom;">Table 1. Describes the <strong>Import a key</strong> settings</caption>
    </table>

5. When you are finished filling out the key's details, click **Import key** to confirm.

## Importing root keys with the API
{: #import-root-key-api}

Import symmetric keys to {{site.data.keyword.hscrypto}} by making a `POST` call to the following endpoint.

```
https://api.<region>.hs-crypto.cloud.ibm.com:<port>/api/v2/keys
```
{: codeblock}

1. [Retrieve your service and authentication credentials to work with keys in the service](/docs/hs-crypto?topic=hs-crypto-set-up-kms-api).

2. Call the [{{site.data.keyword.hscrypto}} key management API](https://{DomainName}/apidocs/hs-crypto){: external} with the following cURL command.:

    ```cURL
    curl -X POST \
      https://api.<region>.hs-crypto.cloud.ibm.com:<port>/api/v2/keys \
      -H 'authorization: Bearer <IAM_token>' \
      -H 'bluemix-instance: <instance_ID>' \
      -H 'content-type: application/vnd.ibm.kms.key+json' \
      -d '{
     "metadata": {
       "collectionType": "application/vnd.ibm.kms.key+json",
       "collectionTotal": 1
     },
     "resources": [
       {
       "type": "application/vnd.ibm.kms.key+json",
       "name": "<key_alias>",
       "description": "<key_description>",
       "expirationDate": "<YYYY-MM-DDTHH:MM:SS.SSZ>",
       "payload": "<key_material>",
       "extractable": <key_type>
       }
     ]
    }'
    ```
    {: codeblock}

    Replace the variables in the example request according to the following table.

      <table>
        <tr>
          <th>Variable</th>
          <th>Description</th>
        </tr>
        <tr>
          <td><varname>region</varname></td>
          <td><strong>Required.</strong> The region abbreviation, such as <code>us-south</code> or <code>au-syd</code>, that represents the geographic area where your {{site.data.keyword.hscrypto}} instance resides. For more information, see <a href="/docs/hs-crypto?topic=hs-crypto-regions#service-endpoints">Regional service endpoints</a>.</td>
        </tr>
        <tr>
          <td><varname>IAM_token</varname></td>
          <td><strong>Required.</strong> Your {{site.data.keyword.cloud_notm}} access token. Include the full contents of the <code>IAM</code> token, including the Bearer value, in the cURL request. For more information, see <a href="/docs/hs-crypto?topic=hs-crypto-retrieve-access-token">Retrieving an access token</a>.</td>
        </tr>
        <tr>
          <td><varname>instance_ID</varname></td>
          <td><strong>Required.</strong> The unique identifier that is assigned to your {{site.data.keyword.hscrypto}} instance. For more information, see <a href="/docs/hs-crypto?topic=hs-crypto-retrieve-instance-ID">Retrieving your instance ID</a>.</td>
        </tr>
        <tr>
          <td><varname>correlation_ID</varname></td>
          <td>The unique identifier that is used to track and correlate transactions.</td>
        </tr>
        <tr>
          <td><varname>key_alias</varname></td>
          <td><strong>Required.</strong> A unique, human-readable name for easy identification of your key. To protect your privacy, do not store your personal data as metadata for your key.</td>
        </tr>
        <tr>
          <td><varname>key_description</varname></td>
          <td>An extended description of your key. To protect your privacy, do not store your personal data as metadata for your key.</td>
        </tr>
        <tr>
          <td><varname>YYYY-MM-DD</varname><br><varname>HH:MM:SS.SS</varname></td>
          <td>The date and time that the key expires in the system, in RFC 3339 format. If the <code>expirationDate</code> attribute is omitted, the key does not expire.</td>
        </tr>
        <tr>
          <td><varname>key_material</varname></td>
          <td>
            <p>The base64 encoded key material, such as an existing key-wrapping key, that you want to store and manage in the service.</p>
            <p>Ensure that the key material meets the following requirements:</p>
            <p>
              <ul>
                <li>The key must be 128, 192, or 256 bits.</li>
                <li>The bytes of data, for example 32 bytes for 256 bits, must be encoded by using base64 encoding.</li>
              </ul>
            </p>
          </td>
        </tr>
        <tr>
          <td><varname>key_type</varname></td>
          <td>
            <p>A boolean value that determines whether the key material can leave the service.</p>
            <p>When you set the <code>extractable</code> attribute to <code>false</code>, the service designates the key as a root key that you can use for <code>wrap</code> or <code>unwrap</code> operations.</p>
          </td>
        </tr>
          <caption style="caption-side:bottom;">Table 4. Describes the variables that are needed to add a root key with the {{site.data.keyword.hscrypto}} key management API</caption>
      </table>

      To protect the confidentiality of your personal data, avoid entering personally identifiable information (PII), such as your name or location, when you add keys to the service. For more examples of PII, see section 2.2 of the [NIST Special Publication 800-122](https://www.nist.gov/publications/guide-protecting-confidentiality-personally-identifiable-information-pii){: external}.
      {: important}

      A successful `POST api/v2/keys` response returns the ID value for your key, along with other metadata. The ID is a unique identifier that is assigned to your key and is used for subsequent calls to the {{site.data.keyword.hscrypto}} key management API.

3. Optional: Verify that the key was added by running the following call to browse the keys in your {{site.data.keyword.hscrypto}} service instance.

    ```cURL
    curl -X GET \
    https://api.<region>.hs-crypto.cloud.ibm.com:<port>/api/v2/keys \
    -H 'accept: application/vnd.ibm.collection+json' \
    -H 'authorization: Bearer <IAM_token>' \
    -H 'bluemix-instance: <instance_ID>'
    ```
    {: codeblock}

## Importing root keys with the CLI
{: #import-root-key-cli}

Complete the following steps to import root keys using the {{site.data.keyword.keymanagementserviceshort}} CLI, which is integrated in {{site.data.keyword.hscrypto}}:

1. [Set up the {{site.data.keyword.keymanagementserviceshort}} CLI](/docs/hs-crypto?topic=hs-crypto-set-up-cli).

2. Import a root key with the following command:

    ```
    ibmcloud kp key create
    ```
    {: pre}

    You can find additional parameters for this command in the [{{site.data.keyword.keymanagementserviceshort}} CLI reference](/docs/key-protect?topic=key-protect-cli-reference#kp-key-create).

## Base64 encoding your key material
{: #encode-key-material-root-key}

When importing an existing root key, it is required to include the encrypted key material that you want to store and manage in the service.

### Using OpenSSL to encode existing key material
{: #open-ssl-encoding-root-key}

1. Download and install [OpenSSL](https://github.com/openssl/openssl#for-production-use){:external}.
2. Base64 encode your key material string by running the following command:

    ```
    $ openssl base64 -in <infile> -out <outfile>
    ```
    {: codeblock}

    Replace the variables in the example request according to the following table.

    <table>
      <tr>
        <th>Variable</th>
        <th>Description</th>
      </tr>
      <tr>
        <td>
          <varname>infile</varname>
        </td>
        <td>
          <p>
            The name of the file where your key material string resides.
          </p>
        </td>
      </tr>
      <tr>
        <td>
          <varname>outfile</varname>
        </td>
        <td>
          <p>
            The name of the file where your base64 encoded key material will be outputted once the command has run.
          </p>
          <p>
            Ensure that the key is 128, 192, or 256 bits in length.
          </p>
        </td>
      </tr>

      <caption style="caption-side:bottom;">
        Table 3. Describes the variables that are needed to base64 encode your key material.
      </caption>
    </table>

  If you want to output the base64 material in the command line directly rather than a file, run the command `openssl enc -base6<<< '<key_material_string>'`, where key_material_string is the key material input for your imported key.
  {: note}

### Using OpenSSL to create and encode new key material
{: #open-ssl-encoding-new-key-material-root-key}

1. Download and install [OpenSSL](https://github.com/openssl/openssl#for-production-use){:external}.
2. Base64 encode your key material string by running the following command:

    ```
    $ openssl rand <bit_length> -base64
    ```
    {: codeblock}

    Replace the variable in the example request according to the following table.

    <table>
      <tr>
        <th>Variable</th>
        <th>Description</th>
      </tr>
      <tr>
        <td>
          <varname>bit_length</varname>
        </td>
        <td>
          <p>
            The length of the key, measured in bits.
          </p>
          <p>
            Acceptable bit lengths: 128, 192, 256
          </p>
        </td>
      </tr>

      <caption style="caption-side:bottom;">
        Table 4. Describes the variable that is needed to create and encode new key material.
      </caption>
    </table>

## What's next
{: #import-root-key-next}

- To find out more about protecting keys with envelope encryption, check out [Wrapping keys](/docs/hs-crypto?topic=hs-crypto-wrap-keys).
- To find out instruction on creating a key, check out [Creating root keys](/docs/hs-crypto?topic=hs-crypto-create-root-keys) or [Creating standard keys](/docs/hs-crypto?topic=hs-crypto-create-standard-keys).
- To find out more about programmatically managing your keys, [check out the {{site.data.keyword.hscrypto}} key management API reference doc](https://{DomainName}/apidocs/hs-crypto){: external}.
- To find out more about using the {{site.data.keyword.keymanagementserviceshort}} CLI, check out the [{{site.data.keyword.keymanagementserviceshort}} CLI reference doc](/docs/key-protect?topic=key-protect-cli-reference).
