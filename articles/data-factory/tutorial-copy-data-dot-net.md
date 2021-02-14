---
title: Adatok másolása az Azure Blob Storageból a Azure SQL Databaseba
description: Ez az oktatóanyag részletes útmutatást biztosít adatok másolásához az Azure Blob Storage-ból az Azure SQL Database-be.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 11/08/2019
ms.author: jingwang
ms.openlocfilehash: b2ec4a65f1001d6d1c93a23964d59972419f651e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380882"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Adatok másolása az Azure Blobból az Azure SQL Database-be az Azure Data Factory segítségével

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ebben az oktatóanyagban olyan adat-előállító folyamatot hoz létre, amely az Azure Blob Storage-ból az Azure SQL Database-be másol adatokat. Az oktatóanyagban szereplő konfigurációs minta fájlalapú adattárból relációs adattárba való másolásra vonatkozik. A forrásként és fogadóként támogatott adattárak listáját a [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats)című részben tekintheti meg.

Ebben az oktatóanyagban az alábbi lépéseket kell elvégeznie:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure Storage-beli és Azure SQL Database-beli társított szolgáltatások létrehozása
> * Azure Blob- és Azure SQL Database-adatkészletek létrehozása
> * Másolási tevékenységet tartalmazó folyamat létrehozása
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Ez az oktatóanyag a .NET SDK-t használja. Más mechanizmusokkal is használhatja a Azure Data Factory; Tekintse át a **példákat a gyors útmutatókban.**

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* *Azure Storage-fiók*. A blobtárolót használjuk *forrás* adattárként. Ha nem rendelkezik Azure Storage-fiókkal, tekintse meg [az általános célú Storage-fiók létrehozása](../storage/common/storage-account-create.md)című témakört.
* *Azure SQL Database*. Ezt az adatbázist használjuk *fogadó* adattárként. Ha nem rendelkezik Azure SQL Database-adatbázissal, tekintse [meg az adatbázis létrehozása a Azure SQL Database-ben](../azure-sql/database/single-database-create-quickstart.md)című témakört.
* *Visual Studio*. A jelen cikkben található útmutató a Visual Studio 2019-et használja.
* *[Azure SDK a .net-hez](/dotnet/azure/dotnet-tools)*.
* *Azure Active Directory alkalmazás*. Ha nem rendelkezik Azure Active Directory alkalmazással, tekintse meg a következő témakört: a [Azure Active Directory alkalmazás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) című rész, amelyből megtudhatja, [hogyan hozhat létre Azure ad-alkalmazást a portál használatával](../active-directory/develop/howto-create-service-principal-portal.md). Másolja a következő értékeket a későbbi lépésekben való használatra: **alkalmazás (ügyfél) azonosítója**, **hitelesítési kulcs** és **könyvtár (bérlő) azonosítója**. Rendelje hozzá az alkalmazást a **közreműködő** szerepkörhöz az ugyanebben a cikkben található utasításokat követve.

### <a name="create-a-blob-and-a-sql-table"></a>Blob és SQL-tábla létrehozása

Készítse elő az Azure-blobot, és Azure SQL Database az oktatóanyaghoz forrás-blog és egy fogadó SQL-tábla létrehozásával.

#### <a name="create-a-source-blob"></a>Forrás blob létrehozása

Először hozzon létre egy forrás blobot egy tároló létrehozásával és egy bemeneti szövegfájl feltöltésével:

1. Nyissa meg a jegyzettömböt. Másolja az alábbi szöveget, és mentse helyileg egy *inputEmp.txt* nevű fájlba.

    ```inputEmp.txt
    John|Doe
    Jane|Doe
    ```

2. Hozzon létre egy eszközt, például [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) a *adfv2tutorial* -tároló létrehozásához, és töltse fel a *inputEmp.txt* fájlt a tárolóba.

#### <a name="create-a-sink-sql-table"></a>Fogadó SQL-tábla létrehozása

Következő lépésként hozzon létre egy fogadó SQL-táblázatot:

1. A következő SQL-szkripttel hozza létre a *dbo.emp* táblát az Azure SQL Database-ben.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Engedélyezi az Azure-szolgáltatások számára a SQL Database elérését. Győződjön meg arról, hogy engedélyezi az Azure-szolgáltatásokhoz való hozzáférést a kiszolgálón, így a Data Factory szolgáltatás adatírást tud írni SQL Databaseba. A beállítás ellenőrzéséhez és bekapcsolásához hajtsa végre a következő lépéseket:

    1. Az SQL Server kezeléséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **SQL-kiszolgálókat**.

    2. Válassza ki a kiszolgálót.

    3. Az SQL Server menü **biztonsági** fejléce alatt válassza a **tűzfalak és virtuális hálózatok** lehetőséget.

    4. A **tűzfal-és virtuális hálózatok** lapon, az **Azure-szolgáltatások és-erőforrások engedélyezése a kiszolgálóhoz való hozzáféréshez** területen válassza **a be** lehetőséget.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

Hozzon létre egy C# .NET-konzol alkalmazást a Visual Studióval.

1. Nyissa meg a Visual Studiót.
2. A **Start** ablakban válassza az **új projekt létrehozása** lehetőséget.
3. A **create a New Project (új projekt létrehozása** ) ablakban válassza ki a **Console app (.NET-keretrendszer)** C#-verzióját a projekttípus listájából. Ezután kattintson a **Tovább** gombra.
4. Az **új projekt konfigurálása** ablakban adja meg az *ADFv2Tutorial* **projekt nevét** . A **Hely mezőben** keresse meg és/vagy hozza létre azt a könyvtárat, amelybe menteni szeretné a projektet. Ezután kattintson a **Létrehozás** elemre. Az új projekt megjelenik a Visual Studio IDE-ban.

## <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

Ezután telepítse a szükséges függvénytár-csomagokat a NuGet csomagkezelő használatával.

1. A menüsávban válassza az **eszközök**  >  **NuGet Package** Manager  >  **csomagkezelő konzolt**.
2. A **Package Manager konzol** ablaktábláján futtassa a következő parancsokat a csomagok telepítéséhez. További információ a Azure Data Factory NuGet csomagról: [Microsoft. Azure. Management. DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```package manager console
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -PreRelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Adat-előállító ügyfél létrehozása

Az alábbi lépéseket követve hozzon létre egy adatfeldolgozó-ügyfelet.

1. Nyissa meg a *program.cs*, majd írja felül a meglévő `using` utasításokat a következő kóddal, hogy hozzáadja a névterekre mutató hivatkozásokat.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Adja hozzá a következő kódot a `Main` metódushoz, amely beállítja a változókat. Cserélje le a 14 helyőrzőt a saját értékeire.

    Ha szeretné megtekinteni azon Azure-régiók listáját, amelyekben a Data Factory jelenleg elérhető, tekintse meg a [régiókban elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/). A **termékek** legördülő listában válassza a **Tallózás**  >  **Analytics**  >  **Data Factory** lehetőséget. Ezután a **régiók** legördülő listában válassza ki az Önt érdeklő régiókat. Megjelenik egy rács a kiválasztott régiókban Data Factory termékek rendelkezésre állási állapotával.

    > [!NOTE]
    > Az adattárak, például az Azure Storage és a Azure SQL Database, valamint a számítások, például a HDInsight, a Data Factory által használt más régiókban is lehetnek, mint amit a Data Factory választott.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "<location to create the data factory in, such as East US>";
    string dataFactoryName = "<name of data factory to create (must be globally unique)>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString =
        "Server=tcp:<your server name>.database.windows.net,1433;" +
        "Database=<your database name>;" +
        "User ID=<your username>@<your server name>;" +
        "Password=<your password>;" +
        "Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Adja hozzá a következő kódot a `Main` metódushoz, amely létrehoz egy osztály egy példányát `DataFactoryManagementClient` . Ezzel az objektummal adat-előállítót, társított szolgáltatást, adatkészleteket és folyamatot hozhat létre. Ezenfelül ez az objektum a folyamat futása részleteinek monitorozására is használható.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync(
        "https://management.azure.com/", cc
    ).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

Adja hozzá a következő kódot a `Main` metódushoz, amely létrehoz egy *adatelőállítót*.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};

client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings)
);

while (
    client.Factories.Get(
        resourceGroup, dataFactoryName
    ).ProvisioningState == "PendingCreation"
)
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Ebben az oktatóanyagban két társított szolgáltatást hoz létre a forráshoz és a fogadóhoz.

### <a name="create-an-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása

Adja hozzá a következő kódot a `Main` metódushoz, amely létrehoz egy *Azure Storage-beli társított szolgáltatást*. További információ a támogatott tulajdonságokról és részletekről: [Azure Blob társított szolgáltatás tulajdonságai](connector-azure-blob-storage.md#linked-service-properties).

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey
        )
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings)
);
```

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása

Adja hozzá a következő kódot a `Main` metódushoz, amely létrehoz egy *Azure SQL Database társított szolgáltatást*. További információ a támogatott tulajdonságokról és részletekről: [Azure SQL Database társított szolgáltatás tulajdonságai](connector-azure-sql-database.md#linked-service-properties).

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings)
);
```

## <a name="create-datasets"></a>Adatkészletek létrehozása

Ebben a szakaszban két adatkészletet hoz létre: egyet a forráshoz, a másikat a fogadóhoz.

### <a name="create-a-dataset-for-source-azure-blob"></a>Adatkészlet létrehozása a forrás Azure Blobhoz

Adja hozzá a következő kódot a `Main` metódushoz, amely létrehoz egy *Azure Blob-adatkészletet*. További információ a támogatott tulajdonságokról és részletekről: [Azure Blob-adatkészlet tulajdonságai](connector-azure-blob-storage.md#dataset-properties).

Meghatároz egy adatkészletet, amely a forrásadatokat jelöli az Azure Blobban. Ez a Blob-adatkészlet az előző lépésben létrehozott Azure Storage-beli társított szolgáltatásra vonatkozik, és a következőket írja le:

- A másolandó blob helye: `FolderPath` és `FileName`
- A tartalom elemzését `TextFormat` , a hozzá tartozó beállításokat, például az oszlop elválasztóját jelző blob formátum
- Az adatstruktúra, beleértve az oszlopnevek és az adattípusokat, amelyek ebben a példában a fogadó SQL-táblára mutatnak.

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement { Name = "FirstName", Type = "String" },
            new DatasetDataElement { Name = "LastName", Type = "String" }
        }
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings)
);
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Adatkészlet létrehozása a fogadó Azure SQL Database-hez

Adja hozzá a következő kódot a `Main` metódushoz, amely létrehoz egy *Azure SQL Database adatkészletet*. További információ a támogatott tulajdonságokról és részletekről: [Azure SQL Database adatkészlet tulajdonságai](connector-azure-sql-database.md#dataset-properties).

Meghatároz egy adatkészletet, amely a fogadó adatait jelöli az Azure SQL Database-ben. Ez az adatkészlet az előző lépésben létrehozott Azure SQL Database társított szolgáltatásra hivatkozik. Emellett megadja a másolt adatokat tartalmazó SQL-táblázatot is.

```csharp
// Create an Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings)
);
```

## <a name="create-a-pipeline"></a>Folyamat létrehozása

Adja hozzá a következő kódot a `Main` metódushoz, amely létrehoz egy *másolási tevékenységgel rendelkező* folyamatot. Ebben az oktatóanyagban ez a folyamat egy tevékenységet tartalmaz: `CopyActivity` , amely a blob-adatkészletet forrásként és az SQL-adatkészletként veszi fel fogadóként. További információ a másolási tevékenység részleteiről: [másolási tevékenység Azure Data Factoryban](copy-activity-overview.md).

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference() { ReferenceName = blobDatasetName }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference { ReferenceName = sqlDatasetName }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings)
);
```

## <a name="create-a-pipeline-run"></a>Folyamat futásának létrehozása

Adja hozzá a következő kódot a `Main` *folyamat futtatását kiváltó* metódushoz.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Folyamat futásának monitorozása

Most szúrja be a kódot a folyamat futtatási állapotának vizsgálatához, és a másolási tevékenység futtatásának részleteit.

1. Adja hozzá a következő kódot a `Main` metódushoz a folyamat futási állapotának folyamatos vizsgálatához, amíg az adatok másolása be nem fejeződik.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(
            resourceGroup, dataFactoryName, runResponse.RunId
        );
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Adja hozzá a következő kódot a `Main` metódushoz, amely lekéri a másolási tevékenység futtatási részleteit, például az olvasott vagy írt adatok méretét.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    RunFilterParameters filterParams = new RunFilterParameters(
        DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)
    );

    ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
        resourceGroup, dataFactoryName, runResponse.RunId, filterParams
    );

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(queryResponse.Value.First().Output);
    }
    else
        Console.WriteLine(queryResponse.Value.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>A kód futtatása

Hozza létre az alkalmazást a **Build**  >  **Build megoldás** kiválasztásával. Ezután indítsa el az alkalmazást a **hibakeresés**  >  **megkezdése** parancs kiválasztásával, és ellenőrizze a folyamat végrehajtását.

A konzol megjeleníti az adat-előállító, a társított szolgáltatás, az adatkészletek, a folyamat, valamint a folyamat futása létrehozásának állapotát. Ezután ellenőrzi a folyamat futási állapotát. Várjon, amíg megjelenik a másolási tevékenység futtatási részletei az adatok olvasási/írási méretével. Ezt követően a SQL Server Management Studio (SSMS) vagy a Visual Studio használatával csatlakozhat a célhelyhez Azure SQL Database és megtekintheti, hogy a megadott céltábla tartalmazza-e a másolt adatait.

### <a name="sample-output"></a>Példakimenet

```json
Creating a data factory AdfV2Tutorial...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedDataIntegrationUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```

## <a name="next-steps"></a>Következő lépések

A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure Storage-beli és Azure SQL Database-beli társított szolgáltatások létrehozása
> * Azure Blob- és Azure SQL Database-adatkészletek létrehozása
> * Másolási tevékenységet tartalmazó folyamat létrehozása.
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Folytassa a következő oktatóanyaggal, amelyben azzal ismerkedhet meg, hogyan másolhat adatokat a helyszíni rendszerből a felhőbe:

> [!div class="nextstepaction"]
>[Adatok másolása a helyszínről a felhőbe](tutorial-hybrid-copy-powershell.md)
