---

copyright:
  years: 2018-2020
lastupdated: "2020-01-08"

keywords: private, private endpoints, private network, private connection, crypto network, dedicated network, VRF, service endpoints, regions, location

subcollection: hs-crypto

---

{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:external: target="_blank" .external}
{:codeblock: .codeblock}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:term: .term}

# Using private network
{: #private-endpoints}

You can create and manage {{site.data.keyword.cloud}} {{site.data.keyword.hscrypto}} resources through the private network of {{site.data.keyword.cloud_notm}}.
{: shortdesc}

Public endpoints provide a connection to your deployment on the public network. Your environment needs to have internet access to connect to a deployment. A deployment with a service endpoint on the private network gets an endpoint that is not accessible from the public internet. Once your environment has access to the private network of {{site.data.keyword.cloud_notm}}, an internet connection is not required to connect to your deployment.

To get started, enable [virtual routing and forwarding (VRF) and service endpoints](/docs/account?topic=account-vrf-service-endpoint){: external} for your infrastructure account. After you enable VRF for your account, you can connect to {{site.data.keyword.hscrypto}} by using a private IP that is accessible only through the private network of {{site.data.keyword.cloud_notm}}.

After you create a {{site.data.keyword.hscrypto}} service instance, you can enable a [private network connection](/docs/hs-crypto?topic=hs-crypto-regions#connectivity-options) for managing {{site.data.keyword.hscrypto}} resources in your account. To learn more about private connections on {{site.data.keyword.cloud_notm}}, see [Service endpoints for private connections](/docs/resources?topic=resources-service-endpoints){:external}.

## Before you begin
{: #private-endpoint-prereqs}

1. Ensure that your {{site.data.keyword.cloud_notm}} infrastructure account is enabled for [virtual routing and forwarding (VRF)](/docs/account?topic=account-vrf-service-endpoint#vrf).

    When you enable VRF, a separate routing table is created for your account, and connections to and from your account's resources are routed separately on the {{site.data.keyword.cloud_notm}} network. To learn more about VRF technology, see [Virtual routing and forwarding on {{site.data.keyword.cloud_notm}}](/docs/resources?topic=direct-link-overview-of-virtual-routing-and-forwarding-vrf-on-ibm-cloud).

    Enabling VRF permanently alters networking for your account. Be sure that you understand the impact to your account and resources. After you enable VRF, it cannot be disabled.
    {: important}
2. Ensure that your {{site.data.keyword.cloud_notm}} infrastructure account is enabled for [service endpoints](/docs/account?topic=account-vrf-service-endpoint#service-endpoint).

    When you enable service endpoints, you can connect to {{site.data.keyword.hscrypto}} by using a private IP that is accessible only through the private network of {{site.data.keyword.cloud_notm}}. To find out more, see [Service endpoints for private connections](/docs/resources?topic=resources-service-endpoints).

    After you enable service endpoints for your account, all existing and future {{site.data.keyword.hscrypto}} resources and service instances become available from both the public and private endpoint.
    {: note}

## Step 1: Configure the private network of {{site.data.keyword.cloud_notm}} on your virtual server
{: #configure-network}

Prepare your VSI or test machine by configuring your routing table for the private network of {{site.data.keyword.cloud_notm}}.

1. To route traffic to the private network of {{site.data.keyword.cloud_notm}}, run the following command on your VSI:

    ```sh
    route add -net 166.9.0.0/16 gw <gateway> dev <gateway_interface>
    ```
    {: pre}

    Replace `<gateway>` (for example, `10.x.x.x`) and `<gateway_interface>` (for example, `eth10`) with the appropriate values.

2. Optional: Verify that the route was added successfully by displaying your new routing table.

    ```sh
    route -n
    ```
    {: pre}

## Step 2: Key management service only: Target the {{site.data.keyword.hscrypto}} private endpoint for key management service
{: #target-internal-endpoint}

If you are using the key management service of {{site.data.keyword.hscrypto}}, after you configure your VSI to accept the private network traffic of {{site.data.keyword.cloud_notm}}, you can target the private endpoint for {{site.data.keyword.hscrypto}} by using the {{site.data.keyword.keymanagementserviceshort}} CLI plug-in.

If you are using the GREP11 service, the service handles the private endpoint connection. You need to only switch your GREP11 service endpoint to the private endpoint per instructions in [Generating a GREP11 API request](/docs/hs-crypto?topic=hs-crypto-set-up-grep11-api#form-grep11-api-request).
{: note}

1. From the command line, log in to {{site.data.keyword.cloud_notm}}.

    ```sh
    ibmcloud login
    ```
    {: pre}

    If the login fails, run the `ibmcloud login --sso` command to try again. The `--sso` parameter is required when you log in with a federated ID. If this option is used, go to the link listed in the CLI output to generate a one-time passcode.
    {: note}

2. Optional: Ensure that your account is enabled for VRF and service endpoints.

    ```sh
    ibmcloud account show
    ```
    {: pre}

    The following CLI output shows the account details of a VRF and service endpoint-enabled account.

    ```
    Retrieving account John Doe's Account of john.doe@email.com...
    OK

    Account ID:                   d154dfbd0bc2edefthyufffc9b5ca318
    Currently Targeted Account:   true
    Linked Softlayer Account:     1008967
    Service Endpoint Enabled:     true
    ```
    {: screen}

    See [Enabling VRF and service endpoints](/docs/account?topic=account-vrf-service-endpoint){: external} to learn how to set up your account for connecting to a private network.
    {: tip}

3. Set the environment variable to target the {{site.data.keyword.hscrypto}} private endpoint.

  Use the following commands on the Linux&reg; operating system or MacOS only. For how to set environment variables on the Windows&reg; operating system, see [Accessing {{site.data.keyword.keymanagementserviceshort}} CLI](/docs/hs-crypto?topic=hs-crypto-set-up-cli).
  {: note}

  Set the KP_PRIVATE_ADDR environment variable to target the private endpoint:

  ```
  export KP_PRIVATE_ADDR=https://api.private.<region>.hs-crypto.cloud.ibm.com:<port>
  ```
  {: pre}

  You can find the private endpoint URL listed in the **Manage** tab of the service dashboard. Alternatively, you can dynamically [retrieve the API endpoint URL](https://{DomainName}/apidocs/hs-crypto#retrieve-the-api-endpoint-url){: external}. The returned value includes the following:

  ```
  {
    "instance_id": "<instance_ID>",
    "kms": {
      "public": "api.<region>.hs-crypto.cloud.ibm.com:<port>",
      "private":"api.private.<region>.hs-crypto.cloud.ibm.com:<port>"
    },
    "ep11": {
      "public": "ep11.<region>.hs-crypto.cloud.ibm.com:<port>",
      "private":"ep11.private.<region>.hs-crypto.cloud.ibm.com:<port>"
    }
  }
  ```
  {: screen}

  The private endpoint URL is returned in `private`. For key management endpoint, use the value that is returned in the `kms` section.

  The KP_PRIVATE_ADDR environment variable is used to set the API endpoint URL both for public endpoint and private endpoint. If you want to use the public endpoint, make sure to set the KP_PRIVATE_ADDR environment variable as the public endpoint URL that is returned in the `public` field in the `kms` section.
  {: important}


## Step 3: Test your private network connection
{: #Test-private-connection}

You might want to test your network connection after you set up your private network.

To test the private network connection for the key management service, use {{site.data.keyword.keymanagementserviceshort}} CLI to perform an action towards the {{site.data.keyword.hscrypto}} service instance. The following example shows how to create a [root key](#x6946961){: term} on the private network.

1. [Create a root key](/docs/hs-crypto?topic=hs-crypto-create-root-keys) by targeting the private endpoint.

  ```sh
  ibmcloud kp create <key_name> -i <instance_ID>
  ```
  {: pre}

  Replace `<key_name>` with a human-readable alias for easy identification of your key. Replace `<instance_ID>` with the {{site.data.keyword.cloud_notm}} instance ID that identifies your {{site.data.keyword.hscrypto}} service instance.

2. Optional: Verify that the key was created successfully by listing the keys that are available in your {{site.data.keyword.hscrypto}} service instance.

  ```sh
  ibmcloud kp list -i <instance_ID>
  ```
  {: pre}

  Replace `<instance_ID>` with the {{site.data.keyword.cloud_notm}} instance ID that identifies your {{site.data.keyword.hscrypto}} service instance.

<!-- * To test the private network connection for the GREP11 service, generate a GREP11 API request. For details, check out [Generating a GREP API request](/docs/hs-crypto?topic=hs-crypto-set-up-grep11-api#form-grep11-api-request). -->

## Next steps
{: #private-network-next-steps}

You're now set to interact with {{site.data.keyword.hscrypto}} through a private endpoint. To find out more about managing keys with {{site.data.keyword.hscrypto}}, [check out the {{site.data.keyword.keymanagementserviceshort}} CLI reference doc](/docs/key-protect?topic=key-protect-cli-reference).

<!-- - To find out more about managing your data using the cloud HSM function of {{site.data.keyword.hscrypto}}, [check out the GREP11 API reference doc](/docs/hs-crypto?topic=hs-crypto-grep11-api-ref).-->
