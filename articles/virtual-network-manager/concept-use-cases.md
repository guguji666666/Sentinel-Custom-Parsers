---
title: Common use cases for Azure Virtual Network Manager
description: This article covers common use cases for customers who use Azure Virtual Network Manager.
author: mbender-ms
ms.author: mbender
ms.topic: overview 
ms.date: 07/11/2025
ms.custom: template-overview
ms.service: azure-virtual-network-manager
# Customer Intent: As a network admin, I need to know when I should use Azure Virtual Network Manager in my organization for managing virtual networks across my organization in a scalable, flexible, and secure manner with minimal administrative overhead.
---

# Common use cases for Azure Virtual Network Manager

Learn about use cases for Azure Virtual Network Manager, including managing the connectivity of virtual networks, helping to secure network traffic, and routing traffic among your network resources.

## Connectivity configuration

You can use a connectivity configuration to create various network topologies based on your network needs. You create a connectivity configuration by adding new or existing virtual networks into [network groups](concept-network-groups.md) and creating a topology that meets your needs. A connectivity configuration offers two topology options: mesh and hub-and-spoke. You can also create a hub-and-spoke topology with direct connectivity between spoke virtual networks.

### Mesh topology

When you deploy a [mesh topology](concept-connectivity-configuration.md), all virtual networks have direct connectivity with each other. They don't need to go through other hops on the network to communicate. A mesh topology is useful when all the virtual networks need to communicate directly with each other.

One common scenario is to mesh specific spoke virtual networks to boost latency and throughput. You don't have to mesh all the spoke virtual networks. You can also mesh spoke virtual networks connected to Virtual WAN hubs, allowing for direct communication and improved latency while still using the hubs to communicate with other virtual networks.

### Hub-and-spoke topology

We recommend a [hub-and-spoke topology](concept-connectivity-configuration.md#hub-and-spoke-topology) when you're deploying central infrastructure services in a hub virtual network that are shared by spoke virtual networks. This topology can be more efficient than having these common components in all spoke virtual networks.

### Hub-and-spoke topology with direct connectivity

A hub-and-spoke topology with direct connectivity combines the two preceding topologies. We recommend it when you have common central infrastructure in the hub, and you want direct communication between all spokes. [Direct connectivity](concept-connectivity-configuration.md#enable-direct-connectivity) helps you reduce the latency that extra network hops cause when they're going through a hub.

### Maintaining a virtual network topology

When you make changes to your infrastructure, Azure Virtual Network Manager automatically maintains the topology you defined in the connectivity configuration. For example, when you add a new spoke network group to the topology, Azure Virtual Network Manager can handle the changes that are necessary to create the connectivity to the spoke network group's virtual networks.

> [!Important]
> You can deploy and manage Azure Virtual Network Manager through the [Azure portal](./create-virtual-network-manager-portal.md), the [Azure CLI](./create-virtual-network-manager-cli.md), [Azure PowerShell](./create-virtual-network-manager-powershell.md), or [Terraform](./create-virtual-network-manager-terraform.md).

## Security

With Azure Virtual Network Manager, you create [security admin rules](concept-security-admins.md) to enforce security policies across virtual networks in your organization. Security admin rules take precedence over rules that network security groups define. Security admin rules are applied first in traffic analysis, as shown in the following diagram:

:::image type="content" source="media/concept-security-admins/traffic-evaluation.png" alt-text="Diagram that shows the order of evaluation for network traffic with security admin rules and network security rules.":::

Common uses include:

- Standard rules that must be applied and enforced on all existing virtual networks and newly created virtual networks.

- Security rules that can't be modified and enforce organizational-level rules.

- Enforced security protection to prevent users from opening high-risk ports.

- Default rules for everyone in the organization so administrators can prevent security threats caused by misconfiguration or unintended gaps in network security groups.

- Implement security guardrails with security admin rules while allowing flexibility for virtual network owners to configure their network security groups, ensuring the network security groups don't break company policies.

- Force-allow the traffic to and from critical services, such as monitoring services and program updates, so other users can't accidentally block this necessary traffic.

For a walkthrough of use cases, see the blog post [Securing Your Virtual Networks with Azure Virtual Network Manager](https://techcommunity.microsoft.com/t5/azure-networking-blog/securing-your-virtual-networks-with-azure-virtual-network/ba-p/3353366).

## Next steps

- [Create an Azure Virtual Network Manager instance](create-virtual-network-manager-portal.md) by using the Azure portal.

- Deploy an [Azure Virtual Network Manager instance](create-virtual-network-manager-terraform.md) by using Terraform.

- Learn more about [network groups](concept-network-groups.md) in Azure Virtual Network Manager.

- Learn what you can do with a [connectivity configuration](concept-connectivity-configuration.md).

- Learn more about [security admin configurations](concept-security-admins.md).
