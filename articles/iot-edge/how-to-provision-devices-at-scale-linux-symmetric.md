---
title: Create and provision Azure IoT Edge devices using symmetric keys on Linux
description: 'Deploy IoT Edge at scale: Use symmetric keys to provision multiple Linux devices with Azure IoT Hub device provisioning service.'
author: PatAltimore
ms.author: patricka
ms.date: 05/16/2025
ms.topic: concept-article
ms.service: azure-iot-edge
ms.custom:
  - linux-related-content
  - ai-gen-docs-bap
  - ai-gen-description
  - ai-seo-date:05/16/2025
  - build-2025
services: iot-edge
---
# Create and provision IoT Edge devices at scale on Linux using symmetric keys

[!INCLUDE [iot-edge-version-all-supported](includes/iot-edge-version-all-supported.md)]

This article gives step-by-step instructions for setting up one or more Linux IoT Edge devices using symmetric keys. Automatically set up Azure IoT Edge devices with the [Azure IoT Hub device provisioning service](../iot-dps/index.yml) (DPS). If you aren't familiar with the autoprovisioning process, review the [provisioning overview](../iot-dps/about-iot-dps.md#provisioning-process) before you continue.

Here are the main tasks:

1. Create an **individual enrollment** for a single device or a **group enrollment** for a set of devices.
1. Install the IoT Edge runtime and connect to the IoT Hub.

>[!TIP]
>For a simplified experience, try the [Azure IoT Edge configuration tool](https://github.com/azure/iot-edge-config). This command-line tool, currently in public preview, installs IoT Edge on your device and provisions it using DPS and symmetric key attestation.

Symmetric key attestation is a simple way to authenticate a device with a device provisioning service instance. This method is a "Hello world" experience for developers who are new to device provisioning or don't have strict security requirements. Device attestation with a [TPM](../iot-dps/concepts-tpm-attestation.md) or [X.509 certificates](../iot-dps/concepts-x509-attestation.md) is more secure, and you should use it for more stringent security needs.

## Prerequisites

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### Device requirements

Use a physical or virtual Linux device as the IoT Edge device.

Define a *unique* **registration ID** to identify each device. Use the MAC address, serial number, or any unique information from the device. For example, combine a MAC address and serial number to form a registration ID like `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`. Valid characters are lowercase alphanumeric and dash (`-`).

<!-- Create a DPS enrollment using symmetric keys H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-symmetric.md](includes/iot-edge-create-dps-enrollment-symmetric.md)]

<!-- Install IoT Edge on Linux H2 and content -->
[!INCLUDE [install-iot-edge-linux.md](includes/iot-edge-install-linux.md)]

## Provision the device with its cloud identity

Once the runtime is installed on your device, configure the device with the information it uses to connect to the device provisioning service and IoT Hub.

Have the following information ready:

* The DPS **ID Scope** value
* The device **Registration ID** you created
* Either the **Primary Key** from an individual enrollment, or a [derived key](#derive-a-device-key) for devices using a group enrollment.

Create a configuration file for your device based on a template file that is provided as part of the IoT Edge installation.

# [Ubuntu / Debian / RHEL](#tab/ubuntu+debian+rhel)

```bash
sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
```

Open the configuration file on the IoT Edge device.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

# [Ubuntu Core snaps](#tab/snaps)

If you use a snap installation of IoT Edge, the template file is at `/snap/azure-iot-edge/current/etc/aziot/config.toml.edge.template`. Copy the template file to your home directory and name it config.toml. For example:

```bash
cp /snap/azure-iot-edge/current/etc/aziot/config.toml.edge.template ~/config.toml
```

Open the configuration file in your home directory on the IoT Edge device.

```bash
nano ~/config.toml
```

---

1. Find the **Provisioning** section of the file. Uncomment the lines for DPS provisioning with symmetric key, and make sure all other provisioning lines are commented out.

    ```toml
    # DPS provisioning with symmetric key
    [provisioning]
    source = "dps"
    global_endpoint = "https://global.azure-devices-provisioning.net"
    id_scope = "PASTE_YOUR_SCOPE_ID_HERE"

    # Uncomment to send a custom payload during DPS registration
    # payload = { uri = "PATH_TO_JSON_FILE" }
    
    [provisioning.attestation]
    method = "symmetric_key"
    registration_id = "PASTE_YOUR_REGISTRATION_ID_HERE"
    
    symmetric_key = { value = "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE" }
    
    # auto_reprovisioning_mode = Dynamic
    ```

1. Update the values of `id_scope`, `registration_id`, and `symmetric_key` with your DPS and device information.

   The symmetric key parameter can accept an inline key, a file URI, or a PKCS#11 URI. Uncomment only one symmetric key line, based on the format you use. If you use an inline key, use a base64-encoded key like the example. If you use a file URI, your file must contain the raw bytes of the key.

   If you use any PKCS#11 URIs, find the **PKCS#11** section in the config file and enter your PKCS#11 configuration information.

    For more information about provisioning configuration settings, see [Configure IoT Edge device settings](configure-device.md#provisioning).

1. Optionally, find the auto reprovisioning mode section of the file. Use the `auto_reprovisioning_mode` parameter to set your device's reprovisioning behavior. **Dynamic** - Reprovision when the device detects that it can be moved from one IoT Hub to another. This is the default. **AlwaysOnStartup** - Reprovision when the device is rebooted or a crash causes the daemons to restart. **OnErrorOnly** - Never trigger device reprovisioning automatically. Each mode has an implicit device reprovisioning fallback if the device can't connect to IoT Hub during identity provisioning because of connectivity errors. For more information, see [IoT Hub device reprovisioning concepts](../iot-dps/concepts-device-reprovision.md).

1. Optionally, uncomment the `payload` parameter to specify the path to a local JSON file. The contents of the file are [sent to DPS as additional data](../iot-dps/how-to-send-additional-data.md#iot-edge-support) when the device registers. This is useful for [custom allocation](../iot-dps/how-to-use-custom-allocation-policies.md). For example, if you want to allocate your devices based on an IoT Plug and Play model ID without human intervention.

1. Save and close the file.

1. Apply the configuration changes you made on the device.

    # [Ubuntu / Debian / RHEL](#tab/ubuntu+debian+rhel)
    ```bash
    sudo iotedge config apply
    ```
    
    # [Ubuntu Core snaps](#tab/snaps)
    
    ```bash
    sudo snap set azure-iot-edge raw-config="$(cat ~/config.toml)"
    ```
    
    ---
    
## Check successful installation

If the runtime starts successfully, go to your IoT Hub and start deploying IoT Edge modules to your device.

# [Individual enrollment](#tab/individual-enrollment)

Check that the individual enrollment you created in device provisioning service is used. Go to your device provisioning service instance in the Azure portal. Open the enrollment details for the individual enrollment you created. The status of the enrollment is **assigned**, and the device ID is listed.

# [Group enrollment](#tab/group-enrollment)

Check that the group enrollment you created in device provisioning service is used. Go to your device provisioning service instance in the Azure portal. Open the enrollment details for the group enrollment you created. Go to the **Registration Records** tab to view all devices registered in that group.

---

Run these commands on your device to check that IoT Edge installs and starts successfully.

1. Check the status of the IoT Edge service.

    ```cmd/sh
    sudo iotedge system status
    ```

1. View service logs.

    ```cmd/sh
    sudo iotedge system logs
    ```

1. List running modules.

    ```cmd/sh
    sudo iotedge list
    ```

## Next steps

The device provisioning service enrollment process lets you set the device ID and device twin tags when you set up a new device. Use these values to target individual devices or groups of devices with automatic device management. Learn how to [deploy and monitor IoT Edge modules at scale using the Azure portal](how-to-deploy-at-scale.md) or [using Azure CLI](how-to-deploy-cli-at-scale.md).
