---
title: Tutorial - Back up Azure Database for PostgreSQL server
description: Learn about how to back up Azure Database for PostgreSQL server to an Azure Backup Vault.
ms.topic: tutorial
ms.date: 02/17/2025
ms.service: azure-backup
author: AbhishekMallick-MS
ms.author: v-mallicka
# Customer intent: "As a database administrator, I want to back up Azure Database for PostgreSQL to a Backup vault, so that I can ensure data safety and facilitate recovery in case of failures."
---
# Back up Azure Database for PostgreSQL server

This tutorial shows you how to back up Azure Database for PostgreSQL server running on an Azure VM to an Azure Backup Recovery Services vault. In this article, you learn how to:

> [!div class="checklist"]
>
> - Create a Backup vault
> - Create a Backup Policy
> - Prepare the databases
> - Configure backup on the database
> - Run an on-demand backup
> - Track a backup job

## Before you start

Before you back up your Azure Database for PostgreSQL server:

- Identify or create a Backup Vault in the same region where you want to back up the Azure Database for PostgreSQL server instance.
- Check that Azure Database for PostgreSQL server is named in accordance with naming guidelines for Azure Backup. [Learn more](/azure/postgresql/tutorial-design-database-using-azure-portal#create-an-azure-database-for-postgresql)
- [Create secrets in the key vault](backup-azure-database-postgresql.md#create-secrets-in-the-key-vault).
- [Allow access permissions for the relevant key vault](backup-azure-database-postgresql-overview.md#access-permissions-on-the-azure-key-vault-associated-with-the-postgresql-server).
- [Provide database user's backup privileges on the database](backup-azure-database-postgresql-overview.md#database-users-backup-privileges-on-the-database).
- [Allow access permissions for PostgreSQL server](backup-azure-database-postgresql-overview.md#access-permissions-on-the-azure-postgresql-server).


## Create a Backup vault

A Backup vault is a storage entity in Azure that holds back-up data for various newer workloads that Azure Backup supports, such as Azure Database for PostgreSQL servers and Azure Disks. Backup vaults make it easy to organize your backup data, while minimizing management overhead. Backup vaults are based on the Azure Resource Manager model of Azure, which provides enhanced capabilities to help secure back-up data.

1. Sign in to the [Azure portal](https://portal.azure.com/).
1. Type **Backup center** in the search box.
1. Under **Services**, select **Backup center**.
1. On the **Backup center** page, select **Vault**.

   :::image type="content" source="./media/backup-managed-disks/backup-center.png" alt-text="Screenshot showing to select Vault in Backup center.":::

1. In the **Initiate: Create Vault** screen, select **Backup vault**, and **Proceed**.

   :::image type="content" source="./media/backup-managed-disks/initiate-create-vault.png" alt-text="Screenshot showing to select Initiate: Create vault.":::

1. On the **Basics** tab, provide subscription, resource group, backup vault name, region, and backup storage redundancy.

   Continue by selecting **Review + create**. Learn more about [creating a Backup vault](./create-manage-backup-vault.md#create-a-backup-vault).

   :::image type="content" source="./media/backup-managed-disks/review-and-create.png" alt-text="Screenshot showing to select Review and create vault.":::

## Create Backup Policy

You can create a Backup policy on the go during the configure backup flow. Alternatively, go to **Backup center** -> **Backup policies** -> **Add**.

1. Enter a name for the new policy.

   :::image type="content" source="./media/backup-azure-database-postgresql/enter-name-for-new-policy-inline.png" alt-text="Screenshot showing the process to enter a name for the new policy." lightbox="./media/backup-azure-database-postgresql/enter-name-for-new-policy-expanded.png":::

1. Define the Backup schedule.

   Currently, only Weekly backup option is available. However, you can schedule the backups on multiple days of the week.

1. Define **Retention** settings.

   You can add one or more retention rules. Each retention rule assumes inputs for specific backups, and data store and retention duration for those backups.

1. To store your backups in one of the two data stores (or tiers), choose **Backup data store** (standard tier) or **Archive data store** (in preview).

1. Choose **On-expiry** to move the backup to archive data store upon its expiry in the backup data store.

   >[!Note]
   >The **default retention rule** is applied in the absence of any other retention rule and has a default value of three months.
   >
   >- Retention duration ranges from seven days to 10 years in the **Backup data store**.
   >- Retention duration ranges from six months to 10 years in the **Archive data store**.

   :::image type="content" source="./media/backup-azure-database-postgresql/choose-option-to-move-backup-to-archive-data-store-inline.png" alt-text="Screenshot showing to	choose On-expiry to move the backup to archive data store upon its expiry." lightbox="./media/backup-azure-database-postgresql/choose-option-to-move-backup-to-archive-data-store-expanded.png":::

>[!Note]
>The retention rules are evaluated in a pre-determined order of priority. The priority is the highest for the yearly rule, followed by the monthly, and then the weekly rule. Default retention settings are applied when no other rules qualify. For example, the same recovery point may be the first successful backup taken every week as well as the first successful backup taken every month. However, as the monthly rule priority is higher than that of the weekly rule, the retention corresponding to the first successful backup taken every month applies.

## Prepare the database

To prepare the database, follow these steps:

1. [Create secrets in the key vault](backup-azure-database-postgresql.md#create-secrets-in-the-key-vault).
1. [Grant privileges to database users using PowerShell scripts](backup-azure-database-postgresql.md#run-powershell-script-to-grant-privileges-to-database-users).


## Configure backup on the database

You can configure backup on multiple databases across multiple Azure PostgreSQL servers. To configure backup on the Azure PostgreSQL databases using Azure Backup, follow these steps:

1. Go to **Backup vault** -> **+Backup**.

   :::image type="content" source="./media/backup-azure-database-postgresql/adding-backup-inline.png" alt-text="Screenshot showing the option to add a backup." lightbox="./media/backup-azure-database-postgresql/adding-backup-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/adding-backup-details-inline.png" alt-text="Screenshot showing the option to add back-up information." lightbox="./media/backup-azure-database-postgresql/adding-backup-details-expanded.png":::

   Alternatively, you can navigate to this page from the [Backup center](./backup-center-overview.md). 

1. Select or [create](#create-backup-policy) a Backup Policy that defines the backup schedule and the retention duration.

   :::image type="content" source="./media/backup-azure-database-postgresql/create-or-add-backup-policy-inline.png" alt-text="Screenshot showing the option to add a backup policy." lightbox="./media/backup-azure-database-postgresql/create-or-add-backup-policy-expanded.png":::

1. **Select Azure PostgreSQL databases to back up**: Choose one of the Azure PostgreSQL servers across subscriptions if they're in the same region as that of the vault. Expand the arrow to see the list of databases within a server.

   >[!Note]
   >You don't need to back up the databases *azure_maintenance* and *azure_sys*. Additionally, you can't back up a database already backed-up to a Backup vault.

   :::image type="content" source="./media/backup-azure-database-postgresql/select-azure-postgresql-databases-to-back-up-inline.png" alt-text="Screenshot showing the option to select an Azure PostgreSQL database." lightbox="./media/backup-azure-database-postgresql/select-azure-postgresql-databases-to-back-up-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/choose-an-azure-postgresql-server-inline.png" alt-text="Screenshot showing how to choose an Azure PostgreSQL server." lightbox="./media/backup-azure-database-postgresql/choose-an-azure-postgresql-server-expanded.png":::


1. **Assign Azure Key Vault** that stores the credentials to connect to the selected database. To assign the key vault at the individual row level, click **Select a key vault and secret**. You can also assign the key vault by multi-selecting the rows and click **Assign key vault** in the top menu of the grid. 

   :::image type="content" source="./media/backup-azure-database-postgresql/assign-azure-key-vault-inline.png" alt-text="Screenshot showing how to assign Azure Key Vault." lightbox="./media/backup-azure-database-postgresql/assign-azure-key-vault-expanded.png"::: 

1. To specify the secret information, use one of the following options: 

   1. **Enter secret URI**: Use this option if the secret URI is shared/known to you. You can copy the **secret URI from the Key vault** -> **Secrets (select a secret)** -> **Secret Identifier**.

      :::image type="content" source="./media/backup-azure-database-postgresql/enter-secret-uri-inline.png" alt-text="Screenshot showing how to enter secret U R I." lightbox="./media/backup-azure-database-postgresql/enter-secret-uri-expanded.png":::  

      However, with this option, Azure Backup gets no visibility about the key vault you’ve referenced. Therefore, access permissions on the key vault can’t be granted inline. The backup admin along with the Postgres and/or key vault admin need to ensure that the backup vault’s [access on the key vault is granted manually](backup-azure-database-postgresql-overview.md#access-permissions-on-the-azure-key-vault-associated-with-the-postgresql-server) outside the configure backup flow for the backup operation to succeed.

   1. **Select the key vault**: Use this option if you know the key vault and secret name. With this option, you (backup admin with write access on the key vault) can grant the access permissions on the key vault inline. The key vault and the secret could pre-exist or be created on the go. Ensure that the secret is the PG server connection string in ADO.net format updated with the credentials of the database user that has been granted with the _backup_ privileges on the server. Learn more about how to [create secrets in the key vault](backup-azure-database-postgresql.md#create-secrets-in-the-key-vault).

      :::image type="content" source="./media/backup-azure-database-postgresql/assign-secret-store-inline.png" alt-text="Screenshot showing how to assign secret store." lightbox="./media/backup-azure-database-postgresql/assign-secret-store-expanded.png":::

      :::image type="content" source="./media/backup-azure-database-postgresql/select-secret-from-azure-key-vault-inline.png" alt-text="Screenshot showing the selection of secret from Azure Key Vault." lightbox="./media/backup-azure-database-postgresql/select-secret-from-azure-key-vault-expanded.png":::   

1. When the secret information update is complete, the validation starts after the key vault information has been updated.

   >[!Note]
   >
   >- Here, the Azure Backup service validates if it has all the necessary [access permissions](backup-azure-database-postgresql-overview.md#key-vault-based-authentication-model) to read secret details from the key vault and connect to the database.
   >- If one or more access permissions are found missing, it'll display one of the error messages – _Role assignment not done or User cannot assign roles_.

   :::image type="content" source="./media/backup-azure-database-postgresql/validation-of-secret-inline.png" alt-text="Screenshot showing the validation of secret." lightbox="./media/backup-azure-database-postgresql/validation-of-secret-expanded.png":::   

   - **User cannot assign roles**: This message displays when you (the backup admin) don’t have the write access on the PostgreSQL server and/or key vault to assign missing permissions as listed under **View details**. Download the assignment template from the action button and have it run by the PostgreSQL and/or key vault admin. It’s an ARM template that helps you assign the necessary permissions on the required resources. Once the template is run successfully, click **Re-validate** on the Configure Backup page.

     :::image type="content" source="./media/backup-azure-database-postgresql/download-role-assignment-template-inline.png" alt-text="Screenshot showing the option to download role assignment template." lightbox="./media/backup-azure-database-postgresql/download-role-assignment-template-expanded.png":::    

   - **Role assignment not done**: This message displays when you (the backup admin) have the write access on the PostgreSQL server and/or key vault to assign missing permissions as listed under **View details**. Use **Assign missing roles** action button in the top action menu to grant permissions on the PostgreSQL server and/or the key vault inline.

     :::image type="content" source="./media/backup-azure-database-postgresql/role-assignment-not-done-inline.png" alt-text="Screenshot showing the error about the role assignment not done." lightbox="./media/backup-azure-database-postgresql/role-assignment-not-done-expanded.png":::     

1. Select **Assign missing roles** in the top menu and assign roles. Once the process starts, the [missing access permissions](backup-azure-database-postgresql-overview.md#azure-backup-authentication-with-the-postgresql-server) on the KV and/or PG server are granted to the backup vault. You can define the scope at which the access permissions should be granted. When the action is complete, re-validation starts.

   :::image type="content" source="./media/backup-azure-database-postgresql/assign-missing-roles-inline.png" alt-text="Screenshot showing the option to assign missing roles." lightbox="./media/backup-azure-database-postgresql/assign-missing-roles-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/define-scope-of-access-permission-inline.png" alt-text="Screenshot showing to define the scope of access permission." lightbox="./media/backup-azure-database-postgresql/define-scope-of-access-permission-expanded.png":::     

   - Backup vault accesses secrets the key vault and runs a test connection to the database to validate if the credentials have been entered correctly. The privileges of the database user are also checked to see [if the Database user has backup-related permissions on the database](backup-azure-database-postgresql-overview.md#database-users-backup-privileges-on-the-database).

   - PostgreSQL admin will have all the backup and restore permissions on the database by default. Therefore, validations would succeed.
   
   - A low-privileged user may not have backup/restore permissions on the database. Therefore, the validations would fail. A PowerShell script is dynamically generated (one per record/selected database). [Run the PowerShell script to grant these privileges to the database user on the database](backup-azure-database-postgresql.md#create-secrets-in-the-key-vault). Alternatively, you can assign these privileges using PG admin or PSQL tool.

   :::image type="content" source="./media/backup-azure-database-postgresql/backup-vault-accesses-secrets-inline.png" alt-text="Screenshot showing the backup vault access secrets from the key vault." lightbox="./media/backup-azure-database-postgresql/backup-vault-accesses-secrets-expanded.png":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/run-test-connection.png" alt-text="Screenshot showing the process to start test connection.":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/user-credentials-to-run-test-connection-inline.png" alt-text="Screenshot showing how to provide user credentials to run the test." lightbox="./media/backup-azure-database-postgresql/user-credentials-to-run-test-connection-expanded.png":::      

1. Keep the records with backup readiness as Success to proceed to last step of submitting the operation.

   :::image type="content" source="./media/backup-azure-database-postgresql/backup-readiness-as-success-inline.png" alt-text="Screenshot showing the backup readiness is successful." lightbox="./media/backup-azure-database-postgresql/backup-readiness-as-success-expanded.png":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/review-backup-configuration-details-inline.png" alt-text="Screenshot showing the back-up configuration review page." lightbox="./media/backup-azure-database-postgresql/review-backup-configuration-details-expanded.png":::      

1. Submit the configure backup operation and track the progress under **Backup instances**.

   :::image type="content" source="./media/backup-azure-database-postgresql/submit-configure-backup-operation-inline.png" alt-text="Screenshot showing the back-up configuration submission and tracking progress." lightbox="./media/backup-azure-database-postgresql/submit-configure-backup-operation-expanded.png":::

## Run an on-demand backup

To trigger an on-demand backup (that's not in the schedule specified in the policy), follow these steps:

1. Go to **Backup instances** -> **Backup Now**.

   :::image type="content" source="./media/backup-azure-database-postgresql/navigate-to-retention-rules-inline.png" alt-text="Screenshot showing the option to navigate to the list of retention rules that were defined in the associated Backup policy." lightbox="./media/backup-azure-database-postgresql/navigate-to-retention-rules-expanded.png":::

1. Choose retention rules from the list that were defined in the associated Backup policy.

   :::image type="content" source="./media/backup-azure-database-postgresql/choose-retention-rules-inline.png" alt-text="Screenshot showing the option to choose retention rules that were defined in the associated Backup policy." lightbox="./media/backup-azure-database-postgresql/choose-retention-rules-expanded.png":::

## Track a backup job

Azure Backup service creates a job for scheduled backups or if you trigger on-demand backup operation for tracking. To view the backup job status:

1. Go to the **Backup instance** screen.

   It shows the jobs dashboard with operation and status for the past seven days.

   :::image type="content" source="./media/backup-azure-database-postgresql/postgre-jobs-dashboard.png" alt-text="Screenshot showing the Jobs dashboard." lightbox="./media/backup-azure-database-postgresql/postgre-jobs-dashboard.png":::

1. To view the status of the backup job, select **View all** to see ongoing and past jobs of this backup instance.

   :::image type="content" source="./media/backup-azure-database-postgresql/postgresql-jobs-view-all.png" alt-text="Screenshot showing to select the View all option." lightbox="./media/backup-azure-database-postgresql/postgresql-jobs-view-all.png":::

1. Review the list of backup and restore jobs and their status. Select a job from the list of jobs to view job details.

   :::image type="content" source="./media/backup-azure-database-postgresql/postgresql-jobs-select-job-inline.png" alt-text="Screenshot showing to select job to see details." lightbox="./media/backup-azure-database-postgresql/postgresql-jobs-select-job-expanded.png":::

## Next steps

In this tutorial, you used the Azure portal to:

> [!div class="checklist"]
>
> - Create a Backup vault
> - Create a Backup Policy
> - Prepare the databases
> - Configure backup on the database
> - Run an on-demand backup
> - Track a backup job

Continue to the how-to article to Azure Database for PostgreSQL.

> [!div class="nextstepaction"]
> [Restore Azure Database for PostgreSQL server](restore-azure-database-postgresql.md)