---
title: "Azure Operator Nexus: Nexus Kubernetes Cluster Service"
description: Introduction to Nexus Kubernetes Cluster Service.
author: scottsteinbrueck
ms.author: ssteinbrueck
ms.service: azure-operator-nexus
ms.topic: conceptual
ms.date: 06/28/2023
ms.custom: template-concept
---

# Nexus Kubernetes cluster overview

This article describes core concepts of a Nexus Azure Kubernetes Service Cluster, a managed Azure Kubernetes Service that you can use to deploy and operate containerized applications on [Azure Operator Nexus Platform](./overview.md)

## What is Kubernetes?

Kubernetes is a rapidly evolving platform that manages container-based applications and their associated networking and storage components.
Kubernetes focuses on the application workloads, not the underlying infrastructure components. It provides a declarative approach to
deployments, backed by a robust set of APIs for management operations. See [What is Kubernetes](/azure/aks/concepts-clusters-workloads#what-is-kubernetes) to learn about Kubernetes.

## Nexus Kubernetes Service

Nexus Kubernetes Cluster Service is a Kubernetes distribution designed for on-premises deployment over Nexus instances. It streamlines the automated creation of containers, and is optimized to run workloads associated for data-intensive network functions.

Like any Kubernetes cluster, Nexus Kubernetes cluster has two components:

* Control plane: provides core Kubernetes services and manages the life cycle of application workloads.

* Nodes: To run your applications and supporting services, you need a Kubernetes node. It provides the container runtime environment.
Each Nexus AKS cluster has at least one node. Node is hosted in virtual machine (VM) that runs the [Kubernetes node components](/azure/aks/core-aks-concepts#nodes). 
VM is created as part of Nexus AKS cluster deployment on Nexus instance. There are two types of node pools in Nexus Kubernetes Clusters

  * When you create a Nexus AKS cluster, you define the initial number of nodes and their size (SKU), which creates a system node pool. System node pools host critical system pods.
  * On the other hand, to support applications that have different compute or storage demands, you can create user node pools, also known as Nexus Agent pool. Each VM in a Nexus Agent pool adheres to a uniform configuration, such as CPU, memory, disk and so on. Once an Agent pool is established, the number of VMs within it remains fixed. To scale the capacity of a Nexus Kubernetes cluster, more Agent pools can be created and integrated into the existing cluster. In other words, the Nexus Agent pool supports horizontal scaling by allowing the addition or removal of Agent pools within the Nexus Kubernetes cluster.

However, application pods can be scheduled on system node pools in case user wants only one node pool in their cluster. Every Nexus Kubernetes Cluster must
contain at least one system node pool with at least one node.

## Nexus Kubernetes Cluster Features

Nexus Kubernetes Cluster Features, previously known as "Add-ons" in releases before [Azure Operator Nexus](./overview.md) release 3.11.0, is a component of the Nexus platform that allows customers to enhance their Nexus Kubernetes clusters with extra packages or components. The Features are categorized into two types: required and optional.

* Required Features: Required Features are automatically deployed into provisioned Nexus Kubernetes clusters. Core Features such as Calico, MetalLB, Nexus Storage CSI, IPAM plugins, metrics-server, node-local-dns, Arc for Kubernetes,
and Arc for Servers are included by default when clusters are created. The successful completion of the cluster provisioning process depends on these Features being installed successfully. If a required Feature installation fails and can't be fixed, the cluster status is marked as failed.

* Optional Features: Optional Features are supplementary services associated with a Kubernetes Cluster resource. Customers can choose to activate or deactivate these Features on demand.
Example for supplementary services could include deployment of cluster-level local image caching registry within the Nexus AKS cluster to support for disconnected scenarios. Nexus AKS enables the customer to observe the status, health,
and version of each required and optional Feature, which can be monitored on Azure portal, or the state can be fetched using Azure Resource Manager APIs.

Features are installed once and can only be updated or upgraded when the customer upgrades the Nexus Kubernetes cluster. It enables customers to apply critical production hotfixes without interference from the underlying infrastructure operations, which could otherwise overwrite their cluster modifications.

## Nexus Available Zones

Nexus supports the concept of an Availability Zone. An Availability Zone is delineated at the Rack level and allows customers to spread their workloads across various zones to achieve high availability. For a Nexus instance with eight racks, customers get eight Availability Zones to spread their workload over. This is configured within control plane and agentpool configuration for a given Nexus AKS cluster.

Each Zone comprises a pair of management servers with redundancy and a collection of compute servers that function as a resource pool.
During multi-rack deployments in Nexus and when performing runtime bundle upgrades, Availability Zones provide the added benefit of acting as an upgrade domain. This ensures that, at most, only the servers within a single rack are taken offline for these upgrades.

## Failure domain

Operator Nexus ensures that the Nexus Kubernetes Cluster nodes are distributed across upgrade domains. This distribution is done in a way that improves the resilience and availability of the cluster. Operator Nexus uses Kubernetes affinity rules to schedule nodes in specific zones. It ensures that the underlying VMs aren't placed on the same physical server or in the same upgrade domain, improving the cluster's fault tolerance.

## Next steps

  * [Guide to deploy Nexus kubernetes cluster](./quickstarts-kubernetes-cluster-deployment-bicep.md)
  * [Supported Kubernetes versions](./reference-nexus-kubernetes-cluster-supported-versions.md)
