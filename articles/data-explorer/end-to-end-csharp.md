---
title: Teljes körű blob-betöltés az Azure Adatkezelő használatávalC#
description: Ebből a cikkből megtudhatja, hogyan végezheti el a Blobok betöltését az Azure C#Adatkezelőba egy teljes körű példával a használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 7d737319c9ddc8040a7cae6f7a9991c625cc4fcd
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809580"
---
# <a name="end-to-end-blob-ingestion-into-azure-data-explorer-using-c"></a>Teljes körű blob-betöltés az Azure Adatkezelő használatávalC#

> [!div class="op_single_selector"]
> * [C#](end-to-end-csharp.md)
> * [Python](end-to-end-python.md)
>

Az Azure Adatkezelő egy gyors és méretezhető adatelemzési szolgáltatás a napló-és telemetria. Ebből a cikkből megtudhatja, hogyan végezheti el az adatok betöltését Blob Storageból az Azure Adatkezelőba. Megtudhatja, hogyan hozhat létre programozott módon egy erőforráscsoportot, egy Storage-fiókot és egy tárolót, egy Event hubot és egy Azure Adatkezelő-fürtöt és-adatbázist. Azt is megtudhatja, hogyan állíthatja be programozott módon az Azure Adatkezelő az új Storage-fiókból származó adatok betöltésére.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="install-c-nuget"></a>A C# Nuget telepítése

* Telepítse a [Microsoft. Azure. Management. kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Telepítse a [Microsoft. Azure. Management. erőforráskezelő](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
* Telepítse a [Microsoft. Azure. Management. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/).
* Telepítse a [Microsoft. Azure. Storage. blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)programot.
* Telepítse a [Microsoft. Rest. ClientRuntime. Azure. Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) hitelesítést a hitelesítéshez.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

[!INCLUDE [data-explorer-e2e-event-grid-resource-template](../../includes/data-explorer-e2e-event-grid-resource-template.md)]

## <a name="code-example"></a>Mintakód 

Az alábbi kódrészlet egy folyamaton alapuló folyamatot eredményez, amely az Azure Adatkezelőba való adatfeldolgozást eredményez. Először létre kell hoznia egy erőforráscsoportot és egy Azure-erőforrást, például egy Storage-fiókot és egy tárolót, egy Event hub-t és egy Azure Adatkezelő-fürtöt és-adatbázist. Ezután létrehoz egy Event Grid-előfizetést és egy tábla-és oszlop-hozzárendelést az Azure Adatkezelő-adatbázisban. Végezetül hozza létre az adatkapcsolódást az Azure Adatkezelő konfigurálásához az új Storage-fiók adatainak betöltéséhez. 

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
string location = "West Europe";
string locationSmallCase = "westeurope";
string azureResourceTemplatePath = @"xxxxxxxxx\template.json";//path to the Azure Resource Manager template json from the previous section

string deploymentName = "e2eexample";
string resourceGroupName = deploymentName + "resourcegroup";
string eventHubName = deploymentName + "eventhub";
string eventHubNamespaceName = eventHubName + "ns";
string storageAccountName = deploymentName + "storage";
string storageContainerName = deploymentName + "storagecontainer";
string eventGridSubscriptionName = deploymentName + "eventgrid";
string kustoClusterName = deploymentName + "kustocluster";
string kustoDatabaseName = deploymentName + "kustodatabase";
string kustoTableName = "Events";
string kustoColumnMappingName = "Events_CSV_Mapping";
string kustoDataConnectionName = deploymentName + "kustoeventgridconnection";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);
Console.WriteLine("Step 1: Create a new resource group in your Azure subscription to manage all the resources for using Azure Data Explorer.");
resourceManagementClient.SubscriptionId = subscriptionId;
await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(resourceGroupName,
    new ResourceGroup() { Location = locationSmallCase });

Console.WriteLine(
    "Step 2: Create a Blob Storage, a container in the Storage account, an Event Hub, an Azure Data Explorer cluster, and database by using an Azure Resource Manager template.");
var parameters = $"{{\"eventHubNamespaceName\":{{\"value\":\"{eventHubNamespaceName}\"}},\"eventHubName\":{{\"value\":\"{eventHubName}\"}},\"storageAccountName\":{{\"value\":\"{storageAccountName}\"}},\"containerName\":{{\"value\":\"{storageContainerName}\"}},\"kustoClusterName\":{{\"value\":\"{kustoClusterName}\"}},\"kustoDatabaseName\":{{\"value\":\"{kustoDatabaseName}\"}}}}";
string template = File.ReadAllText(azureResourceTemplatePath, Encoding.UTF8);
await resourceManagementClient.Deployments.CreateOrUpdateAsync(resourceGroupName, deploymentName,
    new Deployment(new DeploymentProperties(DeploymentMode.Incremental, template: template,
        parameters: parameters)));

Console.WriteLine(
    "Step 3: Create an Event Grid subscription to publish blob events created in a specific container to an Event Hub.");
var eventGridClient = new EventGridManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};
string storageResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
string eventHubResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.EventHub/namespaces/{eventHubNamespaceName}/eventhubs/{eventHubName}";
await eventGridClient.EventSubscriptions.CreateOrUpdateAsync(storageResourceId, eventGridSubscriptionName,
    new EventSubscription()
    {
        Destination = new EventHubEventSubscriptionDestination(eventHubResourceId),
        Filter = new EventSubscriptionFilter()
        {
            SubjectBeginsWith = $"/blobServices/default/containers/{storageContainerName}",
            IncludedEventTypes = new List<string>(){"Microsoft.Storage.BlobCreated"}
        }
    });

Console.WriteLine("Step 4: Create a table (with three columns: EventTime, EventId, and EventSummary) and column mapping in your Azure Data Explorer database.");
var kustoUri = $"https://{kustoClusterName}.{locationSmallCase}.kusto.windows.net";
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri)
{
    InitialCatalog = kustoDatabaseName,
    FederatedSecurity = true,
    ApplicationClientId = clientId,
    ApplicationKey = clientSecret,
    Authority = tenantId
};

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            kustoTableName,
            new[]
            {
                Tuple.Create("EventTime", "System.DateTime"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("EventSummary", "System.String"),
            });

    kustoClient.ExecuteControlCommand(command);

    command = CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
        kustoTableName,
        kustoColumnMappingName,
        new[]
        {
            new CsvColumnMapping { ColumnName = "EventTime", CslDataType="dateTime", Ordinal = 0 },
            new CsvColumnMapping { ColumnName = "EventId", CslDataType="int", Ordinal = 1 },
            new CsvColumnMapping { ColumnName = "EventSummary", CslDataType="string", Ordinal = 2 },
        });
    kustoClient.ExecuteControlCommand(command);
}

Console.WriteLine("Step 5: Add an Event Grid data connection. Azure Data Explorer will automatically ingest the data when new blobs are created.");
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, kustoClusterName,
                kustoDatabaseName, dataConnectionName: kustoDataConnectionName, new EventGridDataConnection(storageResourceId, eventHubResourceId, consumerGroup: "$Default", location: location, tableName:kustoTableName, mappingRuleName: kustoColumnMappingName, dataFormat: "csv"));

```
| **Beállítás** | **Mező leírása** |
|---|---|---|
| TenantId | A bérlő azonosítója. Más néven címtár-azonosító.|
| subscriptionId | Az erőforrás-létrehozáshoz használt előfizetés-azonosító.|
| clientId | Annak az alkalmazásnak az ügyfél-azonosítója, amely hozzáférhet a bérlő erőforrásaihoz.|
| clientSecret | Az alkalmazás ügyfél-titka, amely hozzáférhet a bérlő erőforrásaihoz. |

## <a name="test-the-code-example"></a>Példa a kód tesztelésére

1. Fájl feltöltése a Storage-fiókba

    ```csharp
    string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=xxxxxxxxxxxxxx;AccountKey=xxxxxxxxxxxxxx;EndpointSuffix=core.windows.net";
    var cloudStorageAccount = CloudStorageAccount.Parse(storageConnectionString);
    CloudBlobClient blobClient = cloudStorageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(storageContainerName);
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("test.csv");
    var blobContent = @"2007-01-01 00:00:00.0000000,2592,Several trees down
    2007-01-01 00:00:00.0000000,4171,Winter Storm";
    await blockBlob.UploadTextAsync(blobContent);
    ```
    |**Beállítás** | **Mező leírása**|
    |---|---|---|
    | storageConnectionString | A programozott módon létrehozott Storage-fiókhoz tartozó kapcsolatok karakterlánca.|

2. Tesztelési lekérdezés futtatása az Azure Adatkezelő

    ```csharp
    var kustoUri = $"https://{kustoClusterName}.{locationSmallCase}.kusto.windows.net";
    var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri)
    {
        InitialCatalog = kustoDatabaseName,
        FederatedSecurity = true,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };
    using (var kustoClient = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
    {
        var query = $"{kustoTableName} | take 10";
        using (var reader = kustoClient.ExecuteQuery(query) as DataTableReader2)
        {// Print the contents of each of the result sets. 
            while (reader.Read())
            {
                Console.WriteLine($"{reader[0]}, {reader[1]}, {reader[2]}");
            }
        }
    }
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforráscsoport törléséhez és az erőforrások tisztításához használja az alábbi parancsot:

```csharp
await resourceManagementClient.ResourceGroups.DeleteAsync(resourceGroupName);
```

## <a name="next-steps"></a>Következő lépések

*  [Hozzon létre egy Azure adatkezelő-fürtöt és-adatbázist](create-cluster-database-csharp.md) , és ismerkedjen meg a fürtök és adatbázisok létrehozásának egyéb módjaival.
* Az [Azure adatkezelő](ingest-data-overview.md) a betöltési módszerekkel kapcsolatos további információért.
* Gyors útmutató [: az Azure adatkezelő lekérdezése](web-query-data.md) Webes felhasználói felület.
* [Lekérdezések írása](write-queries.md) Kusto-lekérdezési nyelvvel.