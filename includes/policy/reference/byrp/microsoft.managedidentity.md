---
ms.service: azure-policy
ms.topic: include
ms.date: 07/23/2025
ms.author: jasongroce
author: jasongroce
ms.custom: generated
---

|Name<br /><sub>(Azure portal)</sub> |Description |Effect(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[Preview\]: Managed Identity Federated Credentials from Azure Kubernetes should be from trusted sources](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae62c456-33de-4dc8-b100-7ce9028a7d99) |This policy limits federeation with Azure Kubernetes clusters to only clusters from approved tenants, approved regions, and a specific exception list of additional clusters. |Audit, Disabled, Deny |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Identity/FIC_LimitToAzureKubernetesIssuer.json) |
|[\[Preview\]: Managed Identity Federated Credentials from GitHub should be from trusted repository owners](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd1a8e20-2c4f-4a6c-9354-b58d786d9a1f) |This policy limits federation with GitHub repos to only approved repository owners. |Audit, Disabled, Deny |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Identity/FIC_LimitToGitHubIssuer.json) |
|[\[Preview\]: Managed Identity Federated Credentials should be from allowed issuer types](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2571b7c3-3056-4a61-b00a-9bc5232234f5) |This policy limits whether Managed Identities can use federated credentials, which common issuer types are allowed, and provides a list of allowed issuer exceptions. |Audit, Disabled, Deny |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Identity/FIC_LimitToWellknownIssuers.json) |
