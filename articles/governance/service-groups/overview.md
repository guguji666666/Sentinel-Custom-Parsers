---
title: "Get started with Service Groups - Azure Governance"
description: "Learn how to use and manage resources with Service Groups."
author: kenieva
ms.author: kenieva
ms.service: azure-policy
ms.topic: overview
ms.date: 05/19/2025
ms.custom:
  - build-2025
---

# What are Azure Service Groups?

Azure Service Groups offer a flexible way to organize and manage resources across subscriptions and resource groups, parallel to any existing Azure resource hierarchy. They're ideal for scenarios requiring cross-boundary grouping, minimal permissions, and aggregations of data across resources. These features empower teams to create tailored resource collections that align with operational, organizational, or persona-based needs. This article helps give you an overview of what Service Groups are, the scenarios to use them for, and important facts.

> [!IMPORTANT]
> Azure Service Groups is currently in PREVIEW. 
> For more information about participating in the preview, see [Azure Service Groups Preview](https://aka.ms/ServiceGroups/PreviewSignup).
> See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.


## Key capabilities
- **Multiple Hierarchies**: Service Groups enable scenarios where the resources can be grouped in different views for multiple purposes.
- **Flexible Membership**: Service Groups allow resources from different subscriptions to be grouped together, providing a unified view and management capabilities. They also allow the grouping of subscriptions, resource groups, and resources. The same resources can be connected to many different service groups allowing different customer personas and scenarios to be created and used. 
- **Low Privilege Management**: Service Groups are designed to operate with minimal permissions, ensuring that users can manage resources without needing excessive access rights.


### Example Scenarios
Customers can create many different views that support how they organize their resources.   

* Aggregating  Metrics
   * Organizations with multiple applications and environments can use Service Groups to aggregate  metrics across different environments. Member resources or resource containers could be from various environments within different management groups or subscriptions, can be linked to a single Service Group providing a unified view of metrics.
   * Since Service Groups don't inherit permissions to the members, customers can apply least privileges to assign permissions on the Service Groups that allow viewing of metrics. This capability provides a solution where two users can be assigned access to the same Service Group, but only one is allowed to see certain resources.
        
* Creating Inventory
    * Customers can connect resources to the Service Groups to get a consolidated view of all the resources of a particular type or function in the entire environment.

:::image type="content" source="./media/side-by-side.png" alt-text="Diagram showing the Management Group and Service Group Hierarchies within the Microsoft Entra Tenant" Lightbox="./media/side-by-side.png":::  

* Varying Personas 
    * With Service Groups, organizations have the ability to manage multiple hierarchies over the same resources for different personas and their own individual views. Customers can use the same resources to be members of a Workload Service Group, a Department Service Group, and a Service Group with all Production resources. 

:::image type="content" source="./media/multiple-service-group.png" alt-text="Diagram that shows multiple service group branches." Lightbox="./media/multiple-service-group.png":::


## How it works
Azure Service Groups are a parallel tenant level hierarchy that allows the grouping of resources. The separation from Management Groups, Subscriptions, and Resource Groups allows Service Groups to be connected many times to different resources and resource containers without impacting the existing structures. 

Information about Service Groups 
* A Service Group is created within the Microsoft.Management Resource Provider.  
* Service Groups allow self nesting to create up to 10 "levels" of grouping depth. Nesting can be managed via the 'parent' property within the Service Group resource. 
* Role assignments on the Service Group can be inherited to the **child Service Groups only**. There's **no inheritance** through the memberships to the resources or resource containers.
* There's a limit of 2000 service group members coming from within the same subscription. This means that within one subscription, resources, or resource groups, there can only be 2,000 memberships to Service Groups. 
* Within the Preview window, there's a limit of 10,000 Service Groups in a single tenant.   
* Service Groups and Service Group Member IDs support up to 250 characters. They can be alphanumeric and special characters: - _ ( ). ~
* Service Groups require a globally unique ID. Two Microsoft Entra tenants can't have a Service Group with identical IDs.
* Membership to Service Groups are managed by the 'Microsoft.Relationship/ServiceGroupMember' on the desired member (a resource, resource group, or subscription) while targeting the desired Service Group. 


## Azure Resource Manager Groupings 

Azure offers a wide variety of resources containers that enable our customers to manage resources at many different scales. Service Groups is only the newest in a family of Azure Resource Manager (ARM) containers used to organize your environment.

This table shows a summary of the differences between the groups. 

### Scenario comparison

[!INCLUDE [scenario-comparison](../includes/scenario-comparison.md)]

### Important facts about service groups

- A single tenant can support 10,000 service groups.
- Service group tree can support up to 10 levels of depth.
  This limit doesn't include the root level.
- Each service group can have many children.
- A single service group name/ID can be up to 250 characters.
- There are no limits of number of members of service groups, but there's a limit of 2,000 relationships (including ServiceGroupMember) within a subscription

### The Root Service Group 

Service Groups, similarly to Management Groups, has a one root Service Group, which is the top parent of all service groups in that tenant. Root Service Group's ID is same as its Tenant ID.

Service Groups creates the Root Service Group on the first request received within the Tenant and users can't create or update the root service group. _"/providers/microsoft.management/servicegroups/[tenantId]"_

Access to the root has to be given from a user with "microsoft.authorization/roleassignments/write" permissions at the tenant level. For example, the Tenant's Global Administrator can elevate their access on the tenant to have these permissions. [Details on elevating Tenant Global Administrator Accesses](../../role-based-access-control/elevate-access-global-admin.md)

### Role Based Access Controls 
There are three built-in roles definitions to support Service Groups in the preview.  

> [!NOTE]
> Custom Role Based Access Controls aren't supported within the Preview. 

#### Service Group Administrator 
This role manages all aspects of Service Groups and Relationships and is the default role given to users when they create a Service Group. The role restricts the role assignment capabilities to "Service Group Administrator', "Service Group Contributor", and "Service Group Reader" to other users.  

**ID**: '/providers/Microsoft.Authorization/roleDefinitions/4e50c84c-c78e-4e37-b47e-e60ffea0a775"  

```json
{
  "assignableScopes": [
    "/providers/Microsoft.Management/serviceGroups"
  ],
  "createdBy": null,
  "createdOn": "2024-10-15T18:15:20.488676+00:00",
  "description": "Role Definition for administrator of a Service Group",
  "id": "/providers/Microsoft.Authorization/roleDefinitions/4e50c84c-c78e-4e37-b47e-e60ffea0a775",
  "name": "4e50c84c-c78e-4e37-b47e-e60ffea0a775",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "condition": null,
      "conditionVersion": null,
      "dataActions": [],
      "notActions": [
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete"
      ],
      "notDataActions": []
    },
    {
      "actions": [
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete"
      ],
      "condition": "((!(ActionMatches{'Microsoft.Authorization/roleAssignments/write'})) OR (@Request[Microsoft.Authorization/roleAssignments:RoleDefinitionId] ForAnyOfAnyValues:GuidEquals{4e50c84cc78e4e37b47ee60ffea0a775,32e6a4ec60954e37b54b12aa350ba81f,de754d53652d4c75a67f1e48d8b49c97})) AND ((!(ActionMatches{'Microsoft.Authorization/roleAssignments/delete'})) OR (@Resource[Microsoft.Authorization/roleAssignments:RoleDefinitionId] ForAnyOfAnyValues:GuidEquals{4e50c84cc78e4e37b47ee60ffea0a775,32e6a4ec60954e37b54b12aa350ba81f,de754d53652d4c75a67f1e48d8b49c97}))",
      "conditionVersion": "2.0",
      "dataActions": [],
      "notActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Service Group Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions",
  "updatedBy": null,
  "updatedOn": "2025-03-25T18:40:31.229386+00:00"
}
```
#### Service Group Contributor 
The Service Group Contributor role given to users when they need to create or manage the lifecycle of a Service Group. This role allows for all actions except for Role Assignment capabilities.  
```json
{
  "assignableScopes": [
    "/providers/Microsoft.Management/serviceGroups"
  ],
  "createdBy": null,
  "createdOn": "2024-10-15T18:15:20.488676+00:00",
  "description": "Role Definition for contributor of a Service Group",
  "id": "/providers/Microsoft.Authorization/roleDefinitions/32e6a4ec-6095-4e37-b54b-12aa350ba81f",
  "name": "32e6a4ec-6095-4e37-b54b-12aa350ba81f",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "condition": null,
      "conditionVersion": null,
      "dataActions": [],
      "notActions": [
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Service Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions",
  "updatedBy": null,
  "updatedOn": "2024-10-15T18:15:20.488676+00:00"
}
```


#### Service Group Reader 
This built-in role is to be used to read service groups and can also be assigned to other resources to view the connected relationships.  

```json
{
  "assignableScopes": [
    "/"
  ],
  "createdBy": null,
  "createdOn": "2024-10-15T18:15:20.487675+00:00",
  "description": "Role Definition for reader of a Service Group",
  "id": "/providers/Microsoft.Authorization/roleDefinitions/de754d53-652d-4c75-a67f-1e48d8b49c97",
  "name": "de754d53-652d-4c75-a67f-1e48d8b49c97",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/serviceGroups/read",
        "Microsoft.Authorization/*/read"
      ],
      "condition": null,
      "conditionVersion": null,
      "dataActions": [],
      "notActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Service Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions",
  "updatedBy": null,
  "updatedOn": "2024-10-15T18:15:20.487675+00:00"
}
```
 


## Related content
* [Quickstart: Create a service group with REST API](create-service-group-rest-api.md)
* [How to: Manage Service Groups](manage-service-groups.md)
* [Connect service group members with REST API](create-service-group-member-rest-api.md)

