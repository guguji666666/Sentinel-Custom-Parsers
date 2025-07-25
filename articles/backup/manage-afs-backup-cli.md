---
title: Manage Azure Files backups with the Azure CLI
description: Learn how to manage and monitor the backed-up Azure Files using Azure CLI.
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.date: 05/22/2025
author: AbhishekMallick-MS
ms.author: v-mallicka

# Customer intent: "As a cloud administrator, I want to manage and monitor Azure Files backups using the command line, so that I can automate backup processes and ensure data protection efficiently."
---

# Manage Azure Files backups with the Azure CLI

This article describes how to manage and monitor the backed-up Azure Files ([snapshot](azure-file-share-backup-overview.md?tabs=snapshot) and [vaulted](azure-file-share-backup-overview.md?tabs=vault-standard) backups) using Azure CLI. The Azure CLI provides a command-line experience for managing Azure resources. It's a great tool for building custom automation to use Azure resources. You can also manage Azure Files backups using [Azure portal](manage-afs-backup.md), [Azure PowerShell](manage-afs-powershell.md), [REST API](manage-azure-file-share-rest-api.md).

## Prerequisites

This article assumes you already have an Azure Files backed up by [Azure Backup](./backup-overview.md). If you don't have one, see [Back up Azure Files with the CLI](backup-afs-cli.md) to configure backup for your File Shares. For this article, you use the following resources:
   -  **Resource group**: `azurefiles`
   -  **RecoveryServicesVault**: *azurefilesvault*
   -  **Storage Account**: *afsaccount*
   -  **File Share**: `azurefiles`
  
  [!INCLUDE [azure-cli-prepare-your-environment-no-header.md](~/reusable-content/azure-cli/azure-cli-prepare-your-environment-no-header.md)]
   - This tutorial requires version 2.0.18 or later of the Azure CLI. If using Azure Cloud Shell, the latest version is already installed.

## Monitor jobs

When you trigger backup or restore operations, the backup service creates a job for tracking. To monitor completed or currently running jobs, use the [az backup job list](/cli/azure/backup/job#az-backup-job-list) cmdlet. With the CLI, you also can [suspend a currently running job](/cli/azure/backup/job#az-backup-job-stop) or [wait until a job finishes](/cli/azure/backup/job#az-backup-job-wait).

The following example displays the status of backup jobs for the *azurefilesvault* Recovery Services vault:

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```
## Create a Backup policy

Azure Backup policy for Azure Files defines how and when backups are created, the retention period for recovery points, and the rules for data protection and recovery.

**Choose a backup tier**:

# [Snapshot tier](#tab/snapshot)

You can create a backup policy by executing the [az backup policy create](/cli/azure/backup/policy#az-backup-policy-create) command with the following parameters:

- --backup-management-type – Azure Storage
- --workload-type - AzureFileShare
- --name – Name of the policy
- --policy - JSON file with appropriate details for schedule and retention
- --resource-group - Resource group of the vault
- --vault-name – Name of the vault

**Example**

```azurecli-interactive
az backup policy create --resource-group azurefiles --vault-name azurefilesvault --name schedule20 --backup-management-type AzureStorage --policy samplepolicy.json --workload-type AzureFileShare

```

**Sample JSON (samplepolicy.json)**

```json
{
  "eTag": null,
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule20",
  "location": null,
  "name": "schedule20",
  "properties": {
    "backupManagementType": "AzureStorage",
    "protectedItemsCount": 0,
    "retentionPolicy": {
      "dailySchedule": {
        "retentionDuration": {
          "count": 30,
          "durationType": "Days"
        },
        "retentionTimes": [
          "2020-01-05T08:00:00+00:00"
        ]
      },
      "monthlySchedule": null,
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": null,
      "yearlySchedule": null
    },
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunDays": null,
      "scheduleRunFrequency": "Daily",
      "scheduleRunTimes": [
        "2020-01-05T08:00:00+00:00"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "timeZone": "UTC",
    "workLoadType": “AzureFileShare”
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
}
```

**Example to create a backup policy that configures multiple backups a day**

This sample JSON is for the following requirements:

- **Schedule**: Back up *every 4 hours* starting from *8 AM (UTC)* for the *next 12 hours*.
- **Retention**: Daily - *five days*, Weekly - *Every Sunday for 12 weeks*, Monthly - *First Sunday of every month for 60 months*, and Yearly - *First Sunday of January for 10 years*.

```json
{
    "properties":{
        "backupManagementType": "AzureStorage",
        "workloadType": "AzureFileShare",
        "schedulePolicy": {
            "schedulePolicyType": "SimpleSchedulePolicy",
            "scheduleRunFrequency": "Hourly",
            "hourlySchedule": {
                "interval": 4,
                "scheduleWindowStartTime": "2021-09-29T08:00:00.000Z",
                "scheduleWindowDuration": 12
            }
        },
        "timeZone": "UTC",
        "retentionPolicy": {
            "retentionPolicyType": "LongTermRetentionPolicy",
            "dailySchedule": {
                "retentionTimes": null,
                "retentionDuration": {
                    "count": 5,
                    "durationType": "Days"
                }
            },
            "weeklySchedule": {
                "daysOfTheWeek": [
                    "Sunday"
                ],
                "retentionTimes": null,
                "retentionDuration": {
                    "count": 12,
                    "durationType": "Weeks"
                }
            },
            "monthlySchedule": {
                "retentionScheduleFormatType": "Weekly",
                "retentionScheduleDaily": null,
                "retentionScheduleWeekly": {
                    "daysOfTheWeek": [
                        "Sunday"
                    ],
                    "weeksOfTheMonth": [
                        "First"
                    ]
                },
                "retentionTimes": null,
                "retentionDuration": {
                    "count": 60,
                    "durationType": "Months"
                }
            },
            "yearlySchedule": {
                "retentionScheduleFormatType": "Weekly",
                "monthsOfYear": [
                    "January"
                ],
                "retentionScheduleDaily": null,
                "retentionScheduleWeekly": {
                    "daysOfTheWeek": [
                        "Sunday"
                    ],
                    "weeksOfTheMonth": [
                        "First"
                    ]
                },
                "retentionTimes": null,
                "retentionDuration": {
                    "count": 10,
                    "durationType": "Years"
                }
            }
        }
    }
}

```

Once the policy is created successfully, the output of the command displays the policy JSON that you passed as a parameter while executing the command.

You can modify the schedule and retention section of the policy as required.

**Example**

If you want to retain the backup of first Sunday of every month for two months, update the monthly schedule as per the following example:

```json
"monthlySchedule": {
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        },
        "retentionScheduleDaily": null,
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Sunday"
          ],
          "weeksOfTheMonth": [
            "First"
          ]
        },
        "retentionTimes": [
          "2020-01-05T08:00:00+00:00"
        ]
      }

```

# [Vault-Standard tier](#tab/vault-standard)


To create a backup policy for Azure Files Vault-Standard tier, run the following commands:

1. Export the default snapshot & vault standard policies as JSON files for creating a policy.  

    ```azurecli-interactive
    az backup policy show --resource-group myResourceGroup --vault-name myRecoveryServicesVault  --name DefaultVaultPolicy > vaultdemopol.json 
    ```

2. Create a backup policy using the [`az backup policy create`](/cli/azure/backup/vault#az-backup-vault-create) command.

    ```azurecli-interactive
    az backup policy create --resource-group myResourceGroup --vault-name myRecoveryServicesVault  --policy vaultdemopol.json  --name vaultpol --backup-management-type AzureStorage  
    ```

   Example output:

    ```output
    (azcli) C:\Users\testuser\Downloads\CLIForAFS\azure-cli>az backup policy create -g myResourceGroup  -v afsbugbashvault --policy vaultdemopol.json  --name vaultpol --backup-management-type AzureStorage
    {
      "eTag": null,
      "id": "/subscriptions/aaaa0a0a-bb1b-cc2c-dd3d-eeeeee4e4e4e/resourceGroups/myResourceGroup /providers/Microsoft.RecoveryServices/vaults/ myRecoveryServicesVault  /backupPolicies/vaultpol",
      "location": null,
      "name": "vaultpol",
      "properties": {
        "backupManagementType": "AzureStorage",
        "protectedItemsCount": 0,
        "resourceGuardOperationRequests": null,
        "retentionPolicy": null,
        "schedulePolicy": {
          "hourlySchedule": null,
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": null,
          "scheduleRunFrequency": "Daily",
          "scheduleRunTimes": [
            "2024-12-30T23:00:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        },
        "timeZone": "UTC",
        "vaultRetentionPolicy": {
          "snapshotRetentionInDays": 5,
          "vaultRetention": {
            "dailySchedule": {
              "retentionDuration": {
                "count": 30,
                "durationType": "Days"
              },
              "retentionTimes": [
                "2024-12-30T23:00:00+00:00"
              ]
            },
            "monthlySchedule": null,
            "retentionPolicyType": "LongTermRetentionPolicy",
            "weeklySchedule": null,
            "yearlySchedule": null
          }
        },
        "workLoadType": "AzureFileShare"
      },
      "resourceGroup": "myResourceGroup ",
      "tags": null,
      "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
    }
    ```

---

## Modify policy

You can modify a backup policy to change backup frequency or retention range by using [az backup item set-policy](/cli/azure/backup/item#az-backup-item-set-policy).

To change the policy, define the following parameters:

* **--container-name**: The name of the storage account that hosts the File Share. To retrieve the **name** or **friendly name** of your container, use the [az backup container list](/cli/azure/backup/container#az-backup-container-list) command.
* **--name**: The name of the File Share for which you want to change the policy. To retrieve the **name** or **friendly name** of your backed-up item, use the [az backup item list](/cli/azure/backup/item#az-backup-item-list) command.
* **--policy-name**: The name of the backup policy you want to set for your File Share. You can use [az backup policy list](/cli/azure/backup/policy#az-backup-policy-list) to view all the policies for your vault.

The following example sets the *schedule2* backup policy for the `azurefiles` File Share present in the *afsaccount* storage account.

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

You can also run the previous command by using the friendly names for the container and the item by providing the following two more parameters:

* **--backup-management-type**: `azurestorage`
* **--workload-type**: `azurefileshare`

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

The **Name** attribute in the output corresponds to the name of the job that's created by the backup service for your change policy operation. To track the status of the job, use the [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## Stop protection on a File Share

There are two ways to stop protecting Azure Files:

* Stop all future backup jobs and *delete* all recovery points.
* Stop all future backup jobs but *leave* the recovery points.

There might be a cost associated with leaving the recovery points in storage, because the underlying snapshots created by Azure Backup are retained. The benefit of leaving the recovery points is the option to restore the File Share later, if you want. For information about the cost of leaving the recovery points, see the [pricing details](https://azure.microsoft.com/pricing/details/storage/files). If you choose to delete all recovery points, you can't restore the File Share.

To stop protection for the File Share, define the following parameters:

* **--container-name**: The name of the storage account that hosts the File Share. To retrieve the **name** or **friendly name** of your container, use the [az backup container list](/cli/azure/backup/container#az-backup-container-list) command.
* **--item-name**: The name of the File Share for which you want to stop protection. To retrieve the **name** or **friendly name** of your backed-up item, use the [az backup item list](/cli/azure/backup/item#az-backup-item-list) command.

### Stop protection and retain recovery points

To stop protection while retaining data, use the [az backup protection disable](/cli/azure/backup/protection#az-backup-protection-disable) cmdlet.

The following example stops protection for the `azurefiles` File Share but retains all recovery points.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

You can also run the previous command by using the friendly name for the container and the item by providing the following two more parameters:

* **--backup-management-type**: `azurestorage`
* **--workload-type**: `azurefileshare`

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

The **Name** attribute in the output corresponds to the name of the job that's created by the backup service for your stop protection operation. To track the status of the job, use the [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

### Stop protection without retaining recovery points

To stop protection without retaining recovery points, use the [az backup protection disable](/cli/azure/backup/protection#az-backup-protection-disable) cmdlet with the **delete-backup-data** option set to **true**.

The following example stops protection for the `azurefiles` File Share without retaining recovery points.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

You can also run the previous command by using the friendly name for the container and the item by providing the following two more parameters:

* **--backup-management-type**: `azurestorage`
* **--workload-type**: `azurefileshare`

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## Resume protection on a File Share

If you stopped protection for an Azure Files but retained recovery points, you can resume protection later. If you don't retain the recovery points, you can't resume protection.

To resume protection for the File Share, define the following parameters:

* **--container-name**: The name of the storage account that hosts the File Share. To retrieve the **name** or **friendly name** of your container, use the [az backup container list](/cli/azure/backup/container#az-backup-container-list) command.
* **--item-name**: The name of the File Share for which you want to resume protection. To retrieve the **name** or **friendly name** of your backed-up item, use the [az backup item list](/cli/azure/backup/item#az-backup-item-list) command.
* **--policy-name**: The name of the backup policy for which you want to resume the protection for the File Share.

The following example uses the [az backup protection resume](/cli/azure/backup/protection#az-backup-protection-resume) cmdlet to resume protection for the `azurefiles` File Share by using the *schedule1* backup policy.

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

You can also run the previous command by using the friendly name for the container and the item by providing the following two more parameters:

* **--backup-management-type**: `azurestorage`
* **--workload-type**: `azurefileshare`

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

The **Name** attribute in the output corresponds to the name of the job that's created by the backup service for your resume protection operation. To track the status of the job, use the [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## Unregister a storage account

If you want to protect your File Shares in a particular storage account by using a different Recovery Services vault, first [stop protection for all File Shares](#stop-protection-on-a-file-share) in that storage account. Then unregister the account from the Recovery Services vault currently used for protection.

You need to provide a container name to unregister the storage account. To retrieve the **name** or the **friendly name** of your container, use the [az backup container list](/cli/azure/backup/container#az-backup-container-list) command.

The following example unregisters the *afsaccount* storage account from *azurefilesvault* by using the [az backup container unregister](/cli/azure/backup/container#az-backup-container-unregister) cmdlet.

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

You can also run the previous cmdlet by using the friendly name for the container by providing the following more parameter:

* **--backup-management-type**: `azurestorage`

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## Next steps

[Troubleshoot Azure Files backup](troubleshoot-azure-files.md).
