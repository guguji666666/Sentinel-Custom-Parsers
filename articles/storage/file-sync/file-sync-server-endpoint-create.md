---
title: Create an Azure File Sync Server Endpoint
description: Understand the options during server endpoint creation and how to best apply them to your situation.
author: khdownie
ms.service: azure-file-storage
ms.topic: how-to
ms.date: 07/16/2025
ms.author: kendownie
# Customer intent: As an IT admin deploying Azure File Sync, I want to create a server endpoint, so that I can synchronize files between my registered server and Azure cloud storage.
---

# Create an Azure File Sync server endpoint

A server endpoint represents a specific location on a registered server, such as a folder on a server volume. A server endpoint must meet the following conditions:

- A server endpoint must be a path on a registered server (rather than a mounted share). Network attached storage (NAS) isn't supported.
- Although the server endpoint can be on the system volume, server endpoints on the system volume can't use cloud tiering.
- Changing the path or drive letter after you established a server endpoint on a volume isn't supported. Make sure you're using a suitable path before creating the server endpoint.
- A registered server can support multiple server endpoints, however, a sync group can only have one server endpoint per registered server at any given time. Other server endpoints within the sync group must be on different registered servers.
- Multiple server endpoints can exist on the same volume if their namespaces aren't overlapping (for example, F:\sync1 and F:\sync2) and each endpoint is syncing to a unique sync group. 

This article helps you understand the options and decisions needed to create a new server endpoint and start sync. For this to work, you need to have finished [planning for your Azure File Sync deployment](file-sync-planning.md) and also have deployed [resources needed in prior steps](file-sync-deployment-guide.md) to create a server endpoint.

## Prerequisites

To create a server endpoint, you must first ensure that the following criteria are met: 
- The server has the Azure File Sync agent installed and has been registered. See [Register/unregister a server with Azure File Sync](file-sync-server-registration.md) for details on installing the Azure File Sync Agent. 
- Ensure that a Storage Sync Service has been deployed. See [How to deploy Azure File Sync](file-sync-deployment-guide.md) for details on how to deploy a Storage Sync Service. 
- Ensure that a sync group has been deployed. Learn how to [Create a sync group](file-sync-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint).
- Ensure that the server is connected to the internet and that Azure is accessible. Azure File Sync uses port 443 for all communication between the server and cloud service.
- Ensure that you're within the allowed limits for creating endpoints. See [Azure File Sync Scale Targets](../files/storage-files-scale-targets.md#azure-file-sync-scale-targets) for details on scalability and performance targets.

## Create a server endpoint

[!INCLUDE [storage-files-sync-create-server-endpoint](../../../includes/storage-files-sync-create-server-endpoint.md)]

### Cloud tiering section

When creating a new server endpoint, you can opt into the cloud tiering feature of Azure File Sync. The options in the **Cloud tiering** section can be changed later. However, different options in the following section are available based on having cloud tiering enabled or not for your new server endpoint.

Refer to the [cloud tiering article](file-sync-cloud-tiering-overview.md) that covers the basics, [the policies](file-sync-cloud-tiering-policy.md), and best practices in detail. 

### Initial sync section

The **Initial sync** section is available only for the first server endpoint in a sync group. For any additional server endpoint, see the [Initial download section](#initial-download-section).

There are two fundamentally different initial sync behaviors:

:::row:::
    :::column:::
        **Merge**
    :::column-end:::
    :::column:::
        **Authoritative upload**
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        Merge is the standard option and selected by default. You should leave the selection on **Merge** unless for certain migration scenarios. 
* When joining a server location, in most scenarios either the server location or the cloud share is empty. In these cases, **Merge** is the right behavior and will lead to expected results. 
* When both locations contain files and folders, the namespaces will merge. If there are files or folder names on the server that also exist in the cloud share, there will be a sync conflict. [Conflicts are automatically resolved](../files/storage-files-faq.md#afs-conflict-resolution). </br> </br> Within the **Merge** option, you can select how content from the Azure file share will initially arrive on the server. This selection has no impact if the Azure file share is empty. You can find more details in the [Initial download section](#initial-download-section).
    :::column-end:::
    :::column:::
        **Authoritative upload** is an initial sync option reserved for a specific migration scenario. It syncs the same server path that was also used to seed the cloud share with for instance Azure Data Box. In this case, the cloud and the server locations have mostly the same data, but the server is slightly newer. Users kept making changes while Data Box was in transport. This migration scenario then calls for updating the cloud seamlessly with the changes on the server (newer) without producing any conflicts. So the server is the authority of the shape of the namespace and Data Box was used to avoid large-scale initial upload from the server. Server authoritative upload enables a zero-downtime adoption of the cloud, even when an offline data transport mechanism was used to seed the cloud storage.
    :::column-end:::
:::row-end:::

A server endpoint can only succeed provisioning with the authoritative upload option, when the server location has data in it. This block is to protect from accidental misconfigurations. Authoritative upload works like RoboCopy /MIR. This mode mirrors source to target. The source is the AFS server and the target is the cloud share. Authoritative upload will shape the target in the image of the source. 

* New or updated files and folders will be uploaded from the server.
* Files and folders that don't exist on the server (anymore) will be deleted from the cloud share.
* Metadata-only changes to files and folders on the server will be efficiently moved to the cloud share as metadata-only updates.
* Files and folders might exist on server and the cloud share. But some files or folders might have changed their parent directory on the server since the seeding of the Azure file share. These files and folders will be purged from the cloud share and uploaded again. Because of this, it's best to avoid restructuring your namespace at a larger scale during a migration.

### Initial download section

The **Initial download** section is available for the second and any more server endpoints in a sync group. The [first server endpoint in a sync group has extra options](#initial-sync-section) that relate to migration with Azure Data Box. These options don't apply if this server endpoint isn't the first one in your sync group.

> [!NOTE]
> Selecting an initial download option has no impact if the Azure file share is empty.

As part of this section, you choose how content from the Azure file share will initially arrive on the server.

:::image type="content" source="media/file-sync-server-endpoint-create/initial-download-options.png" alt-text="An image describing the options in the create server endpoint Azure portal wizard." lightbox="media/file-sync-server-endpoint-create/initial-download-options-expanded.png":::

|  | Download the namespace first | Download the namespace only | Avoid tiered files |
|:----------------:|-----------|------------|------------|
| **Description** | Downloads the entire namespace first. File content is recalled from the cloud as a background activity to the server based on the heat map, which recalls recent accessed data sooner. If the free space on server volume is less than 10%, the remaining files will remain tiered files. | Only the namespace (file and folder structure) is downloaded. No file content is brought to the server. | Downloads each file in its entirety before the file shows up in the folder on server. This option avoids a tiered file to ever exist on the server. A namespace item and file content are always present at the same time. |
| **Default settings** | Default if cloud tiering isn't enabled for this server endpoint. | Default if cloud tiering is enabled for this server endpoint. | Not selected as a default option. This option is only available when cloud tiering isn't enabled. |
| **Behavior when tiering is enabled** | When cloud tiering is enabled, the background recall of the tiered files will stop as soon as it meets the criteria for the specified cloud tiering policy (respects volume free policy and date policy too, if present). | Only the namespace (file and folder structure) is downloaded. No file content is brought to the server. | Option not available. |
| **Behavior when tiering isn't enabled** | When cloud tiering isn't enabled, the intent is to recall all the data to the server endpoint via background recall. You would need to provision a volume that is large enough to accommodate all of the data. If the volume doesn't have enough free space, some files will be left as tiered even when cloud tiering is disabled. | Only the namespace (file and folder structure) is downloaded. No file content is brought to the server. | Downloads each file in its entirety before the file shows up in the folder on server. |
| **When to use** | <ul><li>When users need quick access to recent files soon after the namespace is downloaded, and most data is present in the Azure file share at provisioning time. Customers with low bandwidth may also benefit from the background recall after initial provisioning.  For more details on recalling tiered files, see [How to manage Azure File Sync tiered files](file-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk).</li><li>Best suited for Azure File Sync server-side disaster recovery scenarios where server path starts out as empty folder, for example a new server endpoint in branch office.</li></ul> | Ideal for applications that need to recall data less often or only small amount of data on-demand. | <ul><li>When all data must always be available locally without relying on tiering.</li><li>Ideal for applications requiring access to all the files at all times.</li><li>Useful in low bandwidth servers where we do not want tiered files for data access performance issues.</li></ul> |
| **Implications** | The CPU/memory should be sized based on the namespace scale and resource needs to avoid I/O performance issues. For details, see [Recommended System Resources for Azure File Sync](file-sync-planning.md#recommended-system-resources) | - | <ul><li>Volume should have enough space to store all the data. Initial download will likely take much longer due to the need for downloading all file content.</li><li>Not suitable for fast disaster recovery as this is slower than the first two options.</li></ul> |

After you select an initial download option, you can't change it after you confirm to create the server endpoint. 

> [!NOTE]
> When adding a server endpoint and files exist in the Azure file share, if you choose to download the namespace first, files will show up as tiered until they're downloaded locally. Files are downloaded using a single thread by default to limit network bandwidth usage. To improve the file download performance, use the [Invoke-StorageSyncFileRecall](file-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk) cmdlet with a thread count greater than 1.

### File download behavior once initial download completes

How files appear on the server after initial download finishes depends on your use of the cloud tiering feature and whether or not you opted to [proactively recall changes in the cloud](file-sync-cloud-tiering-overview.md#proactive-recalling). The latter is a feature useful for sync groups with multiple server endpoints in different geographic locations.

* **Cloud tiering is enabled** </br> New and changed files from other server endpoints will appear as tiered files on this server endpoint. These changes will only come down as full files if you opted for [proactive recall](file-sync-cloud-tiering-overview.md#proactive-recalling) of changes in the Azure file share by other server endpoints.
*  **Cloud tiering is disabled** </br> New and changed files from other server endpoints will appear as full files on this server endpoint. They won't appear as tiered files first and then recalled. Tiered files with cloud tiering off are a fast disaster recovery feature and appear only during initial provisioning.

### Provisioning steps

When a new server endpoint is created using the portal or PowerShell, the server endpoint isn't ready to be used immediately. Depending on how much data is present on the corresponding file share in the cloud, it might take few minutes to hours for the server endpoint to be functional and ready to use.

In the past, if you wanted to check the status of the server endpoint provisioning status and whether the server is ready for users to access data, you had to log in to the server endpoint and see if all the data had been downloaded. With provisioning steps, you can understand whether a server endpoint is ready to use or not and if the sync is fully functional directly from the Azure portal, in the server endpoint overview blade.

For supported scenarios, the **Provisioning steps** tab provides information on what's happening on the server endpoint, including when the server endpoint is ready for user access.

#### Supported scenarios

Currently, provisioning steps are only displayed when the new server endpoint being added has no data on the server path selected for the server endpoint. In other scenarios, the provisioning steps tab isn't available.

#### Provisioning status

Here are the different statuses that are displayed when server endpoint provisioning is in progress and what they mean:

* In progress: The server endpoint isn't ready for user access.
* Ready (sync not functional): Users can access data, but changes won't sync to cloud file share.
* Ready (sync functional): Users can access data and changes will be synced to the cloud share, making the endpoint fully functional.
* Failed: Provisioning failed because of an error.

The provisioning steps tab is only visible in the Azure portal for supported scenarios. It won't be available or visible for unsupported scenarios.

## Next steps

There's more to discover about Azure file shares and Azure File Sync. The following articles will help you understand advanced options, best practices, and troubleshooting.

* [Migration overview](../files/storage-files-migration-overview.md)
* [Planning for an Azure File Sync deployment](../file-sync/file-sync-planning.md)
* [Create a file share](../files/storage-how-to-create-file-share.md)
* [Troubleshoot Azure File Sync](/troubleshoot/azure/azure-storage/file-sync-troubleshoot?toc=/azure/storage/file-sync/toc.json)
