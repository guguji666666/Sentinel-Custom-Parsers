---
title: Create a Python function using Visual Studio Code - Azure Functions
description: Learn how to create a Python function, then publish the local project to serverless hosting in Azure Functions using the Azure Functions extension in Visual Studio Code.
ms.topic: quickstart
ms.date: 07/04/2025
ms.devlang: python
ms.custom: devx-track-python, mode-api, devdivchpfy22, vscode-azure-extension-update-complete, ai-video-demo, copilot-scenario-highlight
ai-usage: ai-assisted
---

# Quickstart: Create a function in Azure with Python using Visual Studio Code

In this article, you use Visual Studio Code to create a Python function that responds to HTTP requests. After testing the code locally, you deploy it to the serverless environment of Azure Functions.

This article uses the Python v2 programming model for Azure Functions, which provides a decorator-based approach for creating functions. To learn more about the Python v2 programming model, see the [Developer Reference Guide](functions-reference-python.md?pivots=python-mode-decorators)

Completing this quickstart incurs a small cost of a few USD cents or less in your Azure account.

There's also a [CLI-based version](how-to-create-function-azure-cli.md?pivots=programming-language-python) of this article.

This video shows you how to create a Python function in Azure using Visual Studio Code.
> [!VIDEO a1e10f96-2940-489c-bc53-da2b915c8fc2]

The steps in the video are also described in the following sections.

## Configure your environment

Before you begin, make sure that you have the following requirements in place:

+ An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ A Python version that is [supported by Azure Functions](supported-languages.md?pivot=programming-language-python#languages-by-runtime-version). For more information, see [How to install Python](https://wiki.python.org/moin/BeginnersGuide/Download).

+ [Visual Studio Code](https://code.visualstudio.com/) on one of the [supported platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ The [Python extension](https://marketplace.visualstudio.com/items?itemName=ms-python.python) for Visual Studio Code.

+ The [Azure Functions extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) for Visual Studio Code, version 1.8.1 or later.

+ The [Azurite V3 extension](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) local storage emulator. While you can also use an actual Azure storage account, this article assumes you're using the Azurite emulator.

[!INCLUDE [functions-install-core-tools-vs-code](../../includes/functions-install-core-tools-vs-code.md)]

## <a name="create-an-azure-functions-project"></a>Create your local project

In this section, you use Visual Studio Code to create a local Azure Functions project in Python. Later in this article, you publish your function code to Azure.

1. In Visual Studio Code, press <kbd>F1</kbd> to open the command palette and search for and run the command `Azure Functions: Create New Project...`.

1. Choose the directory location for your project workspace and choose **Select**. You should either create a new folder or choose an empty folder for the project workspace. Don't choose a project folder that is already part of a workspace.
 
1. Provide the following information at the prompts:

    |Prompt|Selection|
    |--|--|
    |**Select a language**| Choose `Python (Programming Model V2)`.|
    |**Select a Python interpreter to create a virtual environment**| Choose your preferred Python interpreter. If an option isn't shown, type in the full path to your Python binary.|
    |**Select a template for your project's first function** | Choose `HTTP trigger`. |
    |**Name of the function you want to create**| Enter `HttpExample`.|
    |**Authorization level**| Choose `ANONYMOUS`, which lets anyone call your function endpoint. For more information, see [Authorization level](functions-bindings-http-webhook-trigger.md#http-auth).|
    |**Select how you would like to open your project** | Choose `Open in current window`.|

1. Visual Studio Code uses the provided information and generates an Azure Functions project with an HTTP trigger. You can view the local project files in the Explorer. The generated `function_app.py` project file contains your functions.

1. In the local.settings.json file, update the `AzureWebJobsStorage` setting as in the following example:

    ```json
    "AzureWebJobsStorage": "UseDevelopmentStorage=true",
    ```
    
    This tells the local Functions host to use the storage emulator for the storage connection required by the Python v2 model. When you publish your project to Azure, this setting uses the default storage account instead. If you're using an Azure Storage account during local development, set your storage account connection string here.

## Start the emulator

1. In Visual Studio Code, press <kbd>F1</kbd> to open the command palette. In the command palette, search for and select `Azurite: Start`.

1. Check the bottom bar and verify that Azurite emulation services are running. If so, you can now run your function locally.  

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

After you verify that the function runs correctly on your local computer, it's time to use Visual Studio Code to publish the project directly to Azure.

## Use AI to normalize and validate input

You can use AI tools, such as GitHub Copilot in Visual Studio Code, to update template-generated function code. This is an example prompt for Copilot Chat that updates the existing Python function to retrieve parameters from either the query string or JSON body, apply formatting or type conversions, and return them as JSON in the response: 

```copilot-prompt
#file:function_app.py Modify the function to accept name, email, and age from either the query parameters or the JSON body of the request, whichever is available. Return all three parameters in the JSON response, applying these rules:
Title-case the name
Lowercase the email
Convert age to an integer, otherwise return "not provided"
Use sensible defaults if any parameter is missing
```

You can customize your prompt to add specifics as needed. GitHub Copilot is powered by AI, so surprises and mistakes are possible. For more information, see [Copilot FAQs](https://aka.ms/copilot-general-use-faqs).  

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## Next steps

You created and deployed a function app with a simple HTTP-triggered function. In the next articles, you expand that function by connecting to a storage service in Azure. To learn more about connecting to other Azure services, see [Add bindings to an existing function in Azure Functions](add-bindings-existing-function.md?tabs=python).

> [!div class="nextstepaction"]
> [Connect to Azure Cosmos DB](functions-add-output-binding-cosmos-db-vs-code.md?pivots=programming-language-python)
> [!div class="nextstepaction"]
> [Connect to an Azure Storage queue](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-python)

[Having issues? Let us know.](https://aka.ms/python-functions-qs-survey)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions

## Related content
- [GitHub Copilot in Visual Studio Code](https://code.visualstudio.com/docs/copilot/overview)
- [GitHub Copilot in Visual Studio](/visualstudio/ide/visual-studio-github-copilot-install-and-states)
