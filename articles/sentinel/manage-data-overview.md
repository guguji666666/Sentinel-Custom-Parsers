---
title: Manage data tiers and retention in Microsoft Sentinel (preview)
description: Manage log data in Microsoft Sentinel and with Microsoft Defender XDR services in the Microsoft Defender portal to optimize security operations and cost efficiency.
ms.reviewer: dzatakovi
ms.author: guywild
author: guywi-ms
ms.topic: conceptual
ms.date: 07/16/2025
# Customer intent: As an Microsoft Defender Portal administrator or subscription owner, I want to configure log table tiers and data retention settings to optimize security operations needs and cost efficiency.
---

# Manage data tiers and retention in Microsoft Sentinel (preview)

Data you collect into Microsoft Sentinel (SIEM) and Microsoft Defender XDR is stored in tables. The Microsoft Defender portal lets you manage the retention period and the store costs associated with your data. You can manage retention and costs when you:

- [Configure data connectors](configure-data-connector.md) to send data to Microsoft Sentinel or Microsoft Defender XDR.
- [Manage your existing tables and data](manage-table-tiers-retention.md).

This article explains how to manage table retention and tier options in the Microsoft Defender portal to optimize security operations and reduce costs in Microsoft Sentinel and Microsoft Defender XDR.

## Which tables can you manage in the Defender portal?

This section describes the table types you can manage in the Defender portal.

:::image type="content" source="media/manage-data-overview/view-table-properties-microsoft-defender-portal.png" lightbox="media/manage-data-overview/view-table-properties-microsoft-defender-portal.png" alt-text="Screenshot that shows the Table Management screen in the Defender portal."::: 

| Table type                      | Description                                                                                                    | Examples                                             | Is in Microsoft Sentinel workspace?         |
|----------------------------------|----------------------------------------------------------------------------------------------------------------|------------------------------------------------------|----------------------------------|
| **Microsoft Sentinel**    | Built-in tables, including:<br>- Azure tables, such as AzureDiagnostics and SigninLogs.<br>- Microsoft Sentinel tables.<br>- [Supported Defender XDR advanced hunting tables](#preview-limitations), which are created in your Microsoft Sentinel workspace when you increase the retention period beyond 30 days. See the **XDR** table type for Defender XDR tables that are currently unsupported.             | - Azure tables: `AzureDiagnostics`, `SigninLogs`<br>- Microsoft Sentinel tables: `AWSCloudTrail`, `SecurityAlert`<br>- XDR tables: `DeviceEvents`,<br>`AlertInfo`                 | Yes                              |
| **Custom**                | Tables you create manually or through jobs in your Microsoft Sentinel workspace, including summary rule and search job results tables, and custom data source tables. | Tables with `_CL` or `_SRCH` suffixes.                                                      | Yes                              |
| **XDR**| Tables in the XDR default tier, which have 30 days of analytics retention by default. You can view these tables, but you can't manage them from the Defender portal.                                                                       | `IdentityInfo` | No |

> [!NOTE]
> You can view Basic logs tables in your Microsoft Sentinel workspace from the Defender portal, but you can only currently manage them from your Log Analytics workspace. To manage these tables from the Defender portal, change the table plan from basic to analytics in your Microsoft Sentinel workspace.
 
## How data tiers and retention work

You can retain data in Microsoft Sentinel in one of two tiers: 

* **Analytics tier**: This tier makes data available for alerting, hunting, workbooks, and all Microsoft Sentinel features. It retains data in two states:

  - **Analytics retention**: In this "hot" state, data is fully available for real-time analytics - including high-performance queries and analytics rules - and threat hunting. By default, Microsoft Sentinel and Microsoft Defender XDR retain data in this tier for 30 days. You can extend the retention period of all tables to up to two years at a prorated monthly long-term retention charge. You can extend the retention period of Microsoft Sentinel solution tables to 90 days for free. 
  - **Total retention**: By default, all data in the analytics tier is mirrored to the data lake for the same retention period. You can extend the retention of your data in the lake beyond the analytics retention, for up to 12 years of total retention at a low cost. 

* **Data lake tier**: In this low-cost "cold" tier, Microsoft Sentinel retains your data in the lake only.  Data in the data lake tier isn't available for real-time analytics features and threat hunting. However, you can access data in the lake whenever you need it through [KQL jobs](datalake/kql-jobs.md), analyze trends over time by running scheduled KQL or Spark jobs, and aggregate insights from incoming data at a regular cadence by using summary rules. 

For more information about the differences between these two retention types, see [Compare the analytics and data lake tiers](#compare-the-analytics-and-data-lake-tiers).

By default, Microsoft Defender XDR retains threat hunting data in the **XDR default tier**, which includes 30 days of analytics retention, included in the XDR license. You can extend the retention period of [supported Defender XDR tables](#preview-limitations) beyond 30 days. When you extend the retention period of supported Microsoft Defender XDR tables Microsoft automatically creates the table in your Microsoft Sentinel workspace in the analytics tier.

This diagram shows the retention components of the analytics, data lake, and XDR default tiers, and which table types apply to each tier:

:::image type="content" source="media/manage-data-overview/tiers-retention-defender-portal.png" lightbox="media/manage-data-overview/tiers-retention-defender-portal.png" alt-text="Diagram that depicts the analytics and data lake tiers in the Microsoft Defender portal.":::

For more information about the Microsoft Sentinel data lake, see [What is Microsoft Sentinel data lake](datalake/sentinel-lake-overview.md).

## Compare the analytics and data lake tiers

This table compares the two analytics and data lake tiers and their key characteristics:

| Comparison                                               | Analytics tier                                                  | Data lake tier                           |
| ------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
|Key characteristics                                   | High-performance querying and indexing for logs (also known as hot, or interactive retention). | Cost-effective long-term retention of large data volumes (also known as cold storage). |
| Best for                                               | Real-time analytics rules, alerting, hunting, workbooks, and all Microsoft Sentinel features. |  - Compliance and regulatory logging.<br>- Historical trend analysis and forensics.<br>- Low-touch data that's not needed for real-time alerts. |
| Ingestion cost                                         | Standard                                                     | Minimal                                                      | 
| Query price included                                   | ✅                                                           | ❌                                                            | 
| Optimized query performance                            | ✅                                                           | ❌ Slower queries.<br>Good for auditing. Not optimized for real-time analysis. |
| Query capabilities                                     | [Full query capabilities](/azure/azure-monitor/logs/get-started-queries) in the Microsoft Defender and Azure portals and using APIs.    | - [Full KQL on a single table](/azure/azure-monitor/logs/basic-logs-query), which you can extend with data from an analytics table using [lookup](/azure/data-explorer/kusto/query/lookup-operator).<br>- Run scheduled KQL or Spark jobs.<br>- Use Notebooks. |
| Full set of real-time analytics features | ✅                                                           | ❌  Limitations on some features, including analytics rules, hunting queries, parsers, watchlists, workbooks, and playbooks.                                                           |
| [Search jobs](investigate-large-datasets.md)                  | ✅                                                           | ✅                                                            |
| [Summary rules](summary-rules.md)              | ✅                                                           | ✅ KQL limited to a single table                              |
| [Restore](restore.md)                          | ✅                                                           | ❌                                                            |
| [Data export](/azure/azure-monitor/logs/logs-data-export)                     | ✅                                                           | ❌                                                            |
| Retention period                                  | 90 days for Microsoft Sentinel, 30 days for Microsoft Defender XDR.<br> Can be extended to up to two years at a prorated monthly long-term retention charge. | Same as analytics retention, by default. Can be extended to up to 12 years. |

## What happens when you modify table settings

You can switch a table's tier and retention settings at any time. 

When you change a table's tier from analytics to data lake, all real-time analytics and hunting queries stop working. 

When you shorten a table's total retention, Microsoft waits 30 days before removing the data, so you can revert the change and avoid data loss if you made an error in configuration. 

When you increase total retention, the new retention period applies to all data that was already ingested into the table and wasn't yet removed.   

When you change the analytics retention settings of a table with existing data, the change takes effect immediately. 

***Example***: 

- You have a table in the analytics tier with 180 days of analytics retention. By default, the Total retention is also set to 180 days. 
- You change the analytics retention to 90 days without changing the Total retention period of 180 days. 
- Microsoft Sentinel removes the last 90 days of data from in analytics retention automatically, but continues to store data that's 90-180 days in the data lake.

  
## Preview limitations

As part of the public preview:

- Microsoft Sentinel tables with the Basic plan can only be managed from the Log Analytics workspace. For more information, see [Manage tables in a Log Analytics workspace](/azure/azure-monitor/logs/manage-logs-tables). 

  To manage retention and tiering from the Microsoft Defender portal, change the table plan to analytics from the Log Analytics workspace.
 
- Some Microsoft Defender XDR tables can only be viewed in the Microsoft Defender portal. Currently, the Microsoft Defender portal supports managing these Microsoft Defender XDR tables:

  - AlertEvidence
  - AlertInfo
  - CampaignInfo
  - CloudAppEvents
  - DeviceEvents
  - DeviceFileCertificateInfo
  - DeviceFileEvents
  - DeviceImageLoadEvents
  - DeviceInfo
  - DeviceLogonEvents
  - DeviceNetworkEvents
  - DeviceNetworkInfo
  - DeviceProcessEvents
  - DeviceRegistryEvents
  - EmailAttachmentInfo
  - EmailEvents
  - EmailPostDeliveryEvents
  - EmailUrlInfo
  - FileMaliciousContentInfo
  - IdentityDirectoryEvents
  - IdentityLogonEvents
  - IdentityQueryEvents
  - SecurityAlert
  - SecurityIncident
  - UrlClickEvents

## Next steps

Learn more about:

- [Configuring log table tiers and retention settings](manage-table-tiers-retention.md)
- [Creating a search job to retrieve data matching particular criteria](investigate-large-datasets.md)

