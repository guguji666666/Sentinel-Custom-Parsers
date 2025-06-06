---
title: Enable Diagnostic Logging
description: Learn how to enable diagnostic logging and add instrumentation to your application, along with how to access the information logged by Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: how-to
ms.date: 03/27/2025
ms.author: msangapu
author: msangapu-msft
ms.custom: devx-track-csharp, ai-video-demo, linux-related-content
ai-usage: ai-assisted
#customer intent: As an app developer, I want to understand troubleshooting in Azure App Service to fix issues with my app and make improvements.
---

# Enable diagnostic logging for apps in Azure App Service

Azure provides built-in diagnostics to assist with debugging an [Azure App Service app](overview.md). In this article, you learn how to enable diagnostic logging and add instrumentation to your application. You also learn how to access the information that Azure logs.

This article uses the [Azure portal](https://portal.azure.com) and the Azure CLI to work with diagnostic logs. For information on working with diagnostic logs by using Visual Studio, see [Troubleshoot an app in Azure App Service using Visual Studio](troubleshoot-dotnet-visual-studio.md).

In addition to the logging instructions in this article, you can use the Azure Monitor integrated logging capability. For more information, see [Send logs to Azure Monitor](#send-logs-to-azure-monitor).

> [!NOTE]
> App Service provides a dedicated, interactive diagnostic tool to help you troubleshoot your application. For more information, see [Azure App Service diagnostics overview](overview-diagnostics.md).
>
> You can also use other Azure services to improve the logging and monitoring capabilities of your app, such as [Azure Monitor](/azure/azure-monitor/app/azure-web-apps).

## Overview of logging types

| Type | Platform | Log storage location | Description |
|:-----|:---------|:---------------------|:------------|
| Application logging | Windows, Linux | App Service file system and/or Azure Storage blobs | Log messages that your application code generates. The messages can be generated by the web framework that you choose, or from your application code directly by using the standard logging pattern of your language. Each message is assigned one of the following categories: **Critical**, **Error**, **Warning**, **Info**, **Debug**, or **Trace**. You can select how verbose you want the logging to be by setting the severity level when you enable application logging.|
| Web server logging | Windows | App Service file system or Azure Storage blobs | Raw HTTP request data in the [W3C extended log file format](/windows/desktop/Http/w3c-logging). Each log message includes data such as the HTTP method, resource URI, client IP, client port, user agent, and response code. |
| Detailed error messages | Windows | App Service file system | Copies of the .htm error pages that would have been sent to the client browser. For security reasons, detailed error pages shouldn't be sent to clients in production. But App Service can save the error page each time an application error that has HTTP code 400 or higher occurs. The page might contain information that can help determine why the server returns the error code. |
| Failed request tracing | Windows | App Service file system | Detailed tracing information on failed requests, including a trace of the IIS components used to process the request and the time taken in each component. This information is useful if you want to improve site performance or isolate a specific HTTP error. One folder is generated for each failed request. The folder contains the XML log file and the XSL stylesheet for viewing the log file. |
| Deployment logging | Windows, Linux | App Service file system | Logs for when you publish content to an app. Deployment logging happens automatically, and there are no configurable settings for deployment logging. It helps you determine why a deployment failed. For example, if you use a [custom deployment script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script), you might use deployment logging to determine why the script is failing. |

When logs are stored in the App Service file system, they're subject to the available storage for your pricing tier. For more information, see [App Service limits](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-app-service-limits).

## Enable application logging (Windows)

To enable application logging for Windows apps in the [Azure portal](https://portal.azure.com):

1. Go to your app and select **Monitoring** > **App Service logs**.

1. Select **On** for either or both of these options:

   - **Application logging (Filesystem)**: This option is for temporary debugging purposes. It turns itself off in 12 hours.
   - **Application logging (Blob)**: This option is for long-term logging. It needs a blob storage container to write logs to.

     The **Blob** option includes additional information in the log messages, such as the ID of the origin virtual machine instance of the log message (`InstanceId`), the thread ID (`Tid`), and a more granular time stamp ([`EventTickCount`](/dotnet/api/system.datetime.ticks)).

1. For **Level**, select the level of details to log. The following table shows the log categories included in each level:

   | Level | Included categories |
   |:-|:-|
   |**Disabled** | None |
   |**Error** | **Error**, **Critical** |
   |**Warning** | **Warning**, **Error**, **Critical**|
   |**Information** | **Info**, **Warning**, **Error**, **Critical**|
   |**Verbose** | **Trace**, **Debug**, **Info**, **Warning**, **Error**, **Critical** (all categories) |

1. Select **Save**.

If you write logs to blobs, the retention policy no longer applies if you delete the app but keep the logs in the blobs. For more information, see [Costs that might accrue after resource deletion](overview-manage-costs.md#costs-that-might-accrue-after-resource-deletion).

Currently, only .NET application logs can be written to blob storage. Java, PHP, Node.js, and Python application logs can be stored only in the App Service file system without code modifications to write logs to external storage.

If you [regenerate your storage account's access keys](../storage/common/storage-account-create.md), you must reset the respective logging configuration to use the updated access keys:

1. On the **Configure** tab, set the respective logging feature to **Off**. Save your setting.

1. Enable logging to the storage account's blob again. Save your setting.

## <a name = "enable-application-logging-linuxcontainer"></a> Enable application logging (Linux or container)

To enable application logging for Linux apps or custom containers in the [Azure portal](https://portal.azure.com):

1. Go to your app and select **Monitoring** > **App Service logs**.

1. In **Application logging**, select **File System**.

1. In **Quota (MB)**, specify the disk quota for the application logs.

1. In **Retention Period (Days)**, set the number of days to retain the logs.

1. Select **Save**.

## Enable web server logging

To enable web server logging for Windows apps in the [Azure portal](https://portal.azure.com):

1. Go to your app and select **Monitoring** > **App Service logs**.

1. For **Web server logging**, select **Storage** to store logs in blob storage, or select **File System** to store logs in the App Service file system.

1. In **Retention Period (Days)**, set the number of days to retain the logs.

1. Select **Save**.

If you write logs to blobs, the retention policy no longer applies if you delete the app but keep the logs in the blobs. For more information, see [Costs that might accrue after resource deletion](overview-manage-costs.md#costs-that-might-accrue-after-resource-deletion).

If you [regenerate your storage account's access keys](../storage/common/storage-account-create.md), you must reset the respective logging configuration to use the updated keys:

1. On the **Configure** tab, set the respective logging feature to **Off**. Save your setting.

1. Enable logging to the storage account's blob again. Save your setting.

## Log detailed errors

To save the error page or failed request traces for Windows apps in the [Azure portal](https://portal.azure.com):

1. Go to your app and select **Monitoring** > **App Service logs**.

1. Under **Detailed error messages** or **Failed request tracing**, select **On**.

1. Select **Save**.

Both types of logs are stored in the App Service file system. It retains up to 50 errors (files or folders). When the number of HTML files exceeds 50, App Service deletes the oldest error files.

By default, failed request tracing captures a log of requests that failed with HTTP status codes between 400 and 600. To specify custom rules, override the `<traceFailedRequests>` section in the `Web.config` file.

## Add log messages in code

In your application code, you can use the usual logging facilities to send log messages to the application logs. For example:

- ASP.NET applications can use the [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) class to log information to the application diagnostic log. For example:

  ```csharp
  System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
  ```

  By default, ASP.NET Core uses the [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) logging provider. For more information, see [ASP.NET Core logging in Azure](/aspnet/core/fundamentals/logging/). For information about WebJobs SDK logging, see [Get started with the Azure WebJobs SDK](./webjobs-sdk-get-started.md#enable-console-logging).

- Python applications can use OpenTelemetry to send logs to the application diagnostic log. For more information, see [Enable Azure Monitor OpenTelemetry](/azure/azure-monitor/app/opentelemetry-enable).

## Stream logs

Before you stream logs in real time, enable the log type that you want. App Service streams any information written to the console output or files ending in .txt, .log, or .htm that are stored in the `/home/LogFiles` directory (`D:\home\LogFiles`).

> [!NOTE]
> Some types of logging buffers write to the log file, which can cause events to appear in the incorrect order in the stream. For example, an application log entry that occurs when a user visits a page might be displayed in the stream before the corresponding HTTP log entry for the page request.

### Azure portal

To stream logs in the [Azure portal](https://portal.azure.com), go to your app and select **Monitoring** > **Log stream**.

### <a name = "in-cloud-shell"></a> Cloud Shell

To stream logs live in [Azure Cloud Shell](../cloud-shell/overview.md), use the following command.

> [!IMPORTANT]
> This command might not work with web apps hosted in a Linux-based App Service plan.

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

To filter specific log types, such as HTTP, use the `--provider` parameter. For example:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --provider http
```

### Local terminal

To stream logs in the local console, [install the Azure CLI](/cli/azure/install-azure-cli) and [sign in to your account](/cli/azure/authenticate-azure-cli). After you're signed in, follow the [instructions for Cloud Shell](#in-cloud-shell).

## Access log files

If you configure the Azure Storage blobs option for a log type, you need a client tool that works with Azure Storage. For more information, see [Microsoft client tools for working with Azure Storage](../storage/common/storage-explorers.md).

For logs stored in the App Service file system, access them by using the Kudu engine.

1. Open your app in the Azure portal and select **Development Tools** > **Advanced Tools**, then select **Go**.
1. In Kudu, select **Tools** > **Diagnostic dump**.

For Linux or custom containers, the ZIP file contains console output logs for both the Docker host and the Docker container. For a scaled-out app, the ZIP file contains one set of logs for each instance. In the App Service file system, these log files are the contents of the `/home/LogFiles` directory. Deployment logs are stored in `/site/deployments/`.

For Windows apps, the ZIP file contains the contents of the `D:\Home\LogFiles` directory in the App Service file system. It has the following structure:

| Log type | Directory | Description |
|:-|:-|:-|
| Application log |`/LogFiles/Application/` | Contains one or more text files. The format of the log messages depends on the logging provider that you use. |
| Failed request trace | `/LogFiles/W3SVC#########/` | Contains XML files and an XSL file. You can view the formatted XML files in the browser. |
| Detailed error log | `/LogFiles/DetailedErrors/` | Contains HTM error files. You can view the HTM files in the browser.<br/><br/>Another way to view the failed request traces is to go to your app page in the portal. On the left menu, select **Diagnose and solve problems**. Search for **Failed Request Tracing Logs**, and then select the icon to browse and view the trace that you want. |
| Web server log | `/LogFiles/http/RawLogs/` | Contains text files formatted by using the [W3C extended log file format](/windows/desktop/Http/w3c-logging). You can read these files by using a text editor or a tool like [Log Parser](https://www.iis.net/downloads/community/2010/04/log-parser-22).<br/><br/>App Service doesn't support the `s-computername`, `s-ip`, and `cs-version` fields. |
| Deployment log | `/LogFiles/Git/` and `/deployments/` | Contains logs generated by the internal deployment processes, along with logs for Git deployments. |

## Send logs to Azure Monitor

With [Azure Monitor integration](https://aka.ms/appsvcblog-azmon), you can [create diagnostic settings](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) to send logs to storage accounts, event hubs, and Log Analytics workspaces. When you add a diagnostic setting, App Service adds app settings to your app, which triggers an app restart.

:::image type="content" source="media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png" alt-text="Screenshot that shows selections for displaying diagnostic settings and adding a diagnostic setting." lightbox="media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png":::

### Supported log types

For a list of supported log types and their descriptions, see [Supported resource logs for Microsoft.Web](monitor-app-service-reference.md#supported-resource-logs-for-microsoftweb).

## <a name = "networking-considerations"></a> Network considerations

For information about restrictions for diagnostic settings, see [Destination limits](/azure/azure-monitor/essentials/diagnostic-settings#destination-limitations).

## <a name="nextsteps"></a> Related content

- [Log queries in Azure Monitor](/azure/azure-monitor/logs/log-query-overview)
- [Azure App Service quotas and alerts](web-sites-monitor.md)
- [Troubleshoot an app in Azure App Service by using Visual Studio](troubleshoot-dotnet-visual-studio.md)
- [Tutorial: Run a load test to identify performance bottlenecks in a web app](../load-testing/tutorial-identify-bottlenecks-azure-portal.md)
