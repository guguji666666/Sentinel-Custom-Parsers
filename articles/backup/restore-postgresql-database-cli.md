---
title: Restore PostgreSQL Databases by Using the Azure CLI
description: Learn how to restore Azure Database for PostgreSQL by using the Azure CLI.
ms.topic: how-to
ms.date: 05/20/2025
ms.service: azure-backup
ms.custom:
  - devx-track-azurecli
  - build-2025
author: AbhishekMallick-MS
ms.author: v-mallicka
# Customer intent: As a database administrator, I want to restore PostgreSQL databases using the command line, so that I can efficiently manage backup and recovery operations without relying on graphical interfaces.
---

# Restore PostgreSQL databases by using the Azure CLI

This article describes how to use the Azure CLI to restore PostgreSQL databases to an [Azure Database for PostgreSQL](/azure/postgresql/overview#azure-database-for-postgresql---single-server) server that you backed up via Azure Backup. You can also restore a PostgreSQL database using [Azure portal](restore-azure-database-postgresql.md), [Azure PowerShell](restore-postgresql-database-ps.md), and [REST API](restore-postgresql-database-use-rest-api.md).

Because a PostgreSQL database is a platform as a service (PaaS) database, the Original-Location Recovery (OLR) option to restore by replacing the existing database (from where the backups were taken) isn't supported. You can restore from a recovery point to create a new database in the same Azure Database for PostgreSQL server or in any other PostgreSQL server. This option is called Alternate-Location Recovery (ALR). ALR helps to keep both the source database and the restored (new) database.

The examples in this article refer to an existing Backup vault named `TestBkpVault` under the resource group `testBkpVaultRG`.

## Restore a backed-up PostgreSQL database

### Set up permissions

A Backup vault uses a managed identity to access other Azure resources. To restore from a backup, the Backup vault's managed identity requires a set of permissions on the Azure Database for PostgreSQL server to which the database should be restored.

To assign the relevant permissions for a vault's system-assigned managed identity on the target PostgreSQL server, see the [set of permissions needed to back up a PostgreSQL database](./backup-azure-database-postgresql-overview.md#permissions-needed-for-postgresql-database-restore).

To restore the recovery point as files to a storage account, the [Backup vault's system-assigned managed identity needs access on the target storage account](./restore-azure-database-postgresql.md#restore-permissions-on-the-target-storage-account).

### Fetch the relevant recovery point

To list all backup instances within a vault, use the [`az dataprotection backup-instance list`](/cli/azure/dataprotection/backup-instance#az-dataprotection-backup-instance-list) command. Then fetch the relevant instance by using the [`az dataprotection backup-instance show`](/cli/azure/dataprotection/backup-instance#az-dataprotection-backup-instance-show) command.

Alternatively, for *at-scale* scenarios, you can list backup instances across vaults and subscriptions by using the [`az dataprotection backup-instance list-from-resourcegraph`](/cli/azure/dataprotection/backup-instance#az-dataprotection-backup-instance-list-from-resourcegraph) command:

```azurecli
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL -subscriptions "xxxxxxxx-xxxx-xxxx-xxxx"

  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
    "extendedLocation": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-aaaa0a0a-bb1b-cc2c-dd3d-eeeeee4e4e4e",
    "identity": null,
    "kind": "",
    "location": "",
    "managedBy": "",
    "name": "testpostgresql-empdb11-aaaa0a0a-bb1b-cc2c-dd3d-eeeeee4e4e4e",
    "plan": null,
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
        "resourceLocation": "westus",
        "resourceName": "postgres",
        "resourceProperties": null,
        "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceUri": ""
      },
      "dataSourceProperties": null,
      "dataSourceSetInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "objectType": "DatasourceSet",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
        "resourceLocation": "westus",
        "resourceName": "testpostgresql",
        "resourceProperties": null,
        "resourceType": "Microsoft.DBforPostgreSQL/servers",
        "resourceUri": ""
      },
      "datasourceAuthCredentials": {
        "objectType": "SecretStoreBasedAuthCredentials",
        "secretStoreResource": {
          "secretStoreType": "AzureKeyVault",
          "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
          "value": null
        }
      },
      "friendlyName": "testpostgresql\\empdb11",
      "objectType": "BackupInstance",
      "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
        "policyParameters": null,
        "policyVersion": ""
      },
      "protectionErrorDetails": null,
      "protectionStatus": {
        "errorDetails": null,
        "status": "ProtectionConfigured"
      },
      "provisioningState": "Succeeded",
      "validationType": null
    },
    "protectionState": "ProtectionConfigured",
    "resourceGroup": "testBkpVaultRG",
    "sku": null,
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "tags": null,
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "microsoft.dataprotection/backupvaults/backupinstances",
    "vaultName": "testBkpVault",
    "zones": null
  }
.
.
.
.
.
```

After you identify the instance, fetch the relevant recovery point by using the [`az dataprotection recovery-point list`](/cli/azure/dataprotection/recovery-point#az-dataprotection-recovery-point-list) command:

```azurecli
az dataprotection recovery-point list --backup-instance-name testpostgresql-empdb11-aaaa0a0a-bb1b-cc2c-dd3d-eeeeee4e4e4e -g testBkpVaultRG --vault-name TestBkpVault

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-aaaa0a0a-bb1b-cc2c-dd3d-eeeeee4e4e4e/recoveryPoints/9da55e757af94261afa009b43cd3222a",
  "name": "9da55e757af94261afa009b43cd3222a",
  "properties": {
    "friendlyName": "2031fdb43a914114b6ce644eb6fcb5ce",
    "objectType": "AzureBackupDiscreteRecoveryPoint",
    "policyName": "oss-clitest-policy",
    "policyVersion": null,
    "recoveryPointDataStoresDetails": [
      {
        "creationTime": "2021-09-13T15:17:41.209845+00:00",
        "expiryTime": null,
        "id": "beddea84-7b30-42a5-a752-7c75baf96a52",
        "metaData": "{\"objectType\":\"PostgresBackupMetadata\",\"version\":\"1.0\",\"postgresVersion\":\"11\",\"dbName\":\"postgres\",\"serverName\":\"testpostgresql\",\"serverFQDN\":\"testpostgresql.postgres.database.azure.com\",\"usernameUsed\":\"backupadmin@testpostgresql\",\"backupToolPath\":\"postgresql-11.6-1\\\\bin\\\\pg_dump.exe\",\"backupType\":\"Full\",\"backupDumpFormat\":\"CUSTOM\",\"backupToolArgsFormat\":\"--no-acl --no-owner --serializable-deferrable --no-tablespaces --quote-all-identifiers -Fc -d postgres://{0}:{1}@{2}:5432/{3}?sslmode=verify-full&sslrootcert=E:\\\\approot\\\\Plugins\\\\Postgres\\\\..\\\\..\\\\postgres-root.crt\",\"storageUnits\":{\"1\":\"DbBackupDumpData\"},\"streamNamesInFirstStorageUnit\":[\"dbbkpdmpdatastream-1631546260050\"],\"pitId\":\"2031fdb43a914114b6ce644eb6fcb5ce\",\"bytesTransferred\":2063,\"dataSourceSize\":8442527,\"backupToolVersion\":\"11\"}",
        "rehydrationExpiryTime": null,
        "rehydrationStatus": null,
        "state": "COMMITTED",
        "type": "VaultStore",
        "visible": true
      }
    ],
    "recoveryPointId": "9da55e757af94261afa009b43cd3222a",
    "recoveryPointTime": "2021-09-13T15:17:41.209845+00:00",
    "recoveryPointType": "Full",
    "retentionTagName": "default",
    "retentionTagVersion": "637671427933449525"
  },
  "resourceGroup": "testBkpVaultRG",
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints"
}
```

If you need to fetch the recovery point from the archive tier, the `type` variable in `recoveryPointDataStoreDetails` is `ArchiveStore`.

### Prepare the restore request

There are various restore options for a PostgreSQL database. You can restore the recovery point as another database or restore as files. The recovery point can also be on the archive tier.

#### Restore as a database

Construct the Azure Resource Manager ID of the new PostgreSQL database by using the [target PostgreSQL server to which you assigned permissions](#set-up-permissions). Also, construct the required PostgreSQL database name. For example, a PostgreSQL database can be named `emprestored21` under a target PostgreSQL server named `targetossserver` in the resource group `targetrg` with a different subscription:

```azurecli
$targetOssId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21"
```

Use the [`az dataprotection backup-instance restore initialize-for-data-recovery`](/cli/azure/dataprotection/backup-instance/restore#az-dataprotection-backup-instance-restore-initialize-for-data-recovery) command to prepare the restore request with all relevant details:

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore VaultStore --target-resource-id $targetOssId --recovery-point-id 9da55e757af94261afa009b43cd3222a --secret-store-type AzureKeyVault --secret-store-uri "https://restoreoss-test.vault.azure.net/secrets/dbauth3" > OssRestoreReq.JSON
```

For an archive-based recovery point, you need to:

1. Rehydrate from the archive datastore to the vault datastore.
1. Modify the source datastore.
1. Add other parameters to specify the rehydration priority.
1. Specify the duration for which the rehydrated recovery point should be retained in the vault datastore.
1. Restore as a database from this recovery point.

Use the following command to prepare the request for all the previously mentioned operations at once:

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore ArchiveStore --target-resource-id $targetOssId --recovery-point-id 9da55e757af94261afa009b43cd3222a --secret-store-type AzureKeyVault --secret-store-uri "https://restoreoss-test.vault.azure.net/secrets/dbauth3" --rehydration-priority Standard --rehydration-duration 12 > OssRestoreFromArchiveReq.JSON
```

#### Restore as files

Fetch the URI of the container within the storage account [to which you assigned permissions](#set-up-permissions). The following example uses a container named `testcontainerrestore` under a storage account named `testossstorageaccount` with a different subscription:

```azurecli
$contURI = "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore"
```

Use the [`az dataprotection backup-instance restore initialize-for-data-recovery-as-files`](/cli/azure/dataprotection/backup-instance/restore#az-dataprotection-backup-instance-restore-initialize-for-data-recovery-as-files) command to prepare the restore request with all relevant details:

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery-as-files --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore VaultStore -target-blob-container-url $contURI --target-file-name "empdb11_postgresql-westus_1628853549768" --recovery-point-id 9da55e757af94261afa009b43cd3222a > OssRestoreAsFilesReq.JSON
```

For the archive-based recovery point, in the following script:

- Modify the source datastore.
- Add the rehydration priority and the retention duration, in days, of the rehydrated recovery point.

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery-as-files --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore ArchiveStore -target-blob-container-url $contURI --target-file-name "empdb11_postgresql-westus_1628853549768" --recovery-point-id 9da55e757af94261afa009b43cd3222a --rehydration-priority Standard --rehydration-duration 12 > OssRestoreAsFilesReq.JSON
```

To validate if the JSON file will succeed in creating new resources, use the [`az dataprotection backup-instance validate-for-restore`](/cli/azure/dataprotection/backup-instance#az-dataprotection-backup-instance-validate-for-restore) command.

### Trigger the restore

Use the [`az dataprotection backup-instance restore trigger`](/cli/azure/dataprotection/backup-instance/restore#az-dataprotection-backup-instance-restore-trigger) command to trigger the restore operation with the previously prepared request:

```azurecli-interactive
az dataprotection backup-instance restore trigger -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name testpostgresql-empdb11-aaaa0a0a-bb1b-cc2c-dd3d-eeeeee4e4e4e --restore-request-object OssRestoreReq.JSON
```

## Track jobs

Track jobs by using the [`az dataprotection job list`](/cli/azure/dataprotection/job#az-dataprotection-job-list) command. You can list all jobs and fetch a particular job detail.

You can also use `Az.ResourceGraph` to track jobs across all Backup vaults. Use the [`az dataprotection job list-from-resourcegraph`](/cli/azure/dataprotection/job#az-dataprotection-job-list-from-resourcegraph) command to get the relevant job that's across all Backup vaults:

```azurecli
az dataprotection job list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL --operation Restore
```

## Related content

- [What is Azure Database for PostgreSQL backup?](backup-azure-database-postgresql-overview.md)
