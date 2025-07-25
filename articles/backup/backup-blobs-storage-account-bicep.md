---
title: Quickstart - Back up blobs in a storage account
description: Learn how to back up blobs in a storage account with a Bicep file.
ms.topic: quickstart
ms.custom:
  - devx-track-bicep
  - build-2025
ms.date: 06/17/2025
author: AbhishekMallick-MS
ms.author: v-mallicka
# Customer intent: "As a cloud administrator, I want to back up blob data in a storage account using a Bicep file, so that I can automate the backup process and ensure data retention according to our established policies."
---

# Quickstart: Back up a storage account with Blob data using Azure Backup via a Bicep file

This quickstart describes how to back up a storage account with Azure Blob data with a vaulted backup policy using a Bicep file. You can also [configure backup using REST API](backup-azure-dataprotection-use-rest-api-backup-blobs.md).

[!INCLUDE [blob-vaulted-backup-introduction.md](../../includes/blob-vaulted-backup-introduction.md)]

## Prerequisites

Before you back up a storage account with blob data using a Bicep file, ensure to set up your environment for Bicep development. [Learn more](/azure/azure-resource-manager/bicep/install).

## Review the template

This template allows you to configure backup for two containers in a storage account with a vaulted backup policy with a daily schedule and retention as 30 days, weeks, months, and years for daily, weekly, monthly, and yearly backup, respectively.

```bicep
@description('Name of the Vault')
param vaultName string = 'vault${uniqueString(resourceGroup().id)}'

@description('Change Vault Storage Type (not allowed if the vault has registered backups)')
@allowed([
  'LocallyRedundant'
  'ZoneRedundant'
  'GeoRedundant'
])
param vaultStorageRedundancy string = 'GeoRedundant'

@description('Name of the Backup Policy')
param backupPolicyName string = 'policy${uniqueString(resourceGroup().id)}'

@description('Operational tier backup retention duration in days')
@minValue(1)
@maxValue(360)
param operationalTierRetentionInDays int = 30

@description('Vault tier default backup retention duration in days')
@minValue(7)
@maxValue(3650)
param vaultTierDefaultRetentionInDays int = 30

@description('Vault tier weekly backup retention duration in weeks')
@minValue(4)
@maxValue(521)
param vaultTierWeeklyRetentionInWeeks int = 30

@description('Vault tier monthly backup retention duration in months')
@minValue(5)
@maxValue(116)
param vaultTierMonthlyRetentionInMonths int = 30

@description('Vault tier yearly backup retention duration in years')
@minValue(1)
@maxValue(10)
param vaultTierYearlyRetentionInYears int = 10

@description('Vault tier daily backup schedule time')
param vaultTierDailyBackupScheduleTime string = '06:00'

@description('Name of the Storage Account')
param storageAccountName string = 'store${uniqueString(resourceGroup().id)}'

@description('List of the containers to be protected')
param containerList array = [
  'container1'
  'container2'
]

@description('Location for all resources')
param location string = resourceGroup().location

var roleDefinitionId = subscriptionResourceId(
  'Microsoft.Authorization/roleDefinitions',
  'e5e2a7ff-d759-4cd2-bb51-3152d37e2eb1'
)
var dataSourceType = 'Microsoft.Storage/storageAccounts/blobServices'
var resourceType = 'Microsoft.Storage/storageAccounts'
var operationalTierRetentionDuration = 'P${operationalTierRetentionInDays}D'
var vaultTierDefaultRetentionDuration = 'P${vaultTierDefaultRetentionInDays}D'
var vaultTierWeeklyRetentionDuration = 'P${vaultTierWeeklyRetentionInWeeks}W'
var vaultTierMonthlyRetentionDuration = 'P${vaultTierMonthlyRetentionInMonths}M'
var vaultTierYearlyRetentionDuration = 'P${vaultTierYearlyRetentionInYears}Y'
var repeatingTimeIntervals = 'R/2024-05-06T${vaultTierDailyBackupScheduleTime}:00+00:00/P1D'

resource vault 'Microsoft.DataProtection/backupVaults@2022-05-01' = {
  name: vaultName
  location: location
  identity: {
    type: 'systemAssigned'
  }
  properties: {
    storageSettings: [
      {
        datastoreType: 'VaultStore'
        type: vaultStorageRedundancy
      }
    ]
  }
}

resource backupPolicy 'Microsoft.DataProtection/backupVaults/backupPolicies@2022-05-01' = {
  parent: vault
  name: backupPolicyName
  properties: {
    policyRules: [
      {
        name: 'Default'
        objectType: 'AzureRetentionRule'
        isDefault: true
        lifecycles: [
          {
            deleteAfter: {
              duration: operationalTierRetentionDuration
              objectType: 'AbsoluteDeleteOption'
            }
            sourceDataStore: {
              dataStoreType: 'OperationalStore'
              objectType: 'DataStoreInfoBase'
            }
            targetDataStoreCopySettings: []
          }
        ]
      }
      {
        name: 'Yearly'
        objectType: 'AzureRetentionRule'
        isDefault: false
        lifecycles: [
          {
            deleteAfter: {
              duration: vaultTierYearlyRetentionDuration
              objectType: 'AbsoluteDeleteOption'
            }
            sourceDataStore: {
              dataStoreType: 'VaultStore'
              objectType: 'DataStoreInfoBase'
            }
            targetDataStoreCopySettings: []
          }
        ]
      }
      {
        name: 'Monthly'
        objectType: 'AzureRetentionRule'
        isDefault: false
        lifecycles: [
          {
            deleteAfter: {
              duration: vaultTierMonthlyRetentionDuration
              objectType: 'AbsoluteDeleteOption'
            }
            sourceDataStore: {
              dataStoreType: 'VaultStore'
              objectType: 'DataStoreInfoBase'
            }
            targetDataStoreCopySettings: []
          }
        ]
      }
      {
        name: 'Weekly'
        objectType: 'AzureRetentionRule'
        isDefault: false
        lifecycles: [
          {
            deleteAfter: {
              duration: vaultTierWeeklyRetentionDuration
              objectType: 'AbsoluteDeleteOption'
            }
            sourceDataStore: {
              dataStoreType: 'VaultStore'
              objectType: 'DataStoreInfoBase'
            }
            targetDataStoreCopySettings: []
          }
        ]
      }
      {
        name: 'Default'
        objectType: 'AzureRetentionRule'
        isDefault: true
        lifecycles: [
          {
            deleteAfter: {
              duration: vaultTierDefaultRetentionDuration
              objectType: 'AbsoluteDeleteOption'
            }
            sourceDataStore: {
              dataStoreType: 'VaultStore'
              objectType: 'DataStoreInfoBase'
            }
            targetDataStoreCopySettings: []
          }
        ]
      }
      {
        name: 'BackupDaily'
        objectType: 'AzureBackupRule'
        backupParameters: {
          backupType: 'Discrete'
          objectType: 'AzureBackupParams'
        }
        dataStore: {
          dataStoreType: 'VaultStore'
          objectType: 'DataStoreInfoBase'
        }
        trigger: {
          schedule: {
            timeZone: 'UTC'
            repeatingTimeIntervals: [
              repeatingTimeIntervals
            ]
          }
          taggingCriteria: [
            {
              isDefault: false
              taggingPriority: 10
              tagInfo: {
                id: 'Yearly_'
                tagName: 'Yearly'
              }
              criteria: [
                {
                  absoluteCriteria: [
                    'FirstOfYear'
                  ]
                  objectType: 'ScheduleBasedBackupCriteria'
                }
              ]
            }
            {
              isDefault: false
              taggingPriority: 15
              tagInfo: {
                id: 'Monthly_'
                tagName: 'Monthly'
              }
              criteria: [
                {
                  absoluteCriteria: [
                    'FirstOfMonth'
                  ]
                  objectType: 'ScheduleBasedBackupCriteria'
                }
              ]
            }
            {
              isDefault: false
              taggingPriority: 20
              tagInfo: {
                id: 'Weekly_'
                tagName: 'Weekly'
              }
              criteria: [
                {
                  absoluteCriteria: [
                    'FirstOfWeek'
                  ]
                  objectType: 'ScheduleBasedBackupCriteria'
                }
              ]
            }
            {
              isDefault: true
              taggingPriority: 99
              tagInfo: {
                id: 'Default_'
                tagName: 'Default'
              }
            }
          ]
          objectType: 'ScheduleBasedTriggerContext'
        }
      }
    ]
    datasourceTypes: [
      dataSourceType
    ]
    objectType: 'BackupPolicy'
  }
}

resource storageAccount 'Microsoft.Storage/storageAccounts@2023-01-01' = {
  name: storageAccountName
  location: location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_RAGRS'
    tier: 'Standard'
  }
}

resource storageContainerList 'Microsoft.Storage/storageAccounts/blobServices/containers@2023-01-01' = [
  for item in containerList: {
    name: '${storageAccount.name}/default/${item}'
  }
]

resource roleAssignment 'Microsoft.Authorization/roleAssignments@2022-04-01' = {
  scope: storageAccount
  name: guid(vault.id, roleDefinitionId, storageAccount.id)
  properties: {
    roleDefinitionId: roleDefinitionId
    principalId: reference(vault.id, '2021-01-01', 'Full').identity.principalId
    principalType: 'ServicePrincipal'
  }
}

resource backupInstance 'Microsoft.DataProtection/backupVaults/backupInstances@2022-05-01' = {
  parent: vault
  name: storageAccountName
  properties: {
    objectType: 'BackupInstance'
    friendlyName: storageAccountName
    dataSourceInfo: {
      objectType: 'Datasource'
      resourceID: storageAccount.id
      resourceName: storageAccountName
      resourceType: resourceType
      resourceUri: storageAccount.id
      resourceLocation: location
      datasourceType: dataSourceType
    }
    dataSourceSetInfo: {
      objectType: 'DatasourceSet'
      resourceID: storageAccount.id
      resourceName: storageAccountName
      resourceType: resourceType
      resourceUri: storageAccount.id
      resourceLocation: location
      datasourceType: dataSourceType
    }
    policyInfo: {
      policyId: backupPolicy.id
      name: backupPolicyName
      policyParameters: {
        backupDatasourceParametersList: [
          {
            objectType: 'BlobBackupDatasourceParameters'
            containersList: containerList
          }
        ]
      }
    }
  }
  dependsOn: [
    roleAssignment
    storageContainerList
  ]
}
```

## Next step

- [Create Bicep files](../azure-resource-manager/bicep/quickstart-create-bicep-use-visual-studio-code.md?tabs=CLI).
- Restore Azure Blobs by Azure Backup using [Azure portal](blob-restore.md), [Azure PowerShell](restore-blobs-storage-account-ps.md), [Azure CLI](restore-blobs-storage-account-cli.md), [REST API](backup-azure-dataprotection-use-rest-api-restore-blobs.md).
