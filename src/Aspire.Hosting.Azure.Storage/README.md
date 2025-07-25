# Aspire.Hosting.Azure.Storage library

Provides extension methods and resource definitions for a .NET Aspire AppHost to configure Azure Storage.

## Getting started

### Prerequisites

- Azure subscription - [create one for free](https://azure.microsoft.com/free/)

### Install the package

Install the .NET Aspire Azure Storage Hosting library with [NuGet](https://www.nuget.org):

```dotnetcli
dotnet add package Aspire.Hosting.Azure.Storage
```

## Configure Azure Provisioning for local development

Adding Azure resources to the .NET Aspire application model will automatically enable development-time provisioning
for Azure resources so that you don't need to configure them manually. Provisioning requires a number of settings
to be available via .NET configuration. Set these values in user secrets in order to allow resources to be configured
automatically.

```json
{
    "Azure": {
      "SubscriptionId": "<your subscription id>",
      "ResourceGroupPrefix": "<prefix for the resource group>",
      "Location": "<azure location>"
    }
}
```

> NOTE: Developers must have Owner access to the target subscription so that role assignments
> can be configured for the provisioned resources.

## Usage example

In the _AppHost.cs_ file of `AppHost`, add a Blob (can use tables or queues also) Storage connection and consume the connection using the following methods:

```csharp
var blobs = builder.AddAzureStorage("storage").AddBlobService("blobs");

var myService = builder.AddProject<Projects.MyService>()
                       .WithReference(blobs);
```

The `WithReference` method passes that connection information into a connection string named `blobs` in the `MyService` project. In the _Program.cs_ file of `MyService`, the connection can be consumed using the client library [Aspire.Azure.Storage.Blobs](https://www.nuget.org/packages/Aspire.Azure.Storage.Blobs):

```csharp
builder.AddAzureBlobServiceClient("blobs");
```

## Creating and using blob containers and queues directly

You can create and use blob containers and queues directly by adding them to your storage resource. This allows you to provision and reference specific containers or queues for your services.

### Adding a blob container

```csharp
var storage = builder.AddAzureStorage("storage");
var container = storage.AddBlobContainer("my-container");
```

You can then pass the container reference to a project:

```csharp
builder.AddProject<Projects.MyService>()
       .WithReference(container);
```

In your service, consume the container using:

```csharp
builder.AddAzureBlobContainerClient("my-container");
```

This will register a singleton of type `BlobContainerClient`.

### Adding a queue

```csharp
var storage = builder.AddAzureStorage("storage");
var queue = storage.AddQueue("my-queue");
```

Pass the queue reference to a project:

```csharp
builder.AddProject<Projects.MyService>()
       .WithReference(queue);
```

In your service, consume the queue using:

```csharp
builder.AddAzureQueue("my-queue");
```

This will register a singleton of type `QueueClient`.

This approach allows you to define and use specific blob containers and queues as first-class resources in your Aspire application model.

## Additional documentation

* https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/storage/Azure.Storage.Blobs/README.md
* https://github.com/dotnet/aspire/tree/main/src/Components/README.md

## Feedback & contributing

https://github.com/dotnet/aspire
