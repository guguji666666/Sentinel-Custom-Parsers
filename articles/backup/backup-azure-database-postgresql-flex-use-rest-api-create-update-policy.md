---
title: Create backup policies for Azure database for PostgreSQL - Flexible server using data protection REST API in Azure Backup
description: Learn how to create the backup policy to protect Azure PostgreSQL flexible servers using REST API.
ms.topic: concept-article
ms.custom:
  - ignite-2024
ms.date: 05/28/2025
ms.assetid: 759ee63f-148b-464c-bfc4-c9e640b7da6b
author: AbhishekMallick-MS
ms.author: v-mallicka
# Customer intent: As a database administrator, I want to create backup policies for Azure PostgreSQL flexible servers using a REST API, so that I can ensure consistent data protection and retention according to our operational requirements.
---

# Create Azure Data Protection backup policies for Azure Database for PostgreSQL - Flexible servers using REST API

This article describes how to create the backup policy to protect Azure PostgreSQL flexible servers using REST API.

A backup policy governs the retention and schedule of your backups.

You can reuse an existing backup policy to configure backup for PostgreSQL flexible servers to a vault, or [create a backup policy for an Azure Recovery Services vault using REST API](/rest/api/dataprotection/backup-policies/create-or-update).

## Understanding PostgreSQL backup policy

Before you start creating the backup policy, learn about the backup policy object for PostgreSQL:

- PolicyRule
  - BackupRule
	- BackupParameter
	- BackupType (A full database backup in this case)
	- Initial Datastore (Where will the backups land initially)
	- Trigger (How the backup is triggered)
	- Schedule based
	- Default Tagging Criteria (A default 'tag' for all the scheduled backups. This tag links the backups to the retention rule)
  - Default Retention Rule (A rule that will be applied to all backups, by default, on the initial datastore)

This object defines the type of backups that are triggered, the way they are triggered (via a schedule), the tags marked for the backup operation, the path where the backups are stored (a datastore), and the lifecycle of the backup data in a datastore. The default PowerShell object for PostgreSQL - Flexible servers triggers a full backup every week, and stores the backups in the vault, and retains them for three months.

## Create a backup policy

To create an Azure Backup policy, use the following *PUT* operation:

>[!Important]
>Currently, updating or modifying an existing policy isn't supported. Alternatively, you can create a new policy with the required details and assign it to the relevant backup instance.

```HTTP
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupPolicies/{policyName}?api-version=2021-01-01
```

The `{policyName}` and `{vaultName}` are provided in the URI. Additional information is provided in the request body.

## Create the request body

For example, to create a policy for Azure Database for PostgreSQL - Flexible servers backup, the request body needs the following components:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| **properties** | True | BaseBackupPolicy:BackupPolicy | `BaseBackupPolicyResource properties` |

For the complete list of definitions in the request body, see the [backup policy REST API document](/rest/api/dataprotection/backup-policies/create-or-update).

### Example request body

The policy says that:

- It's scheduled to trigger weekly backup and choose the start time (Time + P1W).
- Datastore is vault store, as the backups are directly transferred to the vault.
- The backups are retained in the vault for three months (P3M).

```json
	"properties": {
	  "datasourceTypes": [
		"Microsoft.DBforPostgreSQL/flexibleServers"
	  ],
	  "name": "PgFlexPolicy1",
	  "objectType": "BackupPolicy",
	  "policyRules": [
		{
		  "backupParameters": {
			"backupType": "Full",
			"objectType": "AzureBackupParams"
		  },
		  "dataStore": {
			"dataStoreType": "VaultStore",
			"objectType": "DataStoreInfoBase"
		  },
		  "name": "BackupWeekly",
		  "objectType": "AzureBackupRule",
		  "trigger": {
			"objectType": "ScheduleBasedTriggerContext",
			"schedule": {
			  "repeatingTimeIntervals": [
				"R/2021-08-15T06:30:00+00:00/P1W"
			  ],
			  "timeZone": "UTC"
			},
			"taggingCriteria": [
			  {
				"isDefault": true,
				"tagInfo": {
				  "id": "Default_",
				  "tagName": "Default"
				},
				"taggingPriority": 99
			  }
			]
		  }
		},
		{
		  "isDefault": true,
		  "lifecycles": [
			{
			  "deleteAfter": {
				"duration": "P3M",
				"objectType": "AbsoluteDeleteOption"
			  },
			  "sourceDataStore": {
				"dataStoreType": "VaultStore",
				"objectType": "DataStoreInfoBase"
			  },
			  "targetDataStoreCopySettings": []
			}
		  ],
		  "name": "Default",
		  "objectType": "AzureRetentionRule"
		}
	  ]
	}
}

```

>[!Important]
>The time formats support `DateTime`; only `Time` isn't supported. The time of the day indicates the *backup start time*, and not the time when the backup completes.

Let's update the above JSON template with one change - Backups on multiple days of the week.

The following example modifies the weekly backup to back up on every Sunday, Wednesday, and Friday of every week. The schedule date array mentions the dates, and the dates of the week are taken as days of the week. You also need to specify that these schedules should repeat every week. So, the schedule interval is *1* and the interval type is *Weekly*.

**Scheduled trigger**:

```
"trigger": {
		"objectType": "ScheduleBasedTriggerContext",
		"schedule": {
		  "repeatingTimeIntervals": [
			"R/2021-08-15T22:00:00+00:00/P1W",
			"R/2021-08-18T22:00:00+00:00/P1W",
			"R/2021-08-20T22:00:00+00:00/P1W"
		  ],
		  "timeZone": "UTC"
		}

```

If you want to add another retention rule, then modify the *policy JSON* as follows:

The above JSON has a lifecycle for the initial datastore under the default retention rule. In this scenario, the rule mentions deleting the backup data after three months. You can add a new retention rule that defines a longer retention duration of 6 months for the first backups taken at the start of every month. Let's name this new rule as *Monthly*.

**Retention lifecycle**:

```json
{
  "isDefault": true,
  "lifecycles": [
	{
	  "deleteAfter": {
		"duration": "P3M",
		"objectType": "AbsoluteDeleteOption"
	  },
	  "sourceDataStore": {
		"dataStoreType": "VaultStore",
		"objectType": "DataStoreInfoBase"
	  },
	  "targetDataStoreCopySettings": []
	}
  ],
  "name": "Default",
  "objectType": "AzureRetentionRule"
},
{
	"lifecycles": [
		{
			"deleteAfter": {
				"objectType": "AbsoluteDeleteOption",
				"duration": "P6M"
			},
			"targetDataStoreCopySettings": [],
			"sourceDataStore": {
				"dataStoreType": "VaultStore",
				"objectType": "DataStoreInfoBase"
			}
		}
	],
	"isDefault": false,
	"name": "Monthly",
	"objectType": "AzureRetentionRule"
}

```

Every time you add a retention rule, you need to add a corresponding tag in the *Trigger* property of the policy. The following example creates a new tag along with the criteria (the first successful backup of the month) with the exact same name as the corresponding retention rule to be applied.

In this example, the tag criteria should be named *Monthly*.

**Tagging criteria**:

```json
  "criteria": [
	{
	  "absoluteCriteria": [
		"FirstOfMonth"
	  ],
	  "objectType": "ScheduleBasedBackupCriteria"
	}
  ],
  "isDefault": false,
  "tagInfo": {
	"tagName": "Monthly"
  },
  "taggingPriority": 15
}

```

After including all changes, the policy JSON will appear as follows:

```json
{
	"properties": {
	  "datasourceTypes": [
		"Microsoft.DBforPostgreSQL/flexibleServers"
	  ],
	  "name": "PgFlexPolicy1",
	  "objectType": "BackupPolicy",
	  "policyRules": [
		{
		  "backupParameters": {
			"backupType": "Full",
			"objectType": "AzureBackupParams"
		  },
		  "dataStore": {
			"dataStoreType": "VaultStore",
			"objectType": "DataStoreInfoBase"
		  },
		  "name": "BackupWeekly",
		  "objectType": "AzureBackupRule",
		  "trigger": {
			"objectType": "ScheduleBasedTriggerContext",
			"schedule": {
			  "repeatingTimeIntervals": [
				"R/2021-08-15T22:00:00+00:00/P1W",
				"R/2021-08-18T22:00:00+00:00/P1W",
				"R/2021-08-20T22:00:00+00:00/P1W"
			  ],
			  "timeZone": "UTC"
			},
			"taggingCriteria": [
			  {
				"isDefault": true,
				"tagInfo": {
				  "id": "Default_",
				  "tagName": "Default"
				},
				"taggingPriority": 99
			  },
			  {
				"criteria": [
				  {
					"absoluteCriteria": [
					  "FirstOfMonth"
					],
					"objectType": "ScheduleBasedBackupCriteria"
				  }
				],
				"isDefault": false,
				"tagInfo": {
				  "tagName": "Monthly"
				},
				"taggingPriority": 15
			  }
			]
		  }
		},
		{
		  "isDefault": true,
		  "lifecycles": [
			{
			  "deleteAfter": {
				"duration": "P3M",
				"objectType": "AbsoluteDeleteOption"
			  },
			  "sourceDataStore": {
				"dataStoreType": "VaultStore",
				"objectType": "DataStoreInfoBase"
			  },
			  "targetDataStoreCopySettings": []
			}
		  ],
		  "name": "Default",
		  "objectType": "AzureRetentionRule"
		},
		{
			"lifecycles": [
				{
					"deleteAfter": {
						"objectType": "AbsoluteDeleteOption",
						"duration": "P6M"
					},
					"targetDataStoreCopySettings": [],
					"sourceDataStore": {
						"dataStoreType": "VaultStore",
						"objectType": "DataStoreInfoBase"
					}
				}
			],
			"isDefault": false,
			"name": "Monthly",
			"objectType": "AzureRetentionRule"
		}
	  ]
	}
}

```

For more details about policy creation, see the [PostgreSQL database Backup policy document](backup-azure-database-postgresql.md#create-backup-policy).

### Responses

The backup policy creation or update is a synchronous operation and returns *OK* once the operation is successful.

| Name | Type | Description |
| --- | --- | --- |
| **200 OK** | [BaseBackupPolicyResource](/rest/api/dataprotection/backup-policies/create-or-update#basebackuppolicyresource) | OK |

**Example responses**:

Once the operation completes, it returns 200 (OK) with the policy content in the response body.

```json
{
  "properties": {
	"policyRules": [
	  {
		"backupParameters": {
		  "backupType": "Full",
		  "objectType": "AzureBackupParams"
		},
		"trigger": {
		  "schedule": {
			"repeatingTimeIntervals": [
			  "R/2021-08-15T22:00:00+00:00/P1W",
			  "R/2021-08-18T22:00:00+00:00/P1W",
			  "R/2021-08-20T22:00:00+00:00/P1W"
			],
			"timeZone": "UTC"
		  },
		  "taggingCriteria": [
			{
			  "tagInfo": {
				"tagName": "Default",
				"id": "Default_"
			  },
			  "taggingPriority": 99,
			  "isDefault": true
			},
			{
			  "tagInfo": {
				"tagName": "Monthly",
				"id": "Monthly_"
			  },
			  "taggingPriority": 15,
			  "isDefault": false,
			  "criteria": [
				{
				  "absoluteCriteria": [
					"FirstOfMonth"
				  ],
				  "objectType": "ScheduleBasedBackupCriteria"
				}
			  ]
			}
		  ],
		  "objectType": "ScheduleBasedTriggerContext"
		},
		"dataStore": {
		  "dataStoreType": "VaultStore",
		  "objectType": "DataStoreInfoBase"
		},
		"name": "BackupWeekly",
		"objectType": "AzureBackupRule"
	  },
	  {
		"lifecycles": [
		  {
			"deleteAfter": {
			  "objectType": "AbsoluteDeleteOption",
			  "duration": "P3M"
			},
			"targetDataStoreCopySettings": [],
			"sourceDataStore": {
			  "dataStoreType": "VaultStore",
			  "objectType": "DataStoreInfoBase"
			}
		  }
		],
		"isDefault": true,
		"name": "Default",
		"objectType": "AzureRetentionRule"
	  },
	  {
		"lifecycles": [
		  {
			"deleteAfter": {
			  "objectType": "AbsoluteDeleteOption",
			  "duration": "P6M"
			},
			"targetDataStoreCopySettings": [],
			"sourceDataStore": {
			  "dataStoreType": "VaultStore",
			  "objectType": "DataStoreInfoBase"
			}
		  }
		],
		"isDefault": false,
		"name": "Monthly",
		"objectType": "AzureRetentionRule"
	  }
	],
	"datasourceTypes": [
	  "Microsoft.DBforPostgreSQL/flexibleServers"
	],
	"objectType": "BackupPolicy"
  },
  "id": "/subscriptions/62b829ee-7936-40c9-a1c9-47a93f9f3965/resourceGroups/PGFlexIntegration/providers/Microsoft.DataProtection/BackupVaults/PgFlexTestVault/backupPolicies/PgFlexPolicy1",
  "name": "PgFlexPolicy1",
  "type": "Microsoft.DataProtection/backupVaults/backupPolicies"
}

```

## Next steps

[Enable protection for Azure Database for PostgreSQL - Flexible Server using REST API](backup-azure-database-postgresql-flex-use-rest-api.md).

For more information on Azure Backup REST APIs, see the following articles:

- [Azure Data Protection REST API](/rest/api/dataprotection).
- [Get started with Azure REST API](/rest/api/azure).
