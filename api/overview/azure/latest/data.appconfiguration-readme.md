---
title: Azure App Configuration client library for .NET
keywords: Azure, dotnet, SDK, API, Azure.Data.AppConfiguration, appconfiguration
ms.date: 09/12/2023
ms.topic: reference
ms.devlang: dotnet
ms.service: appconfiguration
---
# Azure App Configuration client library for .NET - version 1.2.1 


Azure App Configuration is a managed service that helps developers centralize their application and feature settings simply and securely.

Use the client library for App Configuration to:

* [Create and manage centrally stored application configuration settings][azconfig_setting_concepts]

[Source code][source_root] | [Package (NuGet)][package] | [API reference documentation][reference_docs] | [Product documentation][azconfig_docs] | [Samples][source_samples]

## Getting started

### Install the package

Install the Azure App Configuration client library for .NET with [NuGet][nuget]:

```dotnetcli
dotnet add package Azure.Data.AppConfiguration
```

### Prerequisites

* An [Azure subscription][azure_sub].
* An existing [Configuration Store][configuration_store].

If you need to create a Configuration Store, you can use the Azure Portal or [Azure CLI][azure_cli].

You can use the Azure CLI to create the Configuration Store with the following command:

```PowerShell
az appconfig create --name <config-store-name> --resource-group <resource-group-name> --location eastus
```

### Authenticate the client

In order to interact with the App Configuration service, you'll need to create an instance of the [Configuration Client][configuration_client_class] class. To make this possible, you'll need the connection string of the Configuration Store.

#### Get credentials

Use the [Azure CLI][azure_cli] snippet below to get the connection string from the Configuration Store.

```PowerShell
az appconfig credential list --name <config-store-name>
```

Alternatively, get the connection string from the Azure Portal.

#### Create ConfigurationClient

Once you have the value of the connection string, you can create the ConfigurationClient:

```C# Snippet:CreateConfigurationClient
string connectionString = "<connection_string>";
var client = new ConfigurationClient(connectionString);
```

#### Create ConfigurationClient with Azure Active Directory Credential

Client subscription key authentication is used in most of the examples in this getting started guide, but you can also authenticate with Azure Active Directory using the [Azure Identity library][azure_identity]. To use the [DefaultAzureCredential][azure_identity_dac] provider shown below,
or other credential providers provided with the Azure SDK, please install the Azure.Identity package:

```dotnetcli
dotnet add package Azure.Identity
```

You will also need to [register a new AAD application][aad_register_app] and [grant access][aad_grant_access] to Configuration Store by assigning the `"App Configuration Data Reader"` or `"App Configuration Data Owner"` role to your service principal.

Set the values of the client ID, tenant ID, and client secret of the AAD application as environment variables: AZURE_CLIENT_ID, AZURE_TENANT_ID, AZURE_CLIENT_SECRET.

```C# Snippet:CreateConfigurationClientTokenCredential
string endpoint = "<endpoint>";
var client = new ConfigurationClient(new Uri(endpoint), new DefaultAzureCredential());
```

## Key concepts

### Configuration Setting

A Configuration Setting is the fundamental resource within a Configuration Store. In its simplest form, it is a key and a value. However, there are additional properties such as the modifiable content type and tags fields that allow the value to be interpreted or associated in different ways.

The [Label][label_concept] property of a Configuration Setting provides a way to separate Configuration Settings into different dimensions. These dimensions are user defined and can take any form. Some common examples of dimensions to use for a label include regions, semantic versions, or environments. Many applications have a required set of configuration keys that have varying values as the application exists across different dimensions.

For example, MaxRequests may be 100 in "NorthAmerica" and 200 in "WestEurope". By creating a Configuration Setting named MaxRequests with a label of "NorthAmerica" and another, only with a different value, with a "WestEurope" label, an application can seamlessly retrieve Configuration Settings as it runs in these two dimensions.

### Thread safety

We guarantee that all client instance methods are thread-safe and independent of each other ([guideline](https://azure.github.io/azure-sdk/dotnet_introduction.html#dotnet-service-methods-thread-safety)). This ensures that the recommendation of reusing client instances is always safe, even across threads.

### Additional concepts
<!-- CLIENT COMMON BAR -->
[Client options](https://github.com/Azure/azure-sdk-for-net/blob/Azure.Data.AppConfiguration_1.2.1/sdk/core/Azure.Core/README.md#configuring-service-clients-using-clientoptions) |
[Accessing the response](https://github.com/Azure/azure-sdk-for-net/blob/Azure.Data.AppConfiguration_1.2.1/sdk/core/Azure.Core/README.md#accessing-http-response-details-using-responset) |
[Long-running operations](https://github.com/Azure/azure-sdk-for-net/blob/Azure.Data.AppConfiguration_1.2.1/sdk/core/Azure.Core/README.md#consuming-long-running-operations-using-operationt) |
[Handling failures](https://github.com/Azure/azure-sdk-for-net/blob/Azure.Data.AppConfiguration_1.2.1/sdk/core/Azure.Core/README.md#reporting-errors-requestfailedexception) |
[Diagnostics](https://github.com/Azure/azure-sdk-for-net/blob/Azure.Data.AppConfiguration_1.2.1/sdk/core/Azure.Core/samples/Diagnostics.md) |
[Mocking](https://learn.microsoft.com/dotnet/azure/sdk/unit-testing-mocking) |
[Client lifetime](https://devblogs.microsoft.com/azure-sdk/lifetime-management-and-thread-safety-guarantees-of-azure-sdk-net-clients/)
<!-- CLIENT COMMON BAR -->

## Examples

The following sections provide several code snippets covering some of the most common Configuration Service tasks. Note that there are sync and async methods available for both.

* [Create a Configuration Setting](#create-a-configuration-setting)
* [Retrieve a Configuration Setting](#retrieve-a-configuration-setting)
* [Update an existing Configuration Setting](#update-an-existing-configuration-setting)
* [Delete a Configuration Setting](#delete-a-configuration-setting)

### Create a Configuration Setting

Create a Configuration Setting to be stored in the Configuration Store. There are two ways to store a Configuration Setting:

* AddConfigurationSetting creates a setting only if the setting does not already exist in the store.
* SetConfigurationSetting creates a setting if it doesn't exist or overrides an existing setting.

```C# Snippet:CreateConfigurationSetting
string connectionString = "<connection_string>";
var client = new ConfigurationClient(connectionString);
var settingToCreate = new ConfigurationSetting("some_key", "some_value");
ConfigurationSetting setting = client.SetConfigurationSetting(settingToCreate);
```

### Retrieve a Configuration Setting

Retrieve a previously stored Configuration Setting by calling GetConfigurationSetting.  This snippet assumes the setting "some_key" exists in the configuration store.

```C# Snippet:GetConfigurationSetting
string connectionString = "<connection_string>";
var client = new ConfigurationClient(connectionString);
ConfigurationSetting setting = client.GetConfigurationSetting("some_key");
```

### Update an existing Configuration Setting

Update an existing Configuration Setting by calling SetConfigurationSetting.  This snippet assumes the setting "some_key" exists in the configuration store.

```C# Snippet:UpdateConfigurationSetting
string connectionString = "<connection_string>";
var client = new ConfigurationClient(connectionString);
ConfigurationSetting setting = client.SetConfigurationSetting("some_key", "new_value");
```

### Delete a Configuration Setting

Delete an existing Configuration Setting by calling DeleteConfigurationSetting.  This snippet assumes the setting "some_key" exists in the configuration store.

```C# Snippet:DeleteConfigurationSetting
string connectionString = "<connection_string>";
var client = new ConfigurationClient(connectionString);
client.DeleteConfigurationSetting("some_key");
```

## Troubleshooting

See our [troubleshooting guide](https://github.com/Azure/azure-sdk-for-net/tree/Azure.Data.AppConfiguration_1.2.1/sdk/appconfiguration/Azure.Data.AppConfiguration/TROUBLESHOOTING.md) for details on how to diagnose various failure scenarios.

## Next steps

### More sample code

Several App Configuration client library samples are available to you in this GitHub repository.  These include:

* [Hello world](https://github.com/Azure/azure-sdk-for-net/blob/Azure.Data.AppConfiguration_1.2.1/sdk/appconfiguration/Azure.Data.AppConfiguration/samples/Sample1_HelloWorld.md): Create and delete a configuration setting.
* [Hello world async with labels](https://github.com/Azure/azure-sdk-for-net/blob/Azure.Data.AppConfiguration_1.2.1/sdk/appconfiguration/Azure.Data.AppConfiguration/samples/Sample2_HelloWorldExtended.md): Asynchronously create, update and delete configuration settings with labels.
* [Make a configuration setting readonly](https://github.com/Azure/azure-sdk-for-net/blob/Azure.Data.AppConfiguration_1.2.1/sdk/appconfiguration/Azure.Data.AppConfiguration/samples/Sample3_SetClearReadOnly.md): Make a configuration setting read-only, and then return it to a read-write state.
* [Read revision history](https://github.com/Azure/azure-sdk-for-net/blob/Azure.Data.AppConfiguration_1.2.1/sdk/appconfiguration/Azure.Data.AppConfiguration/samples/Sample4_ReadRevisionHistory.md): Read the revision history of a configuration setting that has been changed.
* [Get a setting if changed](https://github.com/Azure/azure-sdk-for-net/blob/Azure.Data.AppConfiguration_1.2.1/sdk/appconfiguration/Azure.Data.AppConfiguration/samples/Sample5_GetSettingIfChanged.md): Save bandwidth by using a conditional request to retrieve a setting only if it is different from your local copy.
* [Update a setting if it hasn't changed](https://github.com/Azure/azure-sdk-for-net/blob/Azure.Data.AppConfiguration_1.2.1/sdk/appconfiguration/Azure.Data.AppConfiguration/samples/Sample6_UpdateSettingIfUnchanged.md): Prevent lost updates by using optimistic concurrency to update a setting only if your local updates were applied to the same version as the resource in the configuration store.
* [Create a mock client](https://learn.microsoft.com/dotnet/azure/sdk/unit-testing-mocking): Mock a client for testing.

 For more details see the [samples README][samples_readme].

## Contributing

See the [App Configuration CONTRIBUTING.md][azconfig_contrib] for details on building, testing, and contributing to this library.

This project welcomes contributions and suggestions. Most contributions require you to agree to a Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us the rights to use your contribution. For details, visit [cla.microsoft.com][cla].

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct][code_of_conduct]. For more information see the [Code of Conduct FAQ][code_of_conduct_faq] or contact [opencode@microsoft.com][email_opencode] with any additional questions or comments.

![Impressions](https://azure-sdk-impressions.azurewebsites.net/api/impressions/azure-sdk-for-net%2Fsdk%2Fappconfiguration%2FAzure.Data.AppConfiguration%2FREADME.png)

<!-- LINKS -->
[azconfig_docs]: /azure/azure-app-configuration/
[azconfig_contrib]: https://github.com/Azure/azure-sdk-for-net/blob/Azure.Data.AppConfiguration_1.2.1/sdk/appconfiguration/CONTRIBUTING.md
[azconfig_setting_concepts]: /azure/azure-app-configuration/concept-key-value
[aad_grant_access]: /powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0
[aad_register_app]: /azure/app-service/configure-authentication-provider-aad#-configure-with-advanced-settings
[azure_identity]: https://github.com/Azure/azure-sdk-for-net/blob/Azure.Data.AppConfiguration_1.2.1/sdk/identity/Azure.Identity
[azure_identity_dac]: https://github.com/Azure/azure-sdk-for-net/blob/Azure.Data.AppConfiguration_1.2.1/sdk/identity/Azure.Identity/README.md#defaultazurecredential
[azure_portal]: https://portal.azure.com
[source_root]: https://github.com/Azure/azure-sdk-for-net/blob/Azure.Data.AppConfiguration_1.2.1/sdk/appconfiguration/Azure.Data.AppConfiguration/src
[source_samples]: https://github.com/Azure/azure-sdk-for-net/blob/Azure.Data.AppConfiguration_1.2.1/sdk/appconfiguration/Azure.Data.AppConfiguration/samples
[reference_docs]: https://azure.github.io/azure-sdk-for-net/appconfiguration.html
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/dotnet/
[configuration_client_class]: https://github.com/Azure/azure-sdk-for-net/blob/Azure.Data.AppConfiguration_1.2.1/sdk/appconfiguration/Azure.Data.AppConfiguration/src/ConfigurationClient.cs
[configuration_store]: /azure/azure-app-configuration/quickstart-dotnet-core-app#create-an-app-configuration-store
[label_concept]: /azure/azure-app-configuration/concept-key-value#label-keys
[nuget]: https://www.nuget.org/
[package]: https://www.nuget.org/packages/Azure.Data.AppConfiguration/
[samples_readme]: https://github.com/Azure/azure-sdk-for-net/blob/Azure.Data.AppConfiguration_1.2.1/sdk/appconfiguration/Azure.Data.AppConfiguration/samples/README.md
[cla]: https://cla.microsoft.com
[code_of_conduct]: https://opensource.microsoft.com/codeofconduct/
[code_of_conduct_faq]: https://opensource.microsoft.com/codeofconduct/faq/
[email_opencode]: mailto:opencode@microsoft.com

