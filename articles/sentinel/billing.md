---
title: Plan costs and understand pricing and billing
titleSuffix: Microsoft Sentinel
description: Learn how to plan your Microsoft Sentinel costs, and understand pricing and billing using the pricing calculator and other methods.
author: EdB-MSFT
ms.author: edbaynash
ms.custom: subject-cost-optimization
ms.topic: concept-article
ms.date: 07/09/2025
ms.collection: usx-security
appliesto:
    - Microsoft Sentinel in the Microsoft Defender portal
    - Microsoft Sentinel in the Azure portal


#Customer intent: As a SOC manager, I want to understand Microsoft Sentinel's pricing and billing models so that I can optimize costs and accurately forecast expenses.

---

# Plan costs and understand Microsoft Sentinel pricing and billing

As you plan your Microsoft Sentinel deployment, you typically want to understand its pricing and billing models to optimize your costs. Microsoft Sentinel's security analytics data is stored in an Azure Monitor Log Analytics workspace. Billing is based on the volume of data *analyzed* in Microsoft Sentinel and *stored* in the Log Analytics workspace. The cost of both is combined in a simplified pricing tier. Learn more about the [simplified pricing tiers](#simplified-pricing-tiers) or learn more about [Microsoft Sentinel pricing](https://azure.microsoft.com/pricing/details/microsoft-sentinel/) in general.

To help estimate your Microsoft Sentinel expected costs, [contact a Security sales specialist](https://info.microsoft.com/ww-landing-microsoft-defender-contact-me.html) for more information on pricing or to request a quote.

Costs for Microsoft Sentinel are only a portion of the monthly costs in your Azure bill. Although this article explains how to plan costs and understand the billing for Microsoft Sentinel, you're billed for all Azure services and resources your Azure subscription uses, including Partner services.

This article is part of the [Deployment guide for Microsoft Sentinel](deploy-overview.md).

[!INCLUDE [unified-soc-preview](includes/unified-soc-preview.md)]

## Free trial

Enable Microsoft Sentinel on an Azure Monitor Log Analytics workspace and the first 10 GB/day ingested using the Analytics logs plan is free for 31 days. The cost for both Log Analytics data ingestion and Microsoft Sentinel analysis charges up to the 10 GB/day limit, are waived during the 31-day trial period. This free trial is subject to a 20 workspace limit per Azure tenant.

See the [Microsoft Sentinel pricing](https://azure.microsoft.com/pricing/details/azure-sentinel) page for information on how usage beyond these limits is charged. Charges related to extra capabilities for [automation](automation.md) and [bring your own machine learning](bring-your-own-ml.md) are still applicable during the free trial, as well as any Microsoft Sentinel data lake related charges.

During your free trial, find resources for cost management, training, and more on the [**News & guides > Free trial**](https://portal.azure.com/#view/Microsoft_Azure_Security_Insights/MainMenuBlade/~/NewsAndGuides) tab in Microsoft Sentinel on the Azure portal. This tab also displays details about the dates of your free trial, and how many days left until the trial expires.

## Understand the full billing model for Microsoft Sentinel

Microsoft Sentinel offers a flexible and predictable pricing model. For more information, see the [Microsoft Sentinel pricing page](https://azure.microsoft.com/pricing/details/azure-sentinel/). Workspaces older than July 2023 might have Log Analytics workspace charges separate from Microsoft Sentinel in a classic pricing tier. For the related Log Analytics charges, see [Azure Monitor Log Analytics pricing](https://azure.microsoft.com/pricing/details/log-analytics/).

Microsoft Sentinel runs on Azure infrastructure that accrues costs when you deploy new resources. It's important to understand that there could be other, extra infrastructure costs that might accrue.

### How you're charged for Microsoft Sentinel

Pricing is based on the tier that the data is ingested into. For more information on tiers and plans, see [Data tiers in Microsoft Sentinel](log-plans.md).

#### Analytics tier

There are two ways to pay for the analytics tier: **Pay-As-You-Go** and **Commitment Tiers**.

- **Pay-As-You-Go** is the default model, based on the actual data volume stored and optionally for data retention beyond 90 days. Data volume is measured in GB (10<sup>9</sup> bytes).

- Log Analytics and Microsoft Sentinel have **Commitment Tier** pricing, formerly called Capacity Reservations. These pricing tiers are combined into simplified pricing tiers that are more predictable and offer substantial savings compared to **Pay-As-You-Go** pricing.

    **Commitment Tier** pricing starts at 100 GB per day. Any usage above the commitment level is billed at the Commitment tier rate you selected. For example, a Commitment tier of **100 GB per day** bills you for the committed 100 GB data volume, plus any extra GB/day at the discounted effective rate for that tier. The **Effective Per GB Price** is simply the **Microsoft Sentinel Price** divided by the **Tier** GB per day quantity. For more information, see [Microsoft Sentinel pricing](https://azure.microsoft.com/pricing/details/microsoft-sentinel/).

    Increase your Commitment tier anytime to optimize costs as your data volume increases. Lowering the Commitment tier is only allowed every 31 days. To see your current Microsoft Sentinel pricing tier, select **Settings** in Microsoft Sentinel, and then select the **Pricing** tab. Your current pricing tier is marked as **Current tier**.

    To set and change your Commitment tier, see [Set or change pricing tier](billing-reduce-costs.md#set-or-change-pricing-tier). Switch any workspaces older than July 2023 to the simplified pricing tiers experience to unify billing meters. Or, continue to use the classic pricing tiers that separate out the Log Analytics pricing from the classic Microsoft Sentinel classic pricing. For more information, see [simplified pricing tiers](#simplified-pricing-tiers).

<a name=auxiliary-logs-and-basic-logs></a>

#### Data lake tier

The data lake tier is a cost-effective option for ingesting secondary security data. They're charged at a flat, low rate per GB. The data lake tier provides querying and jobs scheduling capabilities and once enabled, mirrors all data available in the analytics tier.

For more information, see [Microsoft Sentinel data lake](datalake/sentinel-lake-overview.md)  

The data lake tier incurs charges based on usage of various data like capabilities. 
- **Data lake ingestion** charges are incurred per GB of data ingested for tables in Lake only mode.
- **Data lake storage** charges are incurred per GB per month for any data stored beyond the interactive retention period or in Lake only mode.
- **Data lake query** charges are incurred per GB of data analyzed using data lake exploration KQL queries, KQL jobs, or Search.
- **Advanced data insights** charges are incurred per compute hour used when using data lake exploration notebook sessions or running data lake exploration notebook jobs. Compute hours are calculated by multiplying the number of cores in the pool selected for the notebook with the amount of time a session was active or a job was running.

Once onboarded, usage from Microsoft Sentinel workspaces begins to be billed through the above described meters rather than existing long-term retention (formerly known as Archive), search or auxiliary logs ingestion meters.

>[!IMPORTANT]
>While in preview, once onboarded to the Microsoft Sentinel data lake, billing through new meters is billed at the respective meters' list rate. Pricing from previous meters doesn't carry over. For more details on pricing, see [Microsoft Sentinel pricing](https://azure.microsoft.com/pricing/details/microsoft-sentinel/).
>Existing customers that are currently billed for Auxiliary logs ingestion, long-term retention and search, will see charges transition to the new data lake ingestion, data lake storage and data lake query meters respectively.

For customers that haven't onboarded to the Microsoft Sentinel data lake and are currently using Auxiliary or Basic logs, see [Manage data retention in a Log Analytics workspace](/azure/azure-monitor/logs/data-retention-archive) and [Azure Monitor pricing](https://azure.microsoft.com/pricing/details/monitor/) for relevant information.

##### Preview specific considerations for data lake tier metering

The following are considerations that are going to apply during preview:
- Data lake ingestion includes 30-days of retention for data ingested directly into the lake.
- While long-term retention billing will transition to the data lake storage meter for existing customers, data in long-term retention will initially only be accessible through **Microsoft Sentinel > Data lake exploration > Search & restore**.
- Charges for data lake usage will show in part under a workspace resource and in part under a SentinelPlatformServices resource.
- Data written to analytics tier using jobs will be charged through the analytics ingestion meter.
  
### Simplified pricing tiers

Simplified pricing tiers combine the data analysis costs for Microsoft Sentinel and ingestion storage costs of Log Analytics into a single pricing tier. The following screenshot shows the simplified pricing tier that all new workspaces use.

:::image type="content" source="media/billing/simplified-pricing-tier.png" alt-text="Screenshot shows simplified pricing tier." lightbox="media/billing/simplified-pricing-tier.png":::

Switch any workspace configured with classic pricing tiers to the simplified pricing tiers. For more information on how to **Switch to new pricing**, see [Enroll in a simplified pricing tier](enroll-simplified-pricing-tier.md).

Combining the pricing tiers offers a simplification to the overall billing and cost management experience. This includes visualization in the pricing page, and fewer steps estimating costs in the Azure calculator. To add further value to the new simplified tiers, the current Microsoft Defender for Servers P2 benefit granting 500 MB of security data ingestion into Log Analytics is extended to the simplified pricing tiers. This change greatly increases the financial benefit of bringing eligible data ingested into Microsoft Sentinel for each virtual machine (VM) protected in this manner. For more information, see [FAQ - Microsoft Defender for Servers P2 benefit granting 500 MB](/azure/defender-for-cloud/faq-defender-for-servers#is-the-500-mb-of-free-data-ingestion-allowance-applied-per-workspace-or-per-machine-).

### Understand your Microsoft Sentinel bill

Billable meters are the individual components of your service that appear on your bill and are shown in Microsoft Cost Management. At the end of your billing cycle, the charges for each meter are summed. Your bill or invoice shows a section for all Microsoft Sentinel costs. There's a separate line item for each meter.

To see your Azure bill, select **Cost Analysis** in the left navigation of **Cost Management**. On the **Cost analysis** screen, find and select the **Invoice details** from **All views**. To understand the access level required to view billing information, see [Manage access to billing information for Azure](/azure/cost-management-billing/manage/manage-billing-access).

The costs shown in the following image are for example purposes only. They're not intended to reflect actual costs. Starting July 1, 2023, legacy pricing tiers are prefixed with **Classic**. 

:::image type="content" source="media/billing/sample-bill-classic.png" alt-text="Screenshot showing the Microsoft Sentinel section of a sample Azure bill, to help you estimate costs." lightbox="media/billing/sample-bill-classic.png":::

Microsoft Sentinel and Log Analytics charges might appear on your Azure bill as separate line items based on your selected pricing plan. Simplified pricing tiers are represented as a single `sentinel` line item for the pricing tier. Ingestion and analysis are billed on a daily basis. If your workspace exceeds its Commitment tier usage allocation in any given day, the Azure bill shows one line item for the Commitment tier with its associated fixed cost, and a separate line item for the cost beyond the Commitment tier, billed at the same effective Commitment tier rate. 

# [Simplified](#tab/simplified)
The following tabs show how Microsoft Sentinel costs appear in the **Service name** and **Meter** columns of your Azure bill depending on your simplified pricing tier. 

# [Classic](#tab/classic)
The following tabs show how Microsoft Sentinel and Log Analytics costs appear in the **Service name** and **Meter** columns of your Azure bill depending on your classic pricing tier. 

---

# [Commitment tiers](#tab/commitment-tiers/simplified)

If you're billed at the simplified Commitment tier rate, this table shows how Microsoft Sentinel costs appear in the **Service name** and **Meter** columns of your Azure bill.

| Cost description | Service name | Meter |
|--|--|--|
| Microsoft Sentinel Commitment tier | `Sentinel` | **`n` GB Commitment Tier** |
| Microsoft Sentinel Commitment tier overage | `Sentinel` | **Analysis** |

# [Commitment tiers](#tab/commitment-tiers/classic)

If you're billed at the classic Commitment tier rate, this table shows how Microsoft Sentinel and Log Analytics costs appear in the **Service name** and **Meter** columns of your Azure bill.

| Cost description | Service name | Meter |
|--|--|--|
| Microsoft Sentinel Commitment tier | `Sentinel` | **Classic `n` GB commitment tier** |
| Log Analytics Commitment tier | `Azure Monitor` | **`n` GB commitment tier** |
| Microsoft Sentinel Commitment tier overage | `Sentinel` | **Classic Analysis** |
| Log Analytics over the Commitment tier | `Log Analytics` | **Data Ingestion** |

# [Pay-as-you-go](#tab/pay-as-you-go/simplified)

If you're billed at the simplified pay-as-you-go rate, this table shows how Microsoft Sentinel costs appear in the **Service name** and **Meter** columns of your Azure bill.

| Cost description | Service name | Meter |
|--|--|--|
| Pay-as-you-go | `Sentinel` | **Pay-as-you-go Analysis** |
| Basic logs data analysis| `Sentinel` |**Basic Logs Analysis**|
| Auxiliary logs data analysis | `Sentinel` | **Auxiliary Logs Analysis** |


# [Pay-as-you-go](#tab/pay-as-you-go/classic)

If you're billed at classic pay-as-you-go rate, this table shows how Microsoft Sentinel and Log Analytics costs appear in the **Service name** and **Meter** columns of your Azure bill.

| Cost description | Service name | Meter |
|--|--|--|
| Pay-as-you-go | `Sentinel` | **Classic Pay-as-you-go Analysis** |
| Pay-as-you-go | `Log Analytics` | **Pay-as-you-go Data Ingestion** |
| Basic logs data analysis| `Sentinel` | **Classic Basic Logs Analysis** |
| Basic logs data ingestion| `Azure Monitor` | **Basic Logs Data Ingestion** |
| Auxiliary logs data analysis | `Sentinel` | **Classic Auxiliary Logs Analysis** |
| Auxiliary logs data ingestion | `Azure Monitor` | **Basic Auxiliary Data Ingestion** |


# [Free data meters](#tab/free-data-meters/simplified)

This table shows how Microsoft Sentinel and Log Analytics no charge costs appear in the **Service name** and **Meter** columns of your Azure bill for free data services when billing is at a simplified pricing tier. For more information, see [View Data Allocation Benefits](/azure/azure-monitor/cost-usage#view-data-allocation-benefits).

| Cost description | Service name | Meter |
|--|--|--|
| Microsoft Sentinel Free Trial – Sentinel Analysis | `Sentinel` | **Free trial Analysis** |
| Microsoft Defender XDR Benefit – Data Analysis | `Sentinel` | **Free Benefit - M365 Defender Analysis** |


# [Free data meters](#tab/free-data-meters/classic)

This table shows how Microsoft Sentinel and Log Analytics no charge costs appear in the **Service name** and **Meter** columns of your Azure bill for free data services when billing is at a classic pricing tier. For more information, see [View Data Allocation Benefits](/azure/azure-monitor/cost-usage#view-data-allocation-benefits).

| Cost description | Service name | Meter |
|--|--|--|
| Microsoft Sentinel Free Trial – Log Analytics data ingestion | `Azure Monitor` | **Free Benefit - Az Sentinel Trial Data Ingestion** |
| Microsoft Sentinel Free Trial – Sentinel Analysis | `Sentinel` | **Free trial Analysis** |
| Microsoft Defender XDR Benefit – Data Ingestion | `Azure Monitor` | **Free Benefit - M365 Defender Data Ingestion** |
| Microsoft Defender XDR Benefit – Data Analysis | `Sentinel` | **Free Benefit - M365 Defender Analysis** |

---

Learn how to [view and download your Azure bill](../cost-management-billing/understand/download-azure-daily-usage.md).

## Costs and pricing for other services

Microsoft Sentinel integrates with many other Azure services, including Azure Logic Apps, Azure Notebooks, and bring your own machine learning (BYOML) models. Some of these services might have extra charges. Some of Microsoft Sentinel's data connectors and solutions use Azure Functions for data ingestion, which also has a separate associated cost.

Learn about pricing for these services:

- [Automation-Logic Apps pricing](https://azure.microsoft.com/pricing/details/logic-apps/)
- [Notebooks pricing](https://azure.microsoft.com/pricing/details/machine-learning/)
- [BYOML pricing](https://azure.microsoft.com/pricing/details/machine-learning-studio/)
- [Azure Functions pricing](https://azure.microsoft.com/pricing/details/functions/)

Any other services you use might have associated costs.

## Interactive and total data retention costs

After you enable Microsoft Sentinel on a Log Analytics workspace, consider these configuration options: 

- Retain all data ingested into the workspace at no charge for the first 90 days. Retention beyond 90 days is charged per the standard [Log Analytics retention prices](https://azure.microsoft.com/pricing/details/monitor/).
- Specify different retention settings for individual data types. Learn about [retention by data type](/azure/azure-monitor/logs/data-retention-configure#configure-table-level-retention). 
- Extend retention of data with total retention so you have access to historical logs. The Microsoft Sentinel data lake, currently in preview, is a low-cost retention state for the preservation of data for such things as regulatory compliance. It's charged based on the volume of data stored and scanned. Use **Data management > Tables** to adjust the Analytics and Total retention period and learn more in [What is Microsoft Sentinel data lake?](datalake/sentinel-lake-overview.md). 
- Switch tables that contain secondary security data to **Lake tier**. This enables you to store high-volume, low-value logs at a low price, with querying capabilities built in. Use **Data management > Tables** to switch tables from **Analytics** to **Lake** tier.

## Other CEF ingestion costs

CEF is a supported Syslog events format in Microsoft Sentinel. Use CEF to bring in valuable security information from various sources to your Microsoft Sentinel workspace. CEF logs land in the CommonSecurityLog table in Microsoft Sentinel, which includes all the standard up-to-date CEF fields.

Many devices and data sources support logging fields beyond the standard CEF schema. These extra fields land in the AdditionalExtensions table. These fields could have higher ingestion volumes than the standard CEF fields, because the event content within these fields can be variable.

## Costs that might accrue after resource deletion

Removing Microsoft Sentinel doesn't remove the Log Analytics workspace Microsoft Sentinel was deployed on, or any separate charges that workspace might be incurring.

## Free data sources

The following data sources are free with Microsoft Sentinel:

- Azure Activity Logs
- Microsoft Sentinel Health 
- Office 365 Audit Logs, including all SharePoint activity, Exchange admin activity, and Teams
- Security alerts, including alerts from the following sources:
  - Microsoft Defender XDR
  - Microsoft Defender for Cloud
  - Microsoft Defender for Office 365
  - Microsoft Defender for Identity
  - Microsoft Defender for Cloud Apps
  - Microsoft Defender for Endpoint
- Alerts from the following sources:
  - Microsoft Defender for Cloud
  - Microsoft Defender for Cloud Apps

Although alerts are free, the raw logs for some Microsoft Defender XDR, Defender for Endpoint/Identity/Office 365/Cloud Apps, Microsoft Entra ID, and Azure Information Protection (AIP) data types are paid.

The following table lists the data sources in Microsoft Sentinel and Log Analytics that aren't charged. For more information, see [excluded tables](/azure/azure-monitor/logs/cost-logs#excluded-tables).

| Microsoft Sentinel data connector     | Free data type                          |
| ------------------------------------- | --------------------------------------- |
| **Azure Activity Logs**               | AzureActivity                           |
| **Health monitoring for Microsoft Sentinel** <sup>[1](#audithealthnote)</sup>   | SentinelHealth |
| **Microsoft Entra ID Protection**     | SecurityAlert (IPC)                     |
| **Office 365**                        | OfficeActivity (SharePoint)             |
|                                       | OfficeActivity (Exchange)               |
|                                       | OfficeActivity (Teams)                  |
| **Microsoft Defender for Cloud**      | SecurityAlert (Defender for Cloud)      |
| **Microsoft Defender for IoT**        | SecurityAlert (Defender for IoT)        |
| **Microsoft Defender XDR**            | SecurityIncident                        |
|                                       | SecurityAlert                           |
| **Microsoft Defender for Endpoint**   | SecurityAlert (MDATP)                   |
| **Microsoft Defender for Identity**   | SecurityAlert (AATP)                    |
| **Microsoft Defender for Cloud Apps** | SecurityAlert (Defender for Cloud Apps) |

<a id="audithealthnote">*<sup>1</sup>*</a> *For more information, see [Auditing and health monitoring for Microsoft Sentinel](health-audit.md).*

For data connectors that include both free and paid data types, select which data types you want to enable.

:::image type="content" source="media/billing/data-types.png" alt-text="Screenshot of the connector page for Defender for Cloud Apps, with the free security alerts selected and paid MCAS Shadow IT Reporting not enabled." lightbox="media/billing/data-types.png":::

Learn more about how to [connect data sources](connect-data-sources.md), including free and paid data sources.

## Learn more

- [Monitor costs for Microsoft Sentinel](billing-monitor-costs.md)
- [Reduce costs for Microsoft Sentinel](billing-reduce-costs.md)
- Learn [how to optimize your cloud investment with Microsoft Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Learn more about managing costs with [cost analysis](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Learn about how to [prevent unexpected costs](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Take the [Cost Management](/training/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) guided learning course.
- For more tips on reducing Log Analytics data volume, see [Azure Monitor best practices - Cost management](/azure/azure-monitor/best-practices-cost).

## Next steps

In this article, you learned how to plan costs and understand the billing for Microsoft Sentinel.

> [!div class="nextstepaction"]
> >[Deploy Microsoft Sentinel](deploy-overview.md)
