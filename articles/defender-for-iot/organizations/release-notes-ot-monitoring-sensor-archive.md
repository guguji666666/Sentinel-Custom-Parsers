---
title: OT monitoring software versions archive for Microsoft Defender for IoT for organizations
description: This article lists Microsoft Defender for IoT on-premises OT monitoring software versions archive released more than six months ago, including release and support dates and highlights for new features.
ms.topic: concept-article
ms.date: 04/06/2025
---

# OT monitoring software versions archive for in Microsoft Defender for IoT for organizations

[!INCLUDE [Banner for top of topics](../includes/banner.md)]

This article serves as an archive for OT monitoring software versions released for Microsoft Defender for IoT for organizations more than six months ago.

For more recent updates, see [OT monitoring software versions in Microsoft Defender for IoT?](release-notes.md).

Noted features listed below are in PREVIEW. The [Azure Preview Supplemental Terms](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include other legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

## Versioning and support for on-premises software versions

This section describes the servicing information, timelines, and guidance for the available on-premises software versions.

> [!NOTE]
> If you have an on-premises management console, make sure to also update your on-premises management console to the same version as your sensors.
>

For more information, see [Update Defender for IoT OT monitoring software](update-ot-software.md).

### OT monitoring software versions (sensor versions)

Cloud features may be dependent on a specific sensor version. Such features are listed below for the relevant software versions, and are only available for data coming from sensors that have the required version installed, or higher.

> [!IMPORTANT]
> The on-premises management console isn't supported or available for download after January 1st, 2025. For more information, see [on-premises management console retirement](ot-deploy/on-premises-management-console-retirement.md).
>

| Version / Patch |  Release date | Scope     | Supported until |
| ------- |  ------------ | ----------- | ------------------- |
| **23.2** | | | |
| 23.2.0 | 12/2023 | Major | 12/2024 |
| **23.1** | | | |
| 23.1.3 | 09/2023 | Patch | 08/2024 |
| 23.1.2 | 07/2023 | Major | 06/2024 |
| **22.3** | | | |
|22.3.10 | 07/2023 | Patch | 06/2024 |
| 22.3.9 | 05/2023 | Patch | 04/2024 |
| 22.3.8 | 04/2023 | Patch | 03/2024 |
| 22.3.7 | 03/2023 | Patch | 02/2024 |
| 22.3.6 | 03/2023 | Patch | 02/2024 |
| 22.3.5 | 01/2023 | Patch | 12/2023 |
| 22.3.4 | 01/2023 | Major | 12/2023 |
| **22.2** | | | |
| 22.2.9 | 01/2023 | Patch | 12/2023 |

### Threat intelligence updates

Threat intelligence updates are continuously available and are independent of specific sensor versions. You don't need to update your sensor version in order to get the latest threat intelligence updates.

For more information, see [Threat intelligence research and packages](how-to-work-with-threat-intelligence-packages.md).

### Support model

Defender for IoT provides **1 year of support** for every new version, starting with versions **22.1.7** and **22.2.7**. For example, version **22.2.7** was released in **October 2022** and is supported through **September 2023**.

Earlier versions use a legacy support model, with support dates detailed for each version.

### On-premises appliance security

The OT network sensor and the on-premises management console are designed as a *locked-down* security appliance with a hardened attack surface. Appliance access and control are allowed only through the [management port](best-practices/understand-network-architecture.md), via HTTP for web access and SSH for the support shell.

Defender for IoT adheres to the [Microsoft Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl/) throughout the entire development lifecycle, including activities like training, compliance, code reviews, threat modeling, design requirements, component governance, and pen testing. All appliances are locked down according to industry best practices and shouldn't be modified.

Maintain your sensors and on-premises management consoles, for activities like backups, log exports, or health monitoring, via the web interface, or the Defender for IoT [CLI commands](references-work-with-defender-for-iot-cli-commands.md).

> [!IMPORTANT]
> Manual changes to software packages or additions of external packages may have detrimental security or functional effects on the sensor and on-premises management console. Microsoft is unable to support deployments with manual changes made to software packages.
>

### Feature documentation per versions

Version numbers are listed only in this article and in the [What's new in Microsoft Defender for IoT?](whats-new.md) article, and not in detailed descriptions elsewhere in the documentation.

To understand whether a feature is supported in your sensor version, check the relevant version section below and its listed features.

## Versions 23.2.x

### Version 23.2.0

**Release date**: 12/2023

**Supported until**: 12/2024

This version includes the following updates and enhancements:

- [Sensor software runs on a Debian 11 operating system](ot-deploy/install-software-ot-sensor.md) and [updates to this version may be heavier and longer than usual](whats-new-archive.md#ot-network-sensors-now-run-on-debian-11)
- [The legacy, privileged default *support* user is replaced by the default *admin* user](roles-on-premises.md#legacy-users)

> [!IMPORTANT]
> If you're updating your software from a legacy version and have the *support* credentials saved, such as in CLI scripts, we recommend that you update those credentials to use the *admin* user instead.

## Versions 23.1.x

### Version 23.1.3

**Release date**: 09/2023

**Supported until**: 08/2024

This version includes the following updates and enhancements:

- [Connectivity troubleshooting enhancements from the OT sensor](how-to-troubleshoot-sensor.md#check-sensor---cloud-connectivity-issues)
- [Read Only users can access the Event Timeline](roles-on-premises.md)

### Version 23.1.2

**Release date**: 07/2023

**Supported until**: 06/2024

This version includes the following updates and enhancements:

- [Simplified installation process](ot-deploy/install-software-ot-sensor.md)
- [A new sensor setup wizard from the UI](ot-deploy/activate-deploy-sensor.md)
- [Analyze sensor connectivity](how-to-manage-individual-sensors.md)
- [UI enhancements for downloading PCAP files from the sensor](how-to-view-alerts.md#access-alert-pcap-data)
- [*cyberx* and *cyberx_host* users aren't enabled by default](roles-on-premises.md#default-privileged-on-premises-users)

> [!NOTE]
> Due to internal improvements to the OT sensor's device inventory, column edits made to your device inventory aren't retained after updating to version 23.1.2. If you'd previously edited the columns shown in your device inventory, you'll need to make those same edits again after updating your sensor.
>

## Versions 22.3.x

### 22.3.10

**Release date**: 07/2023

**Supported until**: 06/2024

This version includes bug fixes for stability improvements.

### 22.3.9

**Release date**: 05/2023

**Supported until**: 04/2024

This version includes:

- [Improved monitoring and support for OT sensor logs](whats-new-archive.md#improved-monitoring-and-support-for-ot-sensor-logs)
- Bug fixes for stability improvements.

### 22.3.8

**Release date**: 04/2023

**Supported until**: 03/2024

- [Enrich Windows workstation and server data with a local script (Public preview)](detect-windows-endpoints-script.md)
- [Automatically resolved notifications for operating system changes and device type changes](how-to-work-with-the-sensor-device-map.md#device-notification-responses)
- [UI enhancements when uploading SSL/TLS certificates](how-to-deploy-certificates.md#deploy-a-certificate-on-an-ot-sensor)

### 22.3.6 / 22.3.7

<a name=22.3.7></a>

**Release date**: 03/2023

**Supported until**: 02/2024

Version 22.3.7 includes the same features as 22.3.6. If you have version 22.3.6 installed, we strongly recommend that you update to version 22.3.7, which also includes important bug fixes.

- [Support for transient devices](device-inventory.md#supported-devices)
- [Autoresolved notifications](how-to-work-with-the-sensor-device-map.md#device-notification-responses)
- [Device data retention updated to 90 days](references-data-retention.md#device-data-retention-periods)
- [Merging](how-to-investigate-sensor-detections-in-a-device-inventory.md#merge-devices) and [deleting](how-to-investigate-sensor-detections-in-a-device-inventory.md#delete-devices) devices on OT sensors now include confirmation messages when the action has completed
- Support for [deleting multiple devices](how-to-investigate-sensor-detections-in-a-device-inventory.md#delete-devices) on OT sensors
- An enhanced [editing device details](how-to-investigate-sensor-detections-in-a-device-inventory.md#edit-device-details) process on the OT sensor, using an **Edit** button in the toolbar at the top of the page
- [Enhanced UI on the OT sensor for uploading an SSL/TLS certificate](ot-deploy/activate-deploy-sensor.md#define-ssltls-certificate-settings)
- [Activation files for locally managed sensors no longer expire](how-to-manage-individual-sensors.md#upload-a-new-activation-file)
- Severity for all [**Suspicion of Malicious Activity**](alert-engine-messages.md#malware-engine-alerts) alerts is now **Critical**
- [Allow internet connections on an OT network in bulk](how-to-accelerate-alert-incident-response.md#allow-internet-connections-on-an-ot-network)
- [Security recommendations for OT networks for insecure or missing passwords](recommendations.md#supported-security-recommendations)

### 22.3.5

**Release date**: 01/2023

**Supported until**: 12/2023

This version includes bug fixes for stability improvements.

### 22.3.4

**Release date**: 01/2021

**Supported until**: 12/2023

- [Azure connectivity status shown on OT sensors](how-to-manage-individual-sensors.md#validate-connectivity-status)
- [Configure Active Directory and NTP settings in the Azure portal](configure-sensor-settings-portal.md#active-directory)

## Versions 22.2.x

To update to 22.2.x versions:

- **From version 22.1.x**, update directly to the latest **22.2.x** version
- **From version 10.x**, first update to the latest **22.1.x** version, and then update again to the latest **22.2.x** version.

For more information, see [Update Defender for IoT OT monitoring software](update-ot-software.md).

### 22.2.9

**Release date**: 01/2023

**Supported until**: 12/2023

This version includes bug fixes for stability improvements.

### 22.2.8

**Release date**: 11/2022

**Supported until**: 10/2023

This version includes bug fixes for stability improvements.

### 22.2.7

**Release date**: 10/2022

**Supported until**: 09/2023

This version includes bug fixes for stability improvements.

### 22.2.6

**Release date**: 09/2022

**Supported until**: 04/2023

This version includes the following new updates and fixes:

- Bug fixes and stability improvements
- Enhancements to the device type classification algorithm

### 22.2.5

**Release date**: 08/2022

**Supported until**: 04/2023

This version includes minor stability improvements.

### 22.2.4

**Release date**: 07/2022

**Supported until**: 04/2023

This version includes the following new updates and fixes:

- [Device inventory enhancements in the sensor console](how-to-investigate-sensor-detections-in-a-device-inventory.md):

  - Merge duplicate devices, delete single devices, and delete inactive devices by admin users
  - **Last seen** value in the device details pane is replaced by **Last activity**

- [New parameters for the *devicecves* API](api/management-integration-apis.md): `sensorId`, `score`, and `deviceIds`

- [New alert columns with timestamp data](how-to-view-alerts.md): **Last detection**, **First detection**, and **Last activity**

### 22.2.3

**Release date**: 07/2022

**Supported until**: 04/2023

This version includes the following new updates and fixes:

- [Define and view OT sensor settings from the Azure portal](configure-sensor-settings-portal.md)
- [Update your sensors from the Azure portal](update-ot-software.md#update-ot-sensors-with-the-latest-ot-monitoring-software)
- [New naming convention for hardware profiles](ot-appliance-sizing.md)
- [PCAP access from the Azure portal](how-to-manage-cloud-alerts.md)
- [Bi-directional alert synch between OT sensors and the Azure portal](alerts.md#managing-ot-alerts-in-a-hybrid-environment)
- [Sensor connections restored after certificate rotation](how-to-manage-individual-sensors.md#manage-ssltls-certificates)
- [Upload diagnostic logs for support tickets from the Azure portal](how-to-manage-sensors-on-the-cloud.md#upload-a-diagnostics-log-for-support)
- [Improved security for uploading protocol plugins](resources-manage-proprietary-protocols.md)
- [Sensor names shown in browser tabs](how-to-manage-individual-sensors.md)
- [Site-based access control on the Azure portal](manage-users-portal.md#manage-site-based-access-control-public-preview)


## Versions 22.1.x

Software versions 22.1.x support direct updates to the latest OT monitoring software versions available. For more information, see [Update Defender for IoT OT monitoring software](update-ot-software.md).

### 22.1.7

**Release date**: 07/2022

**Supported until**: 06/2023

This version includes the following new updates and fixes:

- [Identical passwords for *cyberx_host* and *cyberx* users created during installations and updates](how-to-install-software.md)

### 22.1.6

**Release date**: 06/2022

**Supported until**: 10/2022

This version minor maintenance updates for internal sensor components.

### 22.1.5

**Release date**: 06/2022

**Supported until**: 10/2022

This version minor updates to improve TI installation packages and software updates.

### 22.1.4

**Release date**: 04/2022

**Supported until**: 10/2022

This version includes the following new updates and fixes:

- [Extended device property data in the **Device inventory** page on the Azure portal](how-to-manage-device-inventory-for-organizations.md), for the **Description**, **Tags**. **Protocols**, **Scanner**, and **Last Activity** fields

### 22.1.3

**Release date**: 03/2022

**Supported until**: 10/2022

This version includes the following new updates and fixes:

- [Diagnostic logs automatically available to support for cloud-connected sensors](how-to-troubleshoot-sensor.md#download-a-diagnostics-log-for-support)
- [Rockwell protocol: Device inventory shows PLC operating mode key state, run state, and security mode](how-to-manage-device-inventory-for-organizations.md)
- [Automatic CLI session timeouts](references-work-with-defender-for-iot-cli-commands.md)
- [Sensor health widgets in the Azure portal](how-to-manage-sensors-on-the-cloud.md#understand-sensor-health)

### 22.1.1

**Release date**: 02/2022

**Supported until**: 10/2022

This version includes the following new updates and fixes:

- [New sensor installation wizard](how-to-install-software.md)

- [Sensor redesign and unified Microsoft product experience](how-to-manage-individual-sensors.md)

- [Enhanced sensor Overview page](how-to-manage-individual-sensors.md)

- [New sensor diagnostics log](how-to-troubleshoot-sensor.md#download-a-diagnostics-log-for-support)

- [Alert updates](how-to-view-alerts.md):

  - Contextual data for each alert
  - Refreshed alert statuses
  - Alert storage updates
  - A new **Backup Activity with Antivirus Signatures** alert
  - Alert management changes during software updates

- [Enhancements for creating custom alerts on the sensor](how-to-accelerate-alert-incident-response.md#create-custom-alert-rules-on-an-ot-sensor): Hit count data, advanced scheduling options, and more supported fields and protocols

- [Modified CLI commands](cli-ot-sensor.md): Including the following new commands:

  - `sudo dpkg-reconfigure iot-sensor`
  - `sudo dpkg-reconfigure iot-sensor`
  - `sudo dpkg-reconfigure iot-sensor`

- [Refreshed update process and update log](update-ot-software.md)

- [New connectivity models](architecture-connections.md)

- [New firewall requirements](networking-requirements.md#sensor-access-to-azure-portal)

- [Improved support for Profinet DCP, Honeywell, and Windows endpoint detection protocols](concept-supported-protocols.md)

- [Sensor reports now accessible from the **Data Mining** page](how-to-create-data-mining-queries.md)

- [Updated process for sensor name changes](how-to-manage-individual-sensors.md#upload-a-new-activation-file)

- [Site-based access control on the Azure portal](manage-users-portal.md#manage-site-based-access-control-public-preview)
