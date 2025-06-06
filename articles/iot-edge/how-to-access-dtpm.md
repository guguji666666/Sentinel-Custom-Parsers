---
title: dTPM access for Azure IoT Edge for Linux on Windows
description: Learn about how to configure access the dTPM on your  Azure IoT Edge for Linux on Windows virtual machine.
author: PatAltimore
ms.author: patricka
ms.date: 06/05/2025
ms.topic: concept-article
ms.service: azure-iot-edge
ms.custom: linux-related-content
services: iot-edge
---

# dTPM access for Azure IoT Edge for Linux on Windows

[!INCLUDE [iot-edge-version-all-supported](includes/iot-edge-version-all-supported.md)]

A trusted platform module (TPM) chip is a secure crypto-processor that carries out cryptographic operations. This technology provides hardware-based security functions. The Azure IoT Edge for Linux on Windows (EFLOW) virtual machine doesn't have a virtual TPM attached. However, you can enable or disable the TPM passthrough feature, which lets the EFLOW virtual machine use the Windows host OS TPM. The TPM passthrough feature lets you:

- Use TPM technology for IoT Edge device provisioning with Device Provisioning Service (DPS)
- Get read-only access to cryptographic keys stored in the TPM

This article shows you how to write sample C# code to read cryptographic keys stored in the device TPM.  

> [!IMPORTANT]
> Access to TPM keys is limited to read-only. To write keys to the TPM, do it from the Windows host OS. 

## Prerequisites

- A Windows host OS with a TPM or vTPM (if you use a Windows host OS virtual machine).
- An EFLOW virtual machine with TPM passthrough enabled. In an elevated PowerShell session, run `Set-EflowVmFeature -feature "DpsTpm" -enable` to enable TPM passthrough. For more information, see [Set-EflowVmFeature to enable TPM passthrough](./reference-iot-edge-for-linux-on-windows-functions.md#set-eflowvmfeature).
- Make sure the NV index (default index=3001) is initialized with 8 bytes of data. The default AuthValue used by the sample is {1,2,3,4,5,6,7,8}, which matches the NV (Windows) sample in the TSS.MSR libraries when writing to the TPM. Initialize all indexes on the Windows host before reading from the EFLOW VM. For more information about TPM samples, see [TSS.MSR](https://github.com/microsoft/TSS.MSR).

    > [!WARNING]
    > Enabling TPM passthrough to the virtual machine can increase security risks.
    
## Create the dTPM executable

Follow these steps to create a sample executable to access a TPM index from the EFLOW VM. For more information about EFLOW TPM passthrough, see [Azure IoT Edge for Linux on Windows Security](./iot-edge-for-linux-on-windows-security.md).

1. Open Visual Studio 2019 or 2022.

1. Select **Create a new project**.

1. Choose **Console App** in the list of templates, and then select **Next**.

    ![Visual Studio create new solution](./media/how-to-access-dtpm/vs-new-solution.png)

1. Fill in the **Project Name**, **Location**, and **Solution Name** fields, and then select **Next**.

1. Choose a target framework. The latest .NET 6.0 LTS version is preferred. After you choose a target framework, select **Create**. Visual Studio creates a new console app solution.

1. In **Solution Explorer**, right-click the project name and select **Manage NuGet Packages**.

1. Select **Browse**, and then search for `Microsoft.TSS`. For more information about this package, see [Microsoft.TSS](https://www.nuget.org/packages/Microsoft.TSS).

1. Choose the **Microsoft.TSS** package from the list, and then select **Install**.

   :::image type="content" source="./media/how-to-access-dtpm/vs-nuget-microsoft-tss.png" alt-text="Screenshot of Visual Studio showing how to add NuGet packages.":::

1. Edit the *Program.cs* file, and replace the contents with the [EFLOW TPM sample code - Program.cs](https://raw.githubusercontent.com/Azure/iotedge-eflow/main/samples/tpm-read-nv/Program.cs).

1. Select **Build** > **Build solution** to build the project. Verify that the build is successful.

1. In **Solution Explorer**, right-click the project, and then select **Publish**.

1. In the **Publish** wizard, choose **Folder** > **Folder**. Select **Browse**, and choose an output location for the executable file to be generated. Select **Finish**. After the publish profile is created, select **Close**.

1. On the **Publish** tab, select **Show all settings** link. Change the following configurations then select **Save**. 
    - Target Runtime:  **linux-x64**.
    - Deployment mode: **Self-contained**.
    
   :::image type="content" source="./media/how-to-access-dtpm/sample-publish-options.png" alt-text="Screenshot of publish options.":::
 
1. Select **Publish**, and then wait for the executable to be created.

If publishing succeeds, you see the new files in your output folder.

## Copy and run the executable
Once the executable file and dependency files are created, you need to copy the folder to the EFLOW virtual machine. The following steps show you how to copy all the necessary files and how to run the executable inside the EFLOW virtual machine.

1. Start an elevated *PowerShell* session using **Run as Administrator**.

1. Change directory to the parent folder that contains the published files. 
    For example, if your published files are under the folder *TPM* in the directory `C:\Users\User`. You can use the following command to change to the parent folder.
    ```powershell
    cd "C:\Users\User"
    ```

1. Create a *tar* file with all the files created in previous steps.
    For example, if you have all your files under the folder *TPM*, you can use the following command to create the *TPM.tar* file.
    ```powershell
     tar -cvzf TPM.tar ".\TPM"
    ```

1. Once the *TPM.tar* file is created successfully, use the `Copy-EflowVmFile` cmdlet to copy the *tar* file created to the EFLOW VM.
    For example, if you have the *tar* file name *TPM.tar* in the directory `C:\Users\User`. you can use the following command to copy to the EFLOW VM.
    ```powershell
    Copy-EflowVmFile -fromFile "C:\Users\User\TPM.tar" -toFile "/home/iotedge-user/" -pushFile
    ```

1. Connect to the EFLOW virtual machine.
     ```powershell
    Connect-EflowVm
    ```

1. Change directory to the folder where you copied the *tar* file and check that the file is available. If you used the previous example, when you connect to the EFLOW VM, you're already at the *iotedge-user* root folder. Run the `ls` command to list the files and folders.

1. Run the following command to extract all the content from the *tar* file.
    ```bash
    tar -xvzf TPM.tar
    ```

1. After extraction, you should see a new folder with all the TPM files. 
1. Change directory to the *TPM* folder.
    ```bash
    cd TPM
    ```

1. Add executable permission to the main executable file. For example, if your project name was *TPMRead*, your main executable is named *TPMRead*. Run the following command to make it executable.
    ```bash
    chmod +x TPMRead
    ```

1. To solve an [ICU globalization issue](https://github.com/dotnet/core/issues/2186#issuecomment-472629489), run the following command. For example, if your project name is *TPMTest* run:
    ```bash
     sed -i '/"configProperties": /a \\t"System.Globalization.Invariant\": true,' TPMTest.runtimeconfig.json
    ```

1. The last step is to run the executable file. For example, if your project name is *TPMTest*, run the following command:
    ```bash
    ./TPMTest
    ```
    You should see an output similar to the following.

   :::image type="content" source="./media/how-to-access-dtpm/tpm-read-output.png" alt-text="Screenshot that shows EFLOW dTPM output.":::

## Next steps

Learn [how to develop IoT Edge modules with Linux containers using IoT Edge for Linux on Windows](./tutorial-develop-for-linux-on-windows.md).
