---
title: Azure Database for PostgreSQL server support matrix
description: Provides a summary of support settings and limitations of Azure Database for PostgreSQL server backup.
ms.topic: reference
ms.date: 04/16/2025
ms.custom: references_regions
ms.service: azure-backup
author: AbhishekMallick-MS
ms.author: v-mallicka
# Customer intent: "As a database administrator, I want to understand the support matrix for Azure Database for PostgreSQL backups, so that I can ensure compliance with regional restrictions and feature limitations when implementing our backup strategy."
---

# Azure Database for PostgreSQL server support matrix

You can use [Azure Backup](./backup-overview.md) to protect Azure Database for PostgreSQL server. This article summarizes supported regions, scenarios, and the limitations.

## Supported regions

Azure Database for PostgreSQL server backup is available in all regions, except for Germany Central (Sovereign), Germany Northeast (Sovereign) and China regions.

## Support scenarios

|Scenarios  | Details  |
|---------| ---------|
|Deployments   |  [Azure Database for PostgreSQL - Single Server](/azure/postgresql/overview#azure-database-for-postgresql---single-server)     |
|Azure PostgreSQL versions    |   9.5, 9.6, 10, 11    |

## Feature considerations and limitations

- The maximum supported database size is 400GB, which is a hard limit for a single server..
- Cross-region backup isn't supported. Therefore, you can't back up an Azure PostgreSQL server to a vault in another region. Similarly, you can only restore a backup to a server within the same region as the vault. However, we support cross-subscription backup and restore. 
- Private endpoint-enabled Azure PostgreSQL servers can be backed up by allowing trusted Microsoft services in the network settings.
- Only the data is recovered during restore; _roles_ aren't restored.

## Next steps

- [Back up Azure Database for PostgreSQL server](backup-azure-database-postgresql.md).

## Related content

- [Create a backup policy for PostgreSQL databases using REST API](backup-azure-data-protection-use-rest-api-create-update-postgresql-policy.md).
- [Configure backup for PostgreSQL databases using REST API](backup-azure-data-protection-use-rest-api-backup-postgresql.md).
- [Restore for PostgreSQL databases using REST API](restore-postgresql-database-use-rest-api.md).