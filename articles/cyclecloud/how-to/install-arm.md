---
title: Install CycleCloud using ARM template
description: How to install CycleCloud using an ARM template
author: adriankjohnson
ms.date: 07/01/2025
ms.author: adjohnso
---

# Running CycleCloud using an ARM template

You can install Azure CycleCloud on Azure resources by using an Azure Resource Manager template (ARM template) that you store on GitHub. The ARM template takes care of most of the CycleCloud setup. The ARM template:

1. Deploys a virtual network with three separate subnets:
    * *cycle*: The subnet where the CycleCloud server starts
    * *compute*: A /22 subnet for the High Performance Computing (HPC) clusters
    * *user*: The subnet for creating user authentications
1. Provisions a VM in the *cycle* subnet and installs Azure CycleCloud on it.

The recommended method of installing CycleCloud is via the CycleCloud Marketplace Image. For more information, see the [CycleCloud Marketplace Installation Quickstart](../qs-install-marketplace.md). You can also install CycleCloud manually, which gives you greater control over the installation and configuration process. For more information, see the [Manual CycleCloud Installation Quickstart](install-manual.md).

## Prerequisites

You need:

1. An Azure account with an active subscription.
1. A Shell session in a terminal.
    * If you're using a Windows machine, use the [browser-based Bash shell](https://shell.azure.com).
    * For non-Windows machines, install and use Azure CLI v2.0.20 or later. Run `az --version` to find your current version. If you need to install or upgrade, see [Install Azure CLI 2.0](/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](~/articles/cyclecloud/includes/cloud-shell-try-it.md)]

### Service principal

Azure CycleCloud needs a service principal with contributor access to your Azure subscription. If you don't have a service principal, you can create one. Your service principal name must be unique. In the following example, *CycleCloudApp* can be replaced with any name you choose:

```azurecli-interactive
az ad sp create-for-rbac --name CycleCloudApp --years 1
```

The output displays several parameters. Save the `appId`, `password`, and `tenant` values:

``` output
"appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
"displayName": "CycleCloudApp",
"name": "http://CycleCloudApp",
"password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
"tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

### SSH keypair

You need an SSH key to sign in to the CycleCloud VM and clusters. Generate an SSH keypair:

```azurecli-interactive
ssh-keygen -f ~/.ssh/id_rsa -m pem -t rsa -N "" -b 4096
```

> [!NOTE]
> The Python cryptography library used by the CycleCloud CLI doesn't support the newer OpenSSH serialization format. Use `ssh-keygen -m pem` to generate the key with the older standard format.

Get the SSH public key with:

```azurecli-interactive
cat ~/.ssh/id_rsa.pub
```

The output starts with `ssh-rsa` followed by a long string of characters. Copy and save this key now.

On Linux, follow [these instructions on GitHub](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) to generate a new SSH keypair.

## Deploy Azure CycleCloud

Select the following button to deploy Azure CycleCloud into your subscription:

<a target="_blank"
   title="Deploy to Azure"
   href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FCycleCloudCommunity%2Fcyclecloud_arm%2Fmaster%2Fazuredeploy.json">
  <img src="https://azuredeploy.net/deploybutton.svg" alt="deploy to azure"/>
</a>

Enter the required information.

Basics:

* *Subscription*: If you have more than one active Azure subscription, select the one to use here
* *Resource Group*: Enter the name of a new resource group that holds everything generated by this quickstart (for example, `MyQuickstart`)
* *Location*: Select a region in which to store your instance

Settings:

* *Tenant ID*: The `tenant` from the service principal
* *Application ID*: The `appId` from the service principal
* *Application Secret*: The `password` from the service principal
* *SSH Public Key*: The public key you use to sign in to the CycleCloud VM
* *Username*: The username for the CycleCloud VM. Use your Azure portal username without the domain (for example, *johnsmith* instead of *johnsmith@domain.com*)

Use the default values for the other fields. Agree to the terms and conditions, and select **Purchase**. The CycleCloud product is free, but you pay for the core hours you use in Azure.

The deployment process runs an installation script as a custom script extension, which installs and sets up CycleCloud. This process takes between 5 and 8 minutes.

## Sign in to the CycleCloud application server

To connect to the CycleCloud webserver, get the Fully Qualified Domain Name (FQDN) of the CycleServer VM from either the Azure portal or the CLI:

```azurecli-interactive
# Replace "MyQuickstart" with the resource group you created above.
export RESOURCE_GROUP="MyQuickstart"
az network public-ip show -g ${RESOURCE_GROUP?} -n cycle-ip --query dnsSettings.fqdn
```

Browse to `https://<FQDN>/`. The installation uses a self-signed SSL certificate, which might show up with a warning in your browser.

Create a **Site Name** for your installation. You can use any name:

![CycleCloud Welcome screen](~/articles/cyclecloud/images/cc-first-login.png)

The Azure CycleCloud End User License Agreement is displayed - accept it. You need to create a CycleCloud admin user for the application server. We recommend using the same user name you used earlier. Make sure the password you enter meets the listed requirements. Select **Done** to continue.

![CycleCloud Create New User screen](~/articles/cyclecloud/images/create-new-user.png)

After you create your user, set your SSH key so you can more easily access any Linux VMs that CycleCloud creates. To add an SSH key, edit your profile by selecting your name in the upper right corner of the screen.

You now have a running CycleCloud application that lets you create and run clusters.

> [!NOTE]
> You can customize the default CycleCloud configuration for specific environments by using settings in the _$CS_HOME/config/cycle_server.properties_ file.


## Further reading

* [Run CycleCloud using a Marketplace VM](../qs-install-marketplace.md)
* [Install CycleCloud manually](install-manual.md)
* [Explore CycleCloud features with the tutorial](../tutorials/tutorial.md)