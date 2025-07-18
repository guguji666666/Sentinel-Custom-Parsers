---
title: Cluster Template Reference - Environment
description: Environment reference for cluster templates for use with Azure CycleCloud
author: adriankjohnson
ms.date: 06/29/2025
ms.author: adjohnso
ms.service: azure-cyclecloud
ms.custom: compute-evergreen
---

# Environment

Environment is rank 1. An environment corresponds to a [Resource Manager Deployment](/azure/azure-resource-manager/resource-group-template-deploy).

CycleCloud can now manage Azure Resource Manager deployments with ARM templates. You can refer to these environments from within CycleCloud template objects.

While environment is rank 1, a cluster object is required in the cluster template file.

## Example

```ini
[environment vnet]
  ManagedLifecycle=true
  TemplateURL = az://mystorageaccount/mycontainer/${ProjectVersion}/vnet.json
  ParameterValues.backendIpAddress1 = 10.0.1.4
  VariableOverrides.virtualNetworkName = azure-vnet

[environment appgateway]
  TemplateURL = https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-application-gateway-waf/azuredeploy.json
  ParameterValues.virtualNetworkName = ${vnet.Parameters.virtualNetworkName}

[environment existing]
  Azure.ResourceGroup = existingrg
  ManagedLifecycle = false

[cluster my-cluster]
```

The `$` is a reference to a parameter name. The `${}` is another way of referencing a parameter name and allows reference to an environment.

This example launches the ARM template existing at _az://mystorageaccount/mycontainer/${ProjectVersion}/vnet.json_ as an ARM deployment, and provides the **Resource** and **Outputs** as nested data within the `vnet` variable.

## Attribute reference

Attribute | Type | Definition
------ | ----- | ----------
Credentials | String | Name of cloud provider account
Region | String | Azure location, such as westus2
TemplateURL | String | Valid URL for ARM template location on the web. Use only one Template* attribute.
TemplateContents | String | Template JSON read in as a string with `@parametername` reference. Use only one Template* attribute.
TemplatePath | String | For use with Locker. Appends path to locker for ARM template location. Use only one Template* attribute.
Locker | String | For use with TemplatePath. Supports pulling ARM template from locker.
ParameterValues. | ARM Parameter | ParameterValues.my-parameter where my-parameter is a parameter. Parameters in ARM templates support string, list, integers, boolean.
VariableOverrides. | ARM Variable | VariableOverrides.my-variable where my-variable is a variable name in the ARM template. Variables in ARM templates support string, list, integers, boolean.
`ParameterizeVariables` | Boolean | Use with `VariableValues`. Expose ARM template variables in cluster UI menu and cluster template.
`VariableValues` | ARM Variable | Use `VariableValues.my-variable`. Alternative to `VariableOverrides`. Use with `ParameterizeVariables`.
`Azure.ResourceGroup` | String | Name of Azure Resource Group for deployment.
`ManagedLifecycle` | Boolean | Use with existing deployment. Default is true.
`Name` | String | Name of pre-exiting resource group.
`Tags` | String | Use `tags.my-tag = my-tag-value` to add tags to the resource group possessing the deployment in addition to the tags assigned by CycleCloud by default.

For pre-existing deployments, the environment object name refers to the ARM deployment name.

## Using environment resources and outputs

```ini
[environment vnet]
  ManagedLifecycle=true
  TemplateURL = az://mystorageaccount/mycontainer/${ProjectVersion}/vnet.json

[cluster my-cluster]
    [[node proxy]]
        IsReturnProxy = True
        SubnetId = ${vnet.resources.'azure-vnet/ProxySubnet'.id}
```

Following the ARM deployment model, environments create resources and expose those resources to the other Cluster Template objects for use.

Using the `${}` notation, you can refer to created ARM resources in their native schema.

Attribute | Definition
------ | ----- | ----------
Outputs. | Use as `${environment-name.Outputs.my-output}` in template where `my-output` is the name of an output in the ARM template.
Resources. | Use as `${environment-name.Resources.my-resource-name.key1.key2}` in template where `my-resource-name` is the name of a resource in the ARM template and `key1`, `key2` are related keys in the resource object.

### Referring to nested resources

CycleCloud represents environments with a nested data structure. You often need to reference data within this structure.

```ini
[environment db]
TemplateContents = @raw-db-json
ParameterValues.DBName = @DBNameParameter

[cluster my-cluster]
  [[node my-node]]

     SubnetId = ${network.resources.'vnet/ComputeSubnet'.id}

     [[[configuration database]]]
        connection_string = ${db.Outputs.JDBCConnectionString}
        database_id = ${db.resources[ClusterName].id}
```

The indices of nested variables can depend on the ARM resource type you're creating. The following formats are all valid for references to nested variables: `env.resources.my-resource-name.id`, `env.resources['my-resource-name'].id`, and `env.resources[MyResourceParam]`.
