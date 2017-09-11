---
services: azure-resource-manager
platforms: python
author: lmazuel
---

# Use MSI to authenticate simply from inside a VM

This sample explains how to use the SDK from inside an Azure resource like a VM, 
using Managed Service Identity (MSI) authentication.

**On this page**

- [Run this sample](#run)
- [What is example.py doing?](#example)
    - [Create a MSI authentication instance](#create-credentials)
    - [Get the subscription ID of that token](#subscription_id)
    - [List resource groups](#list-groups)

<a id="run"></a>
## Run this sample

1. This sample is intended to be executed from inside a VM with MSI enabled. To create a VM with MSI enabled, please refer
   this document (LINK TO DO).

1. If you don't already have it, [install Python](https://www.python.org/downloads/) on that VM.

1. We recommend to use a [virtual environnement](https://docs.python.org/3/tutorial/venv.html) to run this example, but it's not mandatory. You can initialize a virtualenv this way:

    ```
    pip install virtualenv
    virtualenv mytestenv
    cd mytestenv
    source bin/activate
    ```

1. Clone the repository.

    ```
    git clone https://github.com/Azure-Samples/resource-manager-python-manage-resources-with-msi.git
    ```

1. Install the dependencies using pip.

    ```
    cd resource-manager-python-manage-resources-with-msi
    pip install -r requirements.txt
    ```

1. Run the sample.

    ```
    python example.py
    ```

<a id="example"></a>
## What is example.py doing?

The sample creates a MSI Authentication credentials class. Then it uses this credentials to
extract the current subscription ID. Finally it uses this credentials and subscription ID
to list all the available Resource Groups.

Note that listing Resource Group is just an example, there is no actual limit of what you can do with this 
credentials (creating a KeyVault account, managing the Network of your VMs, etc.). The limit
will be defined by the roles and policy assigned to the MSI token at the time of the creation of the VM.

<a id="create-credentials"></a>
### Create a MSI authentication instance

```python
from msrestazure.azure_active_directory import MSIAuthentication

credentials = MSIAuthentication()
```

<a id="subscription_id"></a>
### Get the subscription ID of that token

```python
from azure.mgmt.resource import SubscriptionClient

subscription_client = SubscriptionClient(credentials)
subscription = next(subscription_client.subscriptions.list())
subscription_id = subscription.subscription_id
```

<a id="list-groups"></a>
### List resource groups

List the resource groups in your subscription.

```python
from azure.mgmt.resource import ResourceManagementClient

resource_client = ResourceManagementClient(credentials, subscription_id)
for item in resource_client.resource_groups.list():
    print(resource_group.name)
```
