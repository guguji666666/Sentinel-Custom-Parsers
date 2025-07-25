---
title: Delete an application volume group in Azure NetApp Files  | Microsoft Docs
description: Describes how to delete an application volume group.
services: azure-netapp-files
author: b-hchen
ms.service: azure-netapp-files
ms.topic: how-to
ms.date: 03/27/2025
ms.author: anfdocs
# Customer intent: "As a cloud administrator, I want to delete an application volume group, so that I can manage storage resources efficiently after removing all contained volumes."
---
# Delete an application volume group

This article describes how to delete an application volume group.

> [!IMPORTANT]
> You can delete a volume group only if it contains no volumes. Before deleting a volume group, delete all volumes in the group. If the volume group contains _any_ volume, an error message prevents you from deleting the volume group.
>
> Network interfaces related to volumes groups cannot be deleted manually. They are deleted automatically when the volume group is deleted.

## Steps

1. Select **Application volume groups**. Select the volume group you want to delete.

2. To delete the volume group, select **Delete**. If you are prompted, type the volume group name to confirm the deletion.  

    :::image type="content" source="./media/application-volume-group-delete/application-volume-group-delete.png" alt-text="Screenshot of create application volume group without volumes..":::



## Next steps  

* [Application volume group FAQs](faq-application-volume-group.md)
* [Troubleshoot application volume group errors](troubleshoot-application-volume-groups.md)
