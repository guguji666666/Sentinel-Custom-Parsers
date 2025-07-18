---
title: Quickstart - Back up blobs in a storage account via ARM template using Azure Backup
description: Learn how to back up blobs in a storage account with an ARM template.
ms.topic: quickstart
ms.custom: devx-track-arm-template
ms.date: 06/17/2025
author: AbhishekMallick-MS
ms.author: v-mallicka
# Customer intent: "As a cloud administrator, I want to back up Azure Blob data in a storage account using an ARM template, so that I can ensure data protection with customizable retention policies and automated scheduling."
---

# Quickstart: Back up a storage account with Blob data using an ARM template

This quickstart describes how to back up a storage account with Azure Blob data with a vaulted backup policy using an ARM template. You can also [configure backup using REST API](backup-azure-dataprotection-use-rest-api-backup-blobs.md).

[!INCLUDE [blob-vaulted-backup-introduction.md](../../includes/blob-vaulted-backup-introduction.md)]

## Review the template

This template allows you to configure backup for two containers in a storage account with a vaulted backup policy with a daily schedule and retention as 30 days, weeks, months, and years for daily, weekly, monthly, and yearly backup, respectively.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "_generator": {
      "name": "bicep",
      "version": "0.27.1.19265",
      "templateHash": "17726641850605580465"
    }
  },
  "parameters": {
    "vaultName": {
      "type": "string",
      "defaultValue": "[format('vault{0}', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the Vault"
      }
    },
    "vaultStorageRedundancy": {
      "type": "string",
      "defaultValue": "GeoRedundant",
      "allowedValues": [
        "LocallyRedundant",
        "ZoneRedundant",
        "GeoRedundant"
      ],
      "metadata": {
        "description": "Change Vault Storage Type (not allowed if the vault has registered backups)"
      }
    },
    "backupPolicyName": {
      "type": "string",
      "defaultValue": "[format('policy{0}', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the Backup Policy"
      }
    },
    "operationalTierRetentionInDays": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 1,
      "maxValue": 360,
      "metadata": {
        "description": "Operational tier backup retention duration in days"
      }
    },
    "vaultTierDefaultRetentionInDays": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 7,
      "maxValue": 3650,
      "metadata": {
        "description": "Vault tier default backup retention duration in days"
      }
    },
    "vaultTierWeeklyRetentionInWeeks": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 4,
      "maxValue": 521,
      "metadata": {
        "description": "Vault tier weekly backup retention duration in weeks"
      }
    },
    "vaultTierMonthlyRetentionInMonths": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 5,
      "maxValue": 116,
      "metadata": {
        "description": "Vault tier monthly backup retention duration in months"
      }
    },
    "vaultTierYearlyRetentionInYears": {
      "type": "int",
      "defaultValue": 10,
      "minValue": 1,
      "maxValue": 10,
      "metadata": {
        "description": "Vault tier yearly backup retention duration in years"
      }
    },
    "vaultTierDailyBackupScheduleTime": {
      "type": "string",
      "defaultValue": "06:00",
      "metadata": {
        "description": "Vault tier daily backup schedule time"
      }
    },
    "storageAccountName": {
      "type": "string",
      "defaultValue": "[format('store{0}', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the Storage Account"
      }
    },
    "containerList": {
      "type": "array",
      "defaultValue": [
        "container1",
        "container2"
      ],
      "metadata": {
        "description": "List of the containers to be protected"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources"
      }
    }
  },
  "variables": {
    "roleDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'e5e2a7ff-d759-4cd2-bb51-3152d37e2eb1')]",
    "dataSourceType": "Microsoft.Storage/storageAccounts/blobServices",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "operationalTierRetentionDuration": "[format('P{0}D', parameters('operationalTierRetentionInDays'))]",
    "vaultTierDefaultRetentionDuration": "[format('P{0}D', parameters('vaultTierDefaultRetentionInDays'))]",
    "vaultTierWeeklyRetentionDuration": "[format('P{0}W', parameters('vaultTierWeeklyRetentionInWeeks'))]",
    "vaultTierMonthlyRetentionDuration": "[format('P{0}M', parameters('vaultTierMonthlyRetentionInMonths'))]",
    "vaultTierYearlyRetentionDuration": "[format('P{0}Y', parameters('vaultTierYearlyRetentionInYears'))]",
    "repeatingTimeIntervals": "[format('R/2024-05-06T{0}:00+00:00/P1D', parameters('vaultTierDailyBackupScheduleTime'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DataProtection/backupVaults",
      "apiVersion": "2022-05-01",
      "name": "[parameters('vaultName')]",
      "location": "[parameters('location')]",
      "identity": {
        "type": "systemAssigned"
      },
      "properties": {
        "storageSettings": [
          {
            "datastoreType": "VaultStore",
            "type": "[parameters('vaultStorageRedundancy')]"
          }
        ]
      }
    },
    {
      "type": "Microsoft.DataProtection/backupVaults/backupPolicies",
      "apiVersion": "2022-05-01",
      "name": "[format('{0}/{1}', parameters('vaultName'), parameters('backupPolicyName'))]",
      "properties": {
        "policyRules": [
          {
            "name": "Default",
            "objectType": "AzureRetentionRule",
            "isDefault": true,
            "lifecycles": [
              {
                "deleteAfter": {
                  "duration": "[variables('operationalTierRetentionDuration')]",
                  "objectType": "AbsoluteDeleteOption"
                },
                "sourceDataStore": {
                  "dataStoreType": "OperationalStore",
                  "objectType": "DataStoreInfoBase"
                },
                "targetDataStoreCopySettings": []
              }
            ]
          },
          {
            "name": "Yearly",
            "objectType": "AzureRetentionRule",
            "isDefault": false,
            "lifecycles": [
              {
                "deleteAfter": {
                  "duration": "[variables('vaultTierYearlyRetentionDuration')]",
                  "objectType": "AbsoluteDeleteOption"
                },
                "sourceDataStore": {
                  "dataStoreType": "VaultStore",
                  "objectType": "DataStoreInfoBase"
                },
                "targetDataStoreCopySettings": []
              }
            ]
          },
          {
            "name": "Monthly",
            "objectType": "AzureRetentionRule",
            "isDefault": false,
            "lifecycles": [
              {
                "deleteAfter": {
                  "duration": "[variables('vaultTierMonthlyRetentionDuration')]",
                  "objectType": "AbsoluteDeleteOption"
                },
                "sourceDataStore": {
                  "dataStoreType": "VaultStore",
                  "objectType": "DataStoreInfoBase"
                },
                "targetDataStoreCopySettings": []
              }
            ]
          },
          {
            "name": "Weekly",
            "objectType": "AzureRetentionRule",
            "isDefault": false,
            "lifecycles": [
              {
                "deleteAfter": {
                  "duration": "[variables('vaultTierWeeklyRetentionDuration')]",
                  "objectType": "AbsoluteDeleteOption"
                },
                "sourceDataStore": {
                  "dataStoreType": "VaultStore",
                  "objectType": "DataStoreInfoBase"
                },
                "targetDataStoreCopySettings": []
              }
            ]
          },
          {
            "name": "Default",
            "objectType": "AzureRetentionRule",
            "isDefault": true,
            "lifecycles": [
              {
                "deleteAfter": {
                  "duration": "[variables('vaultTierDefaultRetentionDuration')]",
                  "objectType": "AbsoluteDeleteOption"
                },
                "sourceDataStore": {
                  "dataStoreType": "VaultStore",
                  "objectType": "DataStoreInfoBase"
                },
                "targetDataStoreCopySettings": []
              }
            ]
          },
          {
            "name": "BackupDaily",
            "objectType": "AzureBackupRule",
            "backupParameters": {
              "backupType": "Discrete",
              "objectType": "AzureBackupParams"
            },
            "dataStore": {
              "dataStoreType": "VaultStore",
              "objectType": "DataStoreInfoBase"
            },
            "trigger": {
              "schedule": {
                "timeZone": "UTC",
                "repeatingTimeIntervals": [
                  "[variables('repeatingTimeIntervals')]"
                ]
              },
              "taggingCriteria": [
                {
                  "isDefault": false,
                  "taggingPriority": 10,
                  "tagInfo": {
                    "id": "Yearly_",
                    "tagName": "Yearly"
                  },
                  "criteria": [
                    {
                      "absoluteCriteria": [
                        "FirstOfYear"
                      ],
                      "objectType": "ScheduleBasedBackupCriteria"
                    }
                  ]
                },
                {
                  "isDefault": false,
                  "taggingPriority": 15,
                  "tagInfo": {
                    "id": "Monthly_",
                    "tagName": "Monthly"
                  },
                  "criteria": [
                    {
                      "absoluteCriteria": [
                        "FirstOfMonth"
                      ],
                      "objectType": "ScheduleBasedBackupCriteria"
                    }
                  ]
                },
                {
                  "isDefault": false,
                  "taggingPriority": 20,
                  "tagInfo": {
                    "id": "Weekly_",
                    "tagName": "Weekly"
                  },
                  "criteria": [
                    {
                      "absoluteCriteria": [
                        "FirstOfWeek"
                      ],
                      "objectType": "ScheduleBasedBackupCriteria"
                    }
                  ]
                },
                {
                  "isDefault": true,
                  "taggingPriority": 99,
                  "tagInfo": {
                    "id": "Default_",
                    "tagName": "Default"
                  }
                }
              ],
              "objectType": "ScheduleBasedTriggerContext"
            }
          }
        ],
        "datasourceTypes": [
          "[variables('dataSourceType')]"
        ],
        "objectType": "BackupPolicy"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DataProtection/backupVaults', parameters('vaultName'))]"
      ]
    },
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2023-01-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Standard_RAGRS",
        "tier": "Standard"
      }
    },
    {
      "copy": {
        "name": "storageContainerList",
        "count": "[length(parameters('containerList'))]"
      },
      "type": "Microsoft.Storage/storageAccounts/blobServices/containers",
      "apiVersion": "2023-01-01",
      "name": "[format('{0}/default/{1}', parameters('storageAccountName'), parameters('containerList')[copyIndex()])]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2022-04-01",
      "scope": "[format('Microsoft.Storage/storageAccounts/{0}', parameters('storageAccountName'))]",
      "name": "[guid(resourceId('Microsoft.DataProtection/backupVaults', parameters('vaultName')), variables('roleDefinitionId'), resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')))]",
      "properties": {
        "roleDefinitionId": "[variables('roleDefinitionId')]",
        "principalId": "[reference(resourceId('Microsoft.DataProtection/backupVaults', parameters('vaultName')), '2021-01-01', 'Full').identity.principalId]",
        "principalType": "ServicePrincipal"
      },
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
        "[resourceId('Microsoft.DataProtection/backupVaults', parameters('vaultName'))]"
      ]
    },
    {
      "type": "Microsoft.DataProtection/backupVaults/backupInstances",
      "apiVersion": "2022-05-01",
      "name": "[format('{0}/{1}', parameters('vaultName'), parameters('storageAccountName'))]",
      "properties": {
        "objectType": "BackupInstance",
        "friendlyName": "[parameters('storageAccountName')]",
        "dataSourceInfo": {
          "objectType": "Datasource",
          "resourceID": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "resourceName": "[parameters('storageAccountName')]",
          "resourceType": "[variables('resourceType')]",
          "resourceUri": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "resourceLocation": "[parameters('location')]",
          "datasourceType": "[variables('dataSourceType')]"
        },
        "dataSourceSetInfo": {
          "objectType": "DatasourceSet",
          "resourceID": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "resourceName": "[parameters('storageAccountName')]",
          "resourceType": "[variables('resourceType')]",
          "resourceUri": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "resourceLocation": "[parameters('location')]",
          "datasourceType": "[variables('dataSourceType')]"
        },
        "policyInfo": {
          "policyId": "[resourceId('Microsoft.DataProtection/backupVaults/backupPolicies', parameters('vaultName'), parameters('backupPolicyName'))]",
          "name": "[parameters('backupPolicyName')]",
          "policyParameters": {
            "backupDatasourceParametersList": [
              {
                "objectType": "BlobBackupDatasourceParameters",
                "containersList": "[parameters('containerList')]"
              }
            ]
          }
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.DataProtection/backupVaults/backupPolicies', parameters('vaultName'), parameters('backupPolicyName'))]",
        "[extensionResourceId(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), 'Microsoft.Authorization/roleAssignments', guid(resourceId('Microsoft.DataProtection/backupVaults', parameters('vaultName')), variables('roleDefinitionId'), resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))))]",
        "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
        "storageContainerList",
        "[resourceId('Microsoft.DataProtection/backupVaults', parameters('vaultName'))]"
      ]
    }
  ]
}


```

## Next step

- [Create ARM templates](../azure-resource-manager/templates/template-tutorial-create-first-template.md).
- Restore Azure Blobs by Azure Backup using [Azure portal](blob-restore.md), [Azure PowerShell](restore-blobs-storage-account-ps.md), [Azure CLI](restore-blobs-storage-account-cli.md), [REST API](backup-azure-dataprotection-use-rest-api-restore-blobs.md).
