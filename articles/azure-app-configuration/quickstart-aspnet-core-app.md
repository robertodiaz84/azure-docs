---
title: Quickstart for Azure App Configuration with ASP.NET Core | Microsoft Docs
description: A quickstart for using Azure App Configuration with ASP.NET Core apps
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''

ms.assetid: 
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu

#Customer intent: As an ASP.NET Core developer, I want to manage all my app settings in one place.
---
# Quickstart: Create an ASP.NET Core app with Azure App Configuration

Azure App Configuration is a managed configuration service in Azure. It lets you easily store and manage all your application settings in one place that is separated from your code. This quickstart shows you how to incorporate the service into an ASP.NET Core web app. ASP.NET Core builds a single key-value based configuration object using settings from one or more data sources, known as *configuration providers*, specified by an application. Because App Configuration's .NET Core client is implemented as such a provider, the service appears just like another data source.

You can use any code editor to complete the steps in this quickstart. However, [Visual Studio Code](https://code.visualstudio.com/) is an excellent option available on the Windows, macOS, and Linux platforms.

## Prerequisites

To complete this quickstart, install the [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Create an app configuration store

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## Create an ASP.NET Core web app

You will use the [.NET Core command-line interface (CLI)](https://docs.microsoft.com/dotnet/core/tools/) to create a new ASP.NET Core MVC Web App project. The advantage of using the .NET Core CLI over Visual Studio is that it is available across the Windows, macOS, and Linux platforms.

1. Create a new folder for your project. For this quickstart, we will name it *TestAppConfig*.

2. In the new folder, execute the following command to create a new ASP.NET Core MVC Web App project:

        dotnet new mvc

## Add Secret Manager

You will add the [Secret Manager tool](https://docs.microsoft.com/aspnet/core/security/app-secrets) to your project. The Secret Manager tool stores sensitive data for development work outside of your project tree. This approach helps prevent the accidental sharing of app secrets within source code.

- Open your *.csproj* file. Add a `UserSecretsId` element as shown below and replace its value with your own, which typically is a GUID. Save the file.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
        <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
    </ItemGroup>

    </Project>
    ```

## Connect to app configuration store

1. Add a reference to the `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet package by executing the following command:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. Execute the following command to restore packages for your project.

        dotnet restore

3. Add a secret named *ConnectionStrings:AppConfig* to Secret Manager.

    This secret will contain the connection string to access your app configuration store. Replace the value in the command below with the connection string for your app configuration store.

    This command must be executed in the same directory as the *.csproj* file.

        dotnet user-secrets set ConnectionStrings:AppConfig "Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>"

    Secret Manager will only be used for testing the web app locally. When the app is deployed (for example, to [Azure App Service](https://azure.microsoft.com/services/app-service/web)), you will use an application setting (for example, **Connection Strings** in App Service) instead of storing the connection string with Secret Manager.

    This secret is a accessed with the configuration API. A colon (:) works in the configuration name with the configuration API on all supported platforms, see [Configuration by environment](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Open *Program.cs* and update the `CreateWebHostBuilder` method to use App Configuration by calling the `config.AddAzureAppConfiguration()` method.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

5. Open *Index.cshtml* in the *Views* > *Home* directory and replace its content with the following code:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@Configuration["TestApp:Settings:Message"]</h1>
    </body>
    </html>
    ```

6. Open *_Layout.cshtml* in the *Views* > *Shared* directory and replace its content with the following code:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## Build and run the app locally

1. To build the app using the .NET Core CLI, execute the following command in the command shell:

        dotnet build

2. Once the build successfully completes, execute the following command to run the web app locally:

        dotnet run

3. Launch a browser window and navigate to `http://localhost:5000`, which is the default URL for the web app hosted locally.

    ![Quickstart app launch local](./media/quickstarts/aspnet-core-app-launch-local.png)

## Clean up resources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## Next steps

In this quickstart, you've created a new app configuration store and used it with an ASP.NET Core web app. To learn more about using App Configuration, continue to the next tutorial that demonstrates authentication.

> [!div class="nextstepaction"]
> [Managed Identities for Azure Resources Integration](./integrate-azure-managed-service-identity.md)
