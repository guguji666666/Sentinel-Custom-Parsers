---
title: Upgrade HCX on Azure VMware Solution 
description: This article explains how to upgrade HCX on Azure VMware Solution. 
ms.topic: how-to
ms.date: 05/23/2025
ms.custom: engagement-fy23
# Customer intent: As an IT administrator managing Azure VMware Solution, I want to upgrade HCX to the latest version, so that I can ensure access to new features, security patches, and continued support while minimizing system downtime.
---

# Upgrade HCX on Azure VMware Solution

In this article, you learn how to upgrade Azure VMware Solution for HCX service updates, which can include new features, software fixes, or security patches. 

You can update HCX Connector and HCX Cloud systems during separate maintenance windows, but for optimal compatibility, we recommend you update both systems together. Apply service updates during a maintenance window where no new HCX operations are queued up. 

>[!IMPORTANT]
>Starting with HCX 4.4.0, HCX appliances install the VMware Photon Operating System. When upgrading to HCX 4.4.x or later from an HCX version prior to version 4.4.0, you must also upgrade all Service Mesh appliances. 

## System requirements 

- For systems requirements, compatibility, and upgrade prerequisites, see the [VMware HCX release notes](https://techdocs.broadcom.com/us/en/vmware-cis/hcx/vmware-hcx/4-10/hcx-4-10-release-notes/Chunk174419121.html#Chunk174419121).  

- For more information about the upgrade path, see the [Product Interoperability Matrix](https://interopmatrix.broadcom.com/Upgrade?productId=660). 
- For information regarding VMware product compatibility by version, see the [Compatibility Matrix](https://interopmatrix.broadcom.com/Interoperability?col=660,&row=0,).
- Review VMware Software Versioning, Skew, and Legacy Support Policies [here](https://techdocs.broadcom.com/us/en/vmware-cis/hcx/vmware-hcx/4-10/vmware-hcx-support-and-lifecycle-policies-4-10/software-versioning-skew-and-legacy-support-policies.html).

- Ensure HCX manager and site pair configurations are healthy.  

- As part of HCX update planning, and to ensure that HCX components are updated successfully, review the service update considerations and requirements. For planning HCX upgrade, see [Planning for HCX Updates](https://techdocs.broadcom.com/us/en/vmware-cis/hcx/vmware-hcx/4-10/vmware-hcx-user-guide-4-10/updating-vmware-hcx/planning-for-hcx-updates.html). 

- Ensure that you have a backup and snapshot of HCX connector in the on-premises environment, if applicable.
- For more information, see the [HCX support policy for legacy vSphere environment](https://knowledge.broadcom.com/external/article?legacyId=82702).
- Check that you're using the [latest VMware HCX version validated with Azure VMware Solution](introduction.md#vmware-software-versions).

### Backup HCX 
- Azure VMware Solution backs up HCX Cloud Manager configuration daily.
- Use the appliance management interface to create backup of HCX in on-premises, see [Backing Up HCX Manager](https://techdocs.broadcom.com/us/en/vmware-cis/hcx/vmware-hcx/4-10/vmware-hcx-user-guide-4-10/backing-up-and-restoring-hcx-manager/backing-up-hcx-manager.html). You can use the configuration backup to restore the appliance to its state before the backup. The contents of the backup file supersede configuration changes made before restoring the appliance. 
- HCX cloud manager snapshots are taken automatically during upgrades to HCX 4.4 or later. HCX retains automatic snapshots for 24 hours before deleting them. 

- You can use HCX Run commands to take an HCX Cloud Manager snapshot, which is retained for 72 hours, see [HCX Run commands](/azure/azure-vmware/use-hcx-run-commands)

- To help with reverting from a snapshot, [create a support ticket](https://ms.portal.azure.com/#view/Microsoft_Azure_Support/HelpAndSupportBlade/~/overview). 

## Upgrade HCX 
The upgrade process is in two steps: 
1. Upgrade HCX Manager  
      1. HCX cloud manager  
      1. HCX connector (You can update site-paired HCX Managers simultaneously) 
1. Upgrade HCX Service Mesh appliances 

### Upgrade HCX manager
The HCX update is first applied to the HCX Manager systems.
 
**What to expect**
- HCX manager is rebooted as part of the upgrade process.  
- HCX vCenter Plugins are updated.  
- There's no data-plane outage during this procedure.

**Prerequisites**
- Verify the HCX Manager system reports healthy connections to the connected (vCenter Server, NSX Manager (if applicable). 
- Verify the HCX Manager system reports healthy connections to the HCX Interconnect service components. (Ensure HCX isn't in an out of sync state)
- Verify that Site Pair configurations are healthy. 
- No VM migrations should be in progress during this upgrade.

**Procedure**

To follow the HCX Manager upgrade process, see [Upgrading the HCX Manager](https://techdocs.broadcom.com/us/en/vmware-cis/hcx/vmware-hcx/4-10/vmware-hcx-user-guide-4-10/updating-vmware-hcx/hcx-service-update-procedures/upgrade-hcx-manager-for-connected-sites.html) 

### Upgrade HCX Service Mesh appliances 

While Service Mesh appliances are upgraded independently to the HCX Manager, they must be upgraded. These appliances are flagged for new available updates anytime the HCX Manager has newer software available.

**What to expect**

- Service VMs are rebooted as part of the upgrade.
- There's a small data plane outage during this procedure.
- In-service upgrade of Network-extension can be considered to reduce downtime during HCX Network extension upgrades. 

**Prerequisites**
- All paired HCX Managers on both the source and the target site are updated and all services are returned to a fully converged state. 
- Service Mesh appliances must be initiated using the HCX plug-in of vCenter or the 443 console at the source site 
- No VM migrations should be in progress during this upgrade. 

**Procedure**

To follow the Service Mesh appliances upgrade process, see [Upgrading the HCX Service Mesh Appliances](https://techdocs.broadcom.com/us/en/vmware-cis/hcx/vmware-hcx/4-10/vmware-hcx-user-guide-4-10/updating-vmware-hcx/hcx-service-update-procedures/upgrade-the-hcx-service-mesh-appliances.html)

## HCX 4.11.0 Upgrade and what it means for current HCX users

### Overview

Broadcom has announced the end-of-support (EOS) for VMware HCX version 4.10.x, effective July 27, 2025. To proactively address this change and ensure continued support, Microsoft will begin upgrading all Azure VMware Solution customers using HCX Manager to HCX version 4.11.0.
The upgrade process will commence in June 2025. Each customer will receive a notification at least one week prior to their scheduled upgrade window.

### What changes are introduced as part of HCX 4.11.0?

Refer to [HCX 4.11.0 release notes](https://techdocs.broadcom.com/us/en/vmware-cis/hcx/vmware-hcx/4-11/hcx-4-11-release-notes/vmware-hcx-411-release-notes.html) for more information on what’s new with HCX 4.11. Some noteworthy changes are listed below: 

#### Local Mode

From HCX 4.11.0 onwards, only HCX local mode is available. This means that HCX systems running 4.11.0 or later will no longer receive upgrade notifications under the System Updates section from Broadcom. Connected Sites that upgrade to 4.11.0 using offline bundles will operate in Local Mode after the upgrade.

#### Connection to VMware & Hybridity Depot

Activation Keys-based licensing is deprecated as of HCX 4.11.0. Activation Keys in HCX 4.11.0 will stop working 450 days after the upgrade to HCX 4.11.0 takes place. HCX systems running older versions that are currently using Activation Keys will stop working when connect.hcx.vmware.com is decommissioned.

- <ins>hybridity-depot.vmware.com</ins> and <ins>connect.hcx.vmware.com</ins> endpoints for licensing, activation, and updates are removed post-activation or upgrade to HCX 4.11.0. HCX services of <ins>hybridity-depot.vmware.com</ins> and <ins>connect.hcx.vmware.com</ins> will be decommissioned.
- All connected systems automatically transition to local licensing mode as part of upgrade to 4.11.0.

Moving forward, until customers are upgraded to HCX 4.11.0, they'll need to submit a Support Request to receive the requested upgrade bundle for their chosen HCX connection version. After the upgrade has taken place, customers will find previous and current versions of HCX Connector bundles, including HCX 4.11.0, in their vSAN datastores under a folder named _"AVS_Official_HCX_Connector_Binaries"_.

>[!NOTE]
> The following HCX functionality is **deprecated** in HCX 4.11.0 and will be **removed** in a future release. HCX 4.11.0 will no longer be supported as of December 24, 2025. Customers should plan to migrate to an alternative solution at the earliest if they use any of the following features:
> - HCX V2T Migration 
> - HCX WAN Optimization 
> - HCX Disaster Recovery 
> - vCenter Server Plug-in for HCX 
> - HCX UI - Tracking page in Migration interface


### What actions will the customer need to take?

To ensure smooth migration, customers are required to upgrade any paired HCX connectors and service mesh appliances to HCX 4.11.0.  Furthermore, customers may be required to execute a resync operation on each HCX service mesh on both the source and connector sides to ensure that no errors have occurred due to the upgrade.

## FAQ 

### What is the impact of an HCX upgrade? 

Apply service updates during a maintenance window where no new HCX operations and migration are queued up.  
For individual HCX component upgrade impact, see [Planning for HCX Updates](https://techdocs.broadcom.com/us/en/vmware-cis/hcx/vmware-hcx/4-10/vmware-hcx-user-guide-4-10/updating-vmware-hcx/planning-for-hcx-updates.html). 

### Do I need to upgrade the service mesh appliances? 

The HCX Service Mesh can be upgraded once all paired HCX Manager systems are updated, and all services are returned to a fully converged state. Check HCX release notes for upgrade requirements. Starting with HCX 4.4.0, HCX appliances installed the VMware Photon Operating System. When upgrading to HCX 4.4.x or later from an HCX version prior to 4.4.0 version, you must upgrade all Service Mesh appliances. 

### How do I roll back HCX upgrade using a snapshot? 

See [Rolling Back an Upgrade Using Snapshots](https://techdocs.broadcom.com/us/en/vmware-cis/hcx/vmware-hcx/4-10/vmware-hcx-user-guide-4-10/updating-vmware-hcx/hcx-service-update-procedures/rolling-back-an-upgrade-using-snapshots.html) to roll back the upgrade. 

## Next steps 
[Software Versioning, Skew, and Legacy Support Policies](https://techdocs.broadcom.com/us/en/vmware-cis/hcx/vmware-hcx/4-10/vmware-hcx-support-and-lifecycle-policies-4-10/software-versioning-skew-and-legacy-support-policies.html)  

[Updating VMware HCX](https://techdocs.broadcom.com/us/en/vmware-cis/hcx/vmware-hcx/4-10/vmware-hcx-user-guide-4-10/updating-vmware-hcx.html) 
