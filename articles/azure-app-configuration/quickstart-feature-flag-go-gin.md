---
title: Quickstart for adding feature flags to Go Gin web apps
titleSuffix: Azure App Configuration
description: Learn to implement feature flags in your Go Gin web application using feature management and Azure App Configuration. Dynamically manage feature rollouts and control feature visibility without redeploying the app.
services: azure-app-configuration
author: linglingye
ms.service: azure-app-configuration
ms.devlang: golang
ms.custom: devx-track-go, mode-other
ms.topic: quickstart
ms.date: 07/03/2025
ms.author: linglingye
#Customer intent: As a Go developer, I want to use feature flags to control feature availability in my Gin web application quickly and confidently.
---

# Quickstart: Add feature flags to a Go Gin web app

In this quickstart, you'll create a feature flag in Azure App Configuration and use it to dynamically control the availability of a new web page in a Go Gin web app without restarting or redeploying it.

The feature management support extends the dynamic configuration feature in App Configuration. This example demonstrates how to integrate feature flags into a Go Gin web application with real-time updates and conditional page rendering.

## Prerequisites

- An Azure account with an active subscription. [Create one for free](https://azure.microsoft.com/free/).
- An App Configuration store. [Create a store](./quickstart-azure-app-configuration-create.md#create-an-app-configuration-store).
- Go 1.21 or later. For information on installing Go, see the [Go downloads page](https://golang.org/dl/).
- [Azure App Configuration Go provider](https://pkg.go.dev/github.com/Azure/AppConfiguration-GoProvider/azureappconfiguration) v1.1.0-beta.1 or later.

## Create a feature flag

Add a feature flag called *Beta* to the App Configuration store and leave **Label** and **Description** with their default values. For more information about how to add feature flags to a store using the Azure portal or the CLI, go to [Create a feature flag](./manage-feature-flags.md#create-a-feature-flag).

:::image type="content" source="./media/add-beta-feature-flag.png" alt-text="Screenshot of creating a feature flag.":::

## Create a Go web application

1. Create a new directory for your Go project and navigate into it:

    ```console
    mkdir gin-feature-flag-quickstart
    cd gin-feature-flag-quickstart
    ```

1. Initialize a new Go module:

    ```console
    go mod init gin-feature-flag-quickstart
    ```

1. Install the required Go packages for Azure App Configuration, Gin web framework, and feature management:

    ```console
    go get github.com/gin-gonic/gin
    go get github.com/microsoft/Featuremanagement-Go/featuremanagement
    go get github.com/microsoft/Featuremanagement-Go/featuremanagement/providers/azappconfig
    ```

1. Create a templates directory for your HTML templates.

    ```bash
    mkdir templates
    ```

1. Create an HTML template for the home page. Add the following content to `templates/index.html`:
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>{{.title}}</title>
        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    </head>
    <body>
        <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
            <div class="container">
                <a class="navbar-brand" href="/">TestAppConfig</a>
                <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target=".navbar-collapse" aria-controls="navbarSupportedContent"
                        aria-expanded="false" aria-label="Toggle navigation">
                    <span class="navbar-toggler-icon"></span>
                </button>
                <div class="navbar-collapse collapse d-sm-inline-flex justify-content-between">
                    <ul class="navbar-nav flex-grow-1">
                        <li class="nav-item">
                            <a class="nav-link text-dark" href="/">Home</a>
                        </li>
                        {{if .betaEnabled}}
                        <li class="nav-item">
                            <a class="nav-link text-dark" href="/beta">Beta</a>
                        </li>
                        {{end}}
                    </ul>
                </div>
            </div>
        </nav>    <div class="container">
            <div class="row justify-content-center">
                <div class="col-md-8 text-center">
                    <h1>Hello from Azure App Configuration</h1>
            </div>
        </div>

        <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
    </body>
    </html>
    ```

1. Create an HTML template for the beta page. Add the following content to `templates/beta.html`:
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>{{.title}}</title>
        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    </head>
    <body>
        <!-- Navigation -->    <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
            <div class="container">
                <a class="navbar-brand" href="/">TestAppConfig</a>
                <div class="navbar-collapse collapse d-sm-inline-flex justify-content-between">
                    <ul class="navbar-nav flex-grow-1">
                        <li class="nav-item">
                            <a class="nav-link text-dark" href="/">Home</a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link text-dark" href="/beta">Beta</a>
                        </li>
                    </ul>
                </div>
            </div>
        </nav>    <div class="container">
            <div class="row justify-content-center">
                <div class="col-md-8 text-center">
                    <h1>This is the beta website.</h1>
                </div>
            </div>
        </div>

        <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
    </body>
    </html>
    ```

## Use a feature flag

1. Update the `options` to enable feature flags in [previous step](./quickstart-go-console-app.md#connect-to-app-configuration).

    ```golang
        options := &azureappconfiguration.Options{
            FeatureFlagOptions: azureappconfiguration.FeatureFlagOptions{
                Enabled: true,
                RefreshOptions: azureappconfiguration.RefreshOptions{
                    Enabled: true,
                },
            },
        }
    ```

    > [!TIP]
    > When no selector is specified in `FeatureFlagOptions`, it loads *all* feature flags with *no label* in your App Configuration store. The default refresh interval of feature flags is 30 seconds. You can customize this behavior via the `RefreshOptions` parameter. For example, the following code snippet loads only feature flags that start with *TestApp:* in their *key name* and have the label *dev*. The code also changes the refresh interval time to 5 minutes. Note that this refresh interval time is separate from that for regular key-values.
    >
    > ```golang
    > FeatureFlagOptions{
    >     Enabled: true,
    >     Selectors: []azureappconfiguration.Selector{
    >         {
    >             KeyFilter:   "TestApp:*",
    >             LabelFilter: "dev",
    >         },
    >     },
    >     RefreshOptions: azureappconfiguration.RefreshOptions{
    >         Enabled: true,
    >         Interval: 5 * time.Minute,
    >     },
    > }
    > ```

1. Create a file named `main.go` with the following content:

    ```golang
    package main

    import (
        "context"
        "fmt"
        "log"
        "net/http"
        "os"

        "github.com/Azure/AppConfiguration-GoProvider/azureappconfiguration"
        "github.com/gin-gonic/gin"
        "github.com/microsoft/Featuremanagement-Go/featuremanagement"
        "github.com/microsoft/Featuremanagement-Go/featuremanagement/providers/azappconfig"
    )

    type WebApp struct {
        featureManager *featuremanagement.FeatureManager
        appConfig      *azureappconfiguration.AzureAppConfiguration
    }

    func (app *WebApp) refreshMiddleware() gin.HandlerFunc {
        return func(c *gin.Context) {
            go func() {
                ctx := context.Background()
                if err := app.appConfig.Refresh(ctx); err != nil {
                    log.Printf("Error refreshing configuration: %v", err)
                }
            }()
            c.Next()
        }
    }

    func (app *WebApp) featureMiddleware() gin.HandlerFunc {
        return func(c *gin.Context) {
            // Check if Beta feature is enabled
            betaEnabled, err := app.featureManager.IsEnabled("Beta")
            if err != nil {
                log.Printf("Error checking Beta feature: %v", err)
                betaEnabled = false
            }

            // Store feature flag status for use in templates
            c.Set("betaEnabled", betaEnabled)
            c.Next()
        }
    }

    func (app *WebApp) setupRoutes(r *gin.Engine) {
        r.Use(app.refreshMiddleware())
        r.Use(app.featureMiddleware())

        // Load HTML templates
        r.LoadHTMLGlob("templates/*.html")

        // Routes
        r.GET("/", app.homeHandler)
        r.GET("/beta", app.betaHandler)
    }

    // Home page handler
    func (app *WebApp) homeHandler(c *gin.Context) {
        betaEnabled := c.GetBool("betaEnabled")

        c.HTML(http.StatusOK, "index.html", gin.H{
            "title":       "Feature Management Example App",
            "betaEnabled": betaEnabled,
        })
    }

    // Beta page handler
    func (app *WebApp) betaHandler(c *gin.Context) {
        betaEnabled := c.GetBool("betaEnabled")

        if !betaEnabled {
            return
        }

        c.HTML(http.StatusOK, "beta.html", gin.H{
            "title": "Beta Page",
        })
    }

    func main() {
        ctx := context.Background()

        // Load Azure App Configuration
        appConfig, err := loadAzureAppConfiguration(ctx)
        if err != nil {
            log.Fatalf("Error loading Azure App Configuration: %v", err)
        }

        // Create feature flag provider
        featureFlagProvider, err := azappconfig.NewFeatureFlagProvider(appConfig)
        if err != nil {
            log.Fatalf("Error creating feature flag provider: %v", err)
        }

        // Create feature manager
        featureManager, err := featuremanagement.NewFeatureManager(featureFlagProvider, nil)
        if err != nil {
            log.Fatalf("Error creating feature manager: %v", err)
        }

        // Create web app
        app := &WebApp{
            featureManager: featureManager,
            appConfig:      appConfig,
        }

        // Set up Gin with default middleware (Logger and Recovery)
        r := gin.Default()

        // Set up routes
        app.setupRoutes(r)

        // Start server
        fmt.Println("Starting server on http://localhost:8080")
        fmt.Println("Open http://localhost:8080 in your browser")
        fmt.Println("Toggle the 'Beta' feature flag in Azure portal to see changes")
        fmt.Println()

        if err := r.Run(":8080"); err != nil {
            log.Fatalf("Failed to start server: %v", err)
        }
    }
    ```

## Run the web application

1. [Set the environment variable for authentication](./quickstart-go-web-app.md#run-the-web-application) and run the application.

    ```console
    go mod tidy
    go run .
    ```

1. Open a browser window, and go to `http://localhost:8080`. Your browser should display a page similar to the image below.

    :::image type="content" source="./media/quickstarts/gin-app-feature-flag-before.png" alt-text="Screenshot of gin web app before enabling feature flag.":::

1. Sign in to the [Azure portal](https://portal.azure.com). Select **All resources**, and select the App Configuration store that you created previously.

1. Select **Feature manager** and locate the *Beta* feature flag. Enable the flag by selecting the checkbox under **Enabled**.

1. Refresh the browser a few times. When the refresh interval time window passes, the page displays with updated content:

    :::image type="content" source="./media/quickstarts/gin-app-feature-flag-after.png" alt-text="Screenshot of gin web app after enabling feature flag.":::

1. Notice that the **Beta** menu item now appears in the navigation bar. Click on it to access the beta page:

    :::image type="content" source="./media/quickstarts/gin-app-feature-flag-beta-page.png" alt-text="Screenshot of gin web app beta page.":::


## Clean up resources

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## Next steps

In this quickstart, you created a feature flag in Azure App Configuration and used it in a Go Gin web application. The [Feature Management Go library](https://github.com/microsoft/FeatureManagement-Go) provides feature flag capabilities that integrate seamlessly with Azure App Configuration.For more features, continue to the following document.

> [!div class="nextstepaction"]
> [Go Feature Management reference](https://pkg.go.dev/github.com/microsoft/Featuremanagement-Go/featuremanagement)

While a feature flag allows you to activate or deactivate functionality in your app, you may want to customize a feature flag based on your app's logic. Feature filters allow you to enable a feature flag conditionally. For more information, continue to the following tutorial.

> [!div class="nextstepaction"]
> [Enable conditional features with feature filters](./howto-feature-filters.md)

Azure App Configuration offers built-in feature filters that enable you to activate a feature flag only during a specific period or to a particular targeted audience of your app. For more information, continue to the following tutorial.

> [!div class="nextstepaction"]
> [Enable features on a schedule](./howto-timewindow-filter.md)

> [!div class="nextstepaction"]
> [Roll out features to targeted audiences](./howto-targetingfilter.md)
