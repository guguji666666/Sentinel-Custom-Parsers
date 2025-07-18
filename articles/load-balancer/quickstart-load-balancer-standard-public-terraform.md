---
title: 'Quickstart: Create a public load balancer - Terraform'
titleSuffix: Azure Load Balancer
description: This quickstart shows how to create a load balancer by using Terraform.
services: load-balancer
author: mbender-ms
manager: kumudD
ms.service: azure-load-balancer
ms.topic: quickstart
ms.date: 04/01/2025
ms.author: mbender
ms.custom: devx-track-terraform
#Customer intent: I want to create a load balancer by using Terraform so that I can load balance internet traffic to VMs.
# Customer intent: As a cloud infrastructure developer, I want to create a public load balancer using Terraform, so that I can efficiently distribute internet traffic to my virtual machines.
---

# Quickstart: Create a public load balancer to load balance VMs using Terraform

This quickstart shows you how to deploy a standard load balancer to load balance virtual machines using Terraform.

[!INCLUDE [About Terraform](~/azure-dev-docs-pr/articles/terraform/includes/abstract.md)]

> [!div class="checklist"]
> * Create an Azure resource group using [azurerm_resource_group](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/resource_group)
> * Create an Azure Virtual Network using [azurerm_virtual_network](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/virtual_network)
> * Create an Azure subnet using [azurerm_subnet](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/subnet)
> * Create an Azure public IP using [azurerm_public_ip](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/public_ip)
> * Create an Azure Load Balancer using [azurerm_lb](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/lb)
> * Create an Azure network interface using [azurerm_network_interface](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/network_interface)
> * Create an Azure network interface load balancer backend address pool association using [azurerm_network_interface_backend_address_pool_association](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/network_interface_backend_address_pool_association)
> * Create an Azure Linux Virtual Machine using [azurerm_linux_virtual_machine](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/linux_virtual_machine)
> * Create an Azure Virtual Machine Extension using [azurerm_virtual_machine_extension](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/virtual_machine_extension)

## Prerequisites
- Create an Azure account with an active subscription. You can [create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- [Install and configure Terraform](/azure/developer/terraform/quickstart-configure)

## Implement the Terraform code

The sample code for this article is located in the [Azure Terraform GitHub repo](https://github.com/Azure/terraform/tree/master/quickstart/101-azure-load-balancer-public). You can view the log file containing the [test results from current and previous versions of Terraform](https://github.com/Azure/terraform/tree/master/quickstart/101-azure-load-balancer-public/TestRecord.md). See more [articles and sample code showing how to use Terraform to manage Azure resources](/azure/terraform)

1. Create a directory in which to test and run the sample Terraform code, and make it the current directory.

1. Create a file named `providers.tf` and insert the following code.
    :::code language="Terraform" source="~/terraform_samples/quickstart/101-azure-load-balancer-public/providers.tf":::

1. Create a file named `main.tf` and insert the following code.
    :::code language="Terraform" source="~/terraform_samples/quickstart/101-azure-load-balancer-public/main.tf":::

1. Create a file named `variables.tf` and insert the following code.
    :::code language="Terraform" source="~/terraform_samples/quickstart/101-azure-load-balancer-public/variables.tf":::

1. Create a file named `outputs.tf` and insert the following code.
    :::code language="Terraform" source="~/terraform_samples/quickstart/101-azure-load-balancer-public/outputs.tf":::

> [!IMPORTANT]
> If you're using the 4.x azurerm provider, you must [explicitly specify the Azure subscription ID](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/guides/4.0-upgrade-guide#specifying-subscription-id-is-now-mandatory) to authenticate to Azure before running the Terraform commands.
>
> One way to specify the Azure subscription ID without putting it in the `providers` block is to specify the subscription ID in an environment variable named `ARM_SUBSCRIPTION_ID`.
>
> For more information, see the [Azure provider reference documentation](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs#argument-reference).

## Initialize Terraform

[!INCLUDE [terraform-init.md](~/azure-dev-docs-pr/articles/terraform/includes/terraform-init.md)]

## Create a Terraform execution plan

[!INCLUDE [terraform-plan.md](~/azure-dev-docs-pr/articles/terraform/includes/terraform-plan.md)]

## Apply a Terraform execution plan

[!INCLUDE [terraform-apply-plan.md](~/azure-dev-docs-pr/articles/terraform/includes/terraform-apply-plan.md)]

## Verify the results

1. Display the Azure resource group name.

    ```console
    terraform output -raw resource_group_name
    ```

1. Optionally, display the VM (virtual machine) password.

    ```console
    terraform output -raw vm_password
    ```

1. Display the public IP address.

    ```console
    terraform output -raw public_ip_address
    ```

1. Paste the public IP address into the address bar of your web browser. The custom VM page of the Nginx web server is displayed in the browser.

## Clean up resources

[!INCLUDE [terraform-plan-destroy.md](~/azure-dev-docs-pr/articles/terraform/includes/terraform-plan-destroy.md)]

## Troubleshoot Terraform on Azure

[Troubleshoot common problems when using Terraform on Azure](/azure/developer/terraform/troubleshoot)

## Next steps

> [!div class="nextstepaction"]
> [What is Azure Load Balancer?](load-balancer-overview.md)

