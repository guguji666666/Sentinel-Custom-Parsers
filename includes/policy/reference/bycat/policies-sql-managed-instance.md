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
|[Customer managed key encryption must be used as part of CMK Encryption for Arc SQL managed instances.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F413923f0-ff16-41ae-8583-90c5c5d9fa8f) |As a part of CMK encryption, Customer managed key encryption must be used. Learn more at [https://aka.ms/EnableTDEArcSQLMI](https://aka.ms/EnableTDEArcSQLMI). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL%20Managed%20Instance/Arc_SQLMI_TdeEnabledInCMKMode_Audit.json) |
|[TLS protocol 1.2 must be used for Arc SQL managed instances.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb3c7464-033e-41ee-81dc-480fde675b20) |As a part of network settings, Microsoft recommends allowing only TLS 1.2 for TLS protocols in SQL Servers. Learn more on network settings for SQL Server at [https://aka.ms/TlsSettingsSQLServer](https://aka.ms/TlsSettingsSQLServer). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL%20Managed%20Instance/Arc_SQLMI_TLS1.2IsUsed_Audit.json) |
|[Transparent Data Encryption must be enabled for Arc SQL managed instances.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6599ab01-29bc-4852-a6f5-de9e2151714a) |Enable transparent data encryption (TDE) at-rest on an Azure Arc-enabled SQL Managed Instance. Learn more at [https://aka.ms/EnableTDEArcSQLMI](https://aka.ms/EnableTDEArcSQLMI). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL%20Managed%20Instance/Arc_SQLMI_TdeEnabled_Audit.json) |
