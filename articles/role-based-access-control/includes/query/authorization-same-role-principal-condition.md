---
author: jenniferf-skc
ms.service: resource-graph
ms.topic: include
ms.date: 01/12/2024
ms.author: jfields
ms.custom:
  - build-2025
---

```kusto
authorizationresources
| where type =~ "microsoft.authorization/roleassignments"
| where id startswith "/subscriptions"
| extend RoleDefinitionId = tolower(tostring(properties.roleDefinitionId))
| extend PrincipalId = tolower(properties.principalId)
| extend RoleDefinitionId_PrincipalId = strcat(RoleDefinitionId, "_", PrincipalId)
| extend condition = tostring(properties.condition)
| join kind = leftouter (
  authorizationresources
  | where type =~ "microsoft.authorization/roledefinitions"
  | extend RoleDefinitionName = tostring(properties.roleName)
  | extend rdId = tolower(id)
  | project RoleDefinitionName, rdId
) on $left.RoleDefinitionId == $right.rdId
| summarize count_ = count(), Scopes = make_set(tolower(properties.scope)) by RoleDefinitionId_PrincipalId,RoleDefinitionName, condition
| project RoleDefinitionId = split(RoleDefinitionId_PrincipalId, "_", 0)[0], RoleDefinitionName, PrincipalId = split(RoleDefinitionId_PrincipalId, "_", 1)[0], count_, Scopes, condition
| where count_ > 1
| order by count_ desc
```
