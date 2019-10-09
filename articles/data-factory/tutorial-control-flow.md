---
title: Elágaztatás Azure Data Factory-folyamatokban | Microsoft Docs
description: Megismerheti, hogyan vezérelheti az adatok folyamát az Azure Data Factoryben elágaztatási és láncolási tevékenységekkel.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 9/27/2019
ms.openlocfilehash: d8ea5a507cc110c92bb74491c3376f7b671638d9
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176009"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Elágaztatási és láncolási tevékenységek a Data Factory-folyamatokban

Ebben az oktatóanyagban egy Data Factory folyamatot hoz létre, amely a vezérlési folyamat egyes funkcióit mutatja be. Ez a folyamat egy Azure-beli tárolóból, egy másik tárolóba, ugyanazon a Storage-fiókba másolja a Blob Storage. Ha a másolási tevékenység sikeres, a folyamat egy e-mailben küldi el a sikeres másolási művelet részleteit. Ezek az adatok magukban foglalhatják a megírt adatok mennyiségét. Ha a másolási tevékenység meghiúsul, a másolási hiba részleteit küldi el, például a hibaüzenetet egy e-mailben. Az oktatóanyag során megismerheti, hogyan adhatók át a paraméterek.

Ez a ábra áttekintést nyújt a forgatókönyvről:

![Áttekintés](media/tutorial-control-flow/overview.png)

Ez az oktatóanyag bemutatja, hogyan végezheti el a következő feladatokat:

> [!div class="checklist"]
> * Data factory létrehozása
> * Azure Storage-beli társított szolgáltatás létrehozása
> * Azure Blob-adatkészlet létrehozása
> * Másolási tevékenységet és webes tevékenységet tartalmazó folyamat létrehozása
> * Tevékenységek kimeneteinek elküldése a soron következő tevékenységek számára
> * Paraméter átadása és rendszerváltozók használata
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Ez az oktatóanyag a .NET SDK-t használja. Más mechanizmusokkal is használhatja a Azure Data Factory. Data Factory rövid útmutatók: [5 perces](/azure/data-factory/quickstart-create-data-factory-portal)gyors útmutató.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Azure Storage-fiók. A blob Storage-t forrásként szolgáló adattárként használhatja. Ha nem rendelkezik Azure Storage-fiókkal, tekintse meg [a Storage-fiók létrehozása](../storage/common/storage-quickstart-create-account.md)című témakört.
* Azure Storage Explorer. Az eszköz telepítéséhez lásd: [Azure Storage Explorer](https://storageexplorer.com/).
* Azure SQL Database. Az adatbázist fogadó adattárként használja. Ha nem rendelkezik Azure SQL Databaseval, tekintse meg [Az Azure SQL Database létrehozása](../sql-database/sql-database-get-started-portal.md)című témakört.
* Visual Studio. Ez a cikk a Visual Studio 2019-et használja.
* Azure .NET SDK. Töltse le és telepítse az [Azure .net SDK](https://azure.microsoft.com/downloads/)-t.

Azon Azure-régiók listájáért, amelyekben jelenleg Data Factory érhető el, tekintse meg a [régiókban elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/). Az adattárak és a számítások más régiókban is lehetnek. Az üzletek közé tartozik az Azure Storage és a Azure SQL Database. A számítások közé tartoznak a HDInsight, amelyeket a Data Factory használ.

Hozzon létre egy alkalmazást az [Azure Active Directory alkalmazás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)című témakörben leírtak szerint. Rendelje hozzá az alkalmazást a **közreműködő** szerepkörhöz az ugyanebben a cikkben található utasításokat követve. Az oktatóanyag későbbi részeihez több érték szükséges, például az **alkalmazás (ügyfél) azonosítója** és a **címtár (bérlő) azonosítója**.

### <a name="create-a-blob-table"></a>BLOB-tábla létrehozása

1. Nyisson meg egy szövegszerkesztőt. Másolja a következő szöveget, és mentse helyileg a *input. txt*néven.

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. Nyissa meg Azure Storage Explorer. Bontsa ki a Storage-fiókját. Kattintson a jobb gombbal a **blob-tárolók** elemre, majd válassza a **blob tároló létrehozása**lehetőséget.
1. Nevezze el az új tároló *adfv2branch* , és válassza a **feltöltés** lehetőséget, hogy hozzáadja a *bemeneti. txt* fájlt a tárolóhoz.

## Visual Studio-projekt létrehozása<a name="create-visual-studio-project"></a>

C# .Net-konzol alkalmazás létrehozása:

1. Indítsa el a Visual studiót, és válassza **az új projekt létrehozása**lehetőséget.
1. A **create a New Project (új projekt létrehozása**) területen válassza a **konzol alkalmazás (.NET-keretrendszer)** lehetőséget, és kattintson a C# **Tovább gombra**.
1. Nevezze el a projekt *ADFv2BranchTutorial*.
1. Válassza ki a **.net 4.5.2** -es vagy újabb verzióját, majd válassza a **Létrehozás**lehetőséget.

### <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

1. Válassza az **eszközök**@no__t – 1**NuGet csomagkezelő**@no__t – 3**csomagkezelő konzolt**.
1. A **Package Manager konzolon**futtassa a következő parancsokat a csomagok telepítéséhez. A részletekért tekintse meg a [Microsoft. Azure. Management. DataFactory nuget csomagot](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/) .

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>Adat-előállító ügyfél létrehozása

1. Nyissa meg a *program.cs* , és adja hozzá a következő utasításokat:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.DataFactory;
   using Microsoft.Azure.Management.DataFactory.Models;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```

1. Adja hozzá ezeket a statikus változókat a `Program` osztályhoz. Cserélje le a helyőrzőket saját értékeire.

   ```csharp
   // Set variables
   static string tenantID = "<tenant ID>";
   static string applicationId = "<application ID>";
   static string authenticationKey = "<Authentication key for your application>";
   static string subscriptionId = "<Azure subscription ID>";
   static string resourceGroup = "<Azure resource group name>";

   static string region = "East US";
   static string dataFactoryName = "<Data factory name>";

   // Specify the source Azure Blob information
   static string storageAccount = "<Azure Storage account name>";
   static string storageKey = "<Azure Storage account key>";
   // confirm that you have the input.txt file placed in th input folder of the adfv2branch container. 
   static string inputBlobPath = "adfv2branch/input";
   static string inputBlobName = "input.txt";
   static string outputBlobPath = "adfv2branch/output";
   static string emailReceiver = "<specify email address of the receiver>";

   static string storageLinkedServiceName = "AzureStorageLinkedService";
   static string blobSourceDatasetName = "SourceStorageDataset";
   static string blobSinkDatasetName = "SinkStorageDataset";
   static string pipelineName = "Adfv2TutorialBranchCopy";

   static string copyBlobActivity = "CopyBlobtoBlob";
   static string sendFailEmailActivity = "SendFailEmailActivity";
   static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
   ```

1. Adja hozzá a következő kódot a `Main` metódushoz: Ez a kód `DataFactoryManagementClient` osztály egy példányát hozza létre. Ezt az objektumot használhatja az adat-előállító, a társított szolgáltatás, az adatkészletek és a folyamat létrehozásához. Ezzel az objektummal is figyelheti a folyamat futtatásának részleteit.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>Data factory létrehozása

1. Adjon hozzá egy `CreateOrUpdateDataFactory` metódust a *program.cs* -fájlhoz:

   ```csharp
   static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating data factory " + dataFactoryName + "...");
       Factory resource = new Factory
       {
           Location = region
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

       Factory response;
       {
           response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
       }

       while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
       {
           System.Threading.Thread.Sleep(1000);
       }
       return response;
   }
   ```

1. Adja hozzá a következő sort a `Main` metódushoz, amely létrehoz egy adatelőállítót:

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása

1. Adjon hozzá egy `StorageLinkedServiceDefinition` metódust a *program.cs* -fájlhoz:

   ```csharp
   static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
   {
      Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
      AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
      {
          ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
      };
      Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
      LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
      return linkedService;
   }
   ```

1. Adja hozzá a következő sort a `Main` metódushoz, amely létrehoz egy Azure Storage-beli társított szolgáltatást:

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

További információ a támogatott tulajdonságokról és részletekről: [társított szolgáltatás tulajdonságai](connector-azure-blob-storage.md#linked-service-properties).

## <a name="create-datasets"></a>Adatkészletek létrehozása

Ebben a szakaszban két adatkészletet hoz létre, egyet a forráshoz és egyet a fogadóhoz.

### <a name="create-a-dataset-for-a-source-azure-blob"></a>Adatkészlet létrehozása egy forrás Azure-Blobhoz

Adjon hozzá egy olyan metódust, amely létrehoz egy *Azure Blob-adatkészletet*. További információ a támogatott tulajdonságokról és részletekről: [Azure Blob-adatkészlet tulajdonságai](connector-azure-blob-storage.md#dataset-properties).

Adjon hozzá egy `SourceBlobDatasetDefinition` metódust a *program.cs* -fájlhoz:

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    { 
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

Meghatároz egy adatkészletet, amely a forrásadatokat jelöli az Azure Blobban. Ez a blob-adatkészlet az előző lépésben támogatott Azure Storage társított szolgáltatásra hivatkozik. A blob-adatkészlet leírja a másolandó blob helyét: *FolderPath* és *fájlnév*.

Figyelje meg a *FolderPath*paramétereinek használatát. a `sourceBlobContainer` a paraméter neve, a kifejezés helyére pedig a folyamat futása során átadott értékek szerepelnek. A paraméterek meghatározására szolgáló szintaxis: `@pipeline().parameters.<parameterName>`

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>Adatkészlet létrehozása egy fogadó Azure-Blobhoz

1. Adjon hozzá egy `SourceBlobDatasetDefinition` metódust a *program.cs* -fájlhoz:

   ```csharp
   static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
       AzureBlobDataset blobDataset = new AzureBlobDataset
       {
           FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
           LinkedServiceName = new LinkedServiceReference
           {
               ReferenceName = storageLinkedServiceName
           }
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
       DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
       return dataset;
   }
   ```

1. Adja hozzá a következő kódot a `Main` metódushoz, amely az Azure Blob Source és a fogadó adatkészleteket is létrehozza.

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>Hozzon C# létre egy osztályt: EmailRequest

A C# projektben hozzon létre egy `EmailRequest` nevű osztályt. Ez az osztály határozza meg, hogy a folyamat milyen tulajdonságokat küldjön a törzs kérelmében e-mailben. Ebben az oktatóanyagban a folyamat négy tulajdonságot küld a folyamatból az e-mailbe:

* az üzenet. Az e-mail törzse. Sikeres másolás esetén ez a tulajdonság tartalmazza a megírt adatmennyiséget. Sikertelen másolás esetén ez a tulajdonság tartalmazza a hiba részleteit.
* Az adatelőállító neve. Az adatelőállító neve.
* A folyamat neve. A folyamat neve.
* Fogadó. A paraméter, amely áthalad. Ez a tulajdonság adja meg az e-mail fogadóját.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```

## <a name="create-email-workflow-endpoints"></a>E-mail munkafolyamat végpontjainak létrehozása

E-mail küldésének aktiválásához a [Logic Apps](../logic-apps/logic-apps-overview.md) használatával határozhat meg munkafolyamatot. Logic Apps munkafolyamat létrehozásával kapcsolatos részletekért lásd: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

### <a name="success-email-workflow"></a>Sikeres műveletről tájékoztató e-mail munkafolyamata

A [Azure Portal](https://portal.azure.com)hozzon létre egy *copysuccessemail munkafolyamatot*nevű Logic apps-munkafolyamatot. Adja meg a munkafolyamat-triggert `When an HTTP request is received` értékkel. A kérelem eseményindítójához a `Request Body JSON Schema` esetében adja meg a következő JSON-t:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

A munkafolyamat a következő példához hasonlóan néz ki:

![Sikeres műveletről tájékoztató e-mail munkafolyamata](media/tutorial-control-flow/success-email-workflow-trigger.png)

Ez a JSON-tartalom az előző szakaszban létrehozott `EmailRequest` osztályhoz igazodik.

@No__t-0 művelet hozzáadása. Az **E-mail küldése** művelethez testre szabhatja az e-mailek formázásának módját a kérelem **törzse** JSON-sémájában átadott tulajdonságok használatával. Például:

![Logic app Designer – e-mail küldése művelet](media/tutorial-control-flow/customize-send-email-action.png)

A munkafolyamat mentése után másolja és mentse a **http post URL-címet** az triggerből.

## <a name="fail-email-workflow"></a>Sikertelen műveletről tájékoztató e-mail munkafolyamata

A **copysuccessemail munkafolyamatot** klónozása egy másik Logic apps *CopyFailEmail*nevű munkafolyamat. A kérelem eseményindítójában a `Request Body JSON schema` ugyanaz. Módosítsa az e-mail formátumát (például a `Subject` értékét) a sikertelen műveletről tájékoztató e-mailnek megfelelően. Például:

![Logic app Designer – sikertelen e-mail munkafolyamat](media/tutorial-control-flow/fail-email-workflow.png)

A munkafolyamat mentése után másolja és mentse a **http post URL-címet** az triggerből.

Ekkor két munkafolyamat-URL-címmel kell rendelkeznie, például a következő példákkal:

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>Folyamat létrehozása

Lépjen vissza a projekthez a Visual Studióban. Most hozzáadjuk azt a kódot, amely létrehoz egy másolási tevékenységgel rendelkező folyamatot, és `DependsOn` tulajdonságot. Ebben az oktatóanyagban a folyamat egy tevékenységet tartalmaz, egy másolási tevékenységet, amely a blob-adatkészletet forrásként és egy másik blob-adatkészletként veszi át fogadóként. Ha a másolási tevékenység sikeres vagy sikertelen, akkor más e-mail-feladatokat hív meg.

Ebben a folyamatban a következő funkciókat használja:

* Paraméterek
* Webes tevékenység
* Tevékenységfüggőség
* Tevékenység kimenetének használata bemenetként egy másik tevékenységhez

1. Adja hozzá ezt a metódust a projekthez. A következő szakaszokban részletesebben is tájékozódhat.

    ```csharp
    static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
            {
                Console.WriteLine("Creating pipeline " + pipelineName + "...");
                PipelineResource resource = new PipelineResource
                {
                    Parameters = new Dictionary<string, ParameterSpecification>
                    {
                        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                    },
                    Activities = new List<Activity>
                    {
                        new CopyActivity
                        {
                            Name = copyBlobActivity,
                            Inputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSourceDatasetName
                                }
                            },
                            Outputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSinkDatasetName
                                }
                            },
                            Source = new BlobSource { },
                            Sink = new BlobSink { }
                        },
                        new WebActivity
                        {
                            Name = sendSuccessEmailActivity,
                            Method = WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Succeeded" }
                                }
                            }
                        },
                        new WebActivity
                        {
                            Name = sendFailEmailActivity,
                            Method =WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Failed" }
                                }
                            }
                        }
                    }
                };
                Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
                return resource;
            }
    ```

1. Adja hozzá a következő sort a `Main` metódushoz, amely létrehozza a folyamatot:

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>Paraméterek

A folyamat kódjának első szakasza határozza meg a paramétereket.

* `sourceBlobContainer`. A forrás blob-adatkészlet ezt a paramétert használja a folyamatban.
* `sinkBlobContainer`. A fogadó blob-adatkészlet ezt a paramétert használja a folyamatban.
* `receiver`. A folyamat két webes tevékenysége, amelyek sikeres vagy sikertelen e-maileket küldenek a fogadónak, ezt a paramétert használják.

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Webes tevékenység

A webes tevékenység lehetővé teszi a REST-végpontok hívását. További információ a tevékenységről: [webes tevékenység Azure Data Factoryban](control-flow-web-activity.md). Ez a folyamat egy webes tevékenységgel hívja meg az Logic Apps e-mail-munkafolyamatot. Két webes tevékenységet hoz létre: az egyiket, amely meghívja a `CopySuccessEmail` munkafolyamatot, a másik pedig a `CopyFailWorkFlow`.

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```

A `Url` tulajdonságban illessze be a **http post URL-** végpontokat a Logic apps munkafolyamatokból. A `Body` tulajdonságban adja át a `EmailRequest` osztály egy példányát. Az e-mail-kérelem a következő tulajdonságokat tartalmazza:

* az üzenet. A `@{activity('CopyBlobtoBlob').output.dataWritten` értéket adja át. Hozzáfér az előző másolási tevékenység tulajdonságához, és átadja a `dataWritten` értékét. Sikertelen művelet esetén az átadott érték a `@{activity('CopyBlobtoBlob').error.message` helyett a hibakimenet.
* Data Factory neve. @No__t-0 értékének átadása ez a rendszerváltozó lehetővé teszi a megfelelő adatelőállító-név elérését. A rendszerváltozók listáját a [rendszerváltozók](control-flow-system-variables.md)részben tekintheti meg.
* A folyamat neve. A `@{pipeline().Pipeline}` értéket adja át. Ez a rendszerváltozó lehetővé teszi a megfelelő folyamat nevének elérését.
* Fogadó. A `"@pipeline().parameters.receiver"` értéket adja át. A folyamat paramétereinek elérése.

Ez a kód olyan új tevékenység-függőséget hoz létre, amely az előző másolási tevékenységtől függ.

## <a name="create-a-pipeline-run"></a>Folyamat futásának létrehozása

Adja hozzá a következő kódot a `Main` metódushoz, amely elindítja a folyamat futtatását.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};
 
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Main osztály

Az utolsó `Main` metódusnak így kell kinéznie.

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

Állítsa össze és futtassa a programot a folyamat futásának aktiválásához.

## <a name="monitor-a-pipeline-run"></a>Folyamat futásának monitorozása

1. Adja hozzá a következő kódot a `Main` metódushoz:

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

    Ez a kód folyamatosan ellenőrzi a Futtatás állapotát, amíg be nem fejeződik az adatok másolása.

1. Adja hozzá a következő kódot a `Main` metódushoz, amely lekéri a másolási tevékenység futtatási részleteit, például az olvasott/írt adatok méretét:

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
 
    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>A kód futtatása

Állítsa össze és indítsa el az alkalmazást, majd ellenőrizze a folyamat-végrehajtást.

Az alkalmazás megjeleníti az adat-előállító, a társított szolgáltatás, az adatkészletek, a folyamat és a folyamat futtatásának folyamatát. Ezután ellenőrzi a folyamat futási állapotát. Várjon, amíg megjelennek a másolási tevékenység futásának részletei, beleértve az olvasott és írt adatok méretét. Ezt követően az Azure Storage Explorer segítségével tekintse át a blobot a *outputBlobPath* -ből a *inputBlobPath* -be a változók területen megadott módon.

A kimenetnek az alábbi példához hasonlónak kell lennie:

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": {
      "type": "SecureString",
      "value": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
    }
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következő feladatokat végezte el:

> [!div class="checklist"]
> * Data factory létrehozása
> * Azure Storage-beli társított szolgáltatás létrehozása
> * Azure Blob-adatkészlet létrehozása
> * Másolási tevékenységet és webes tevékenységet tartalmazó folyamat létrehozása
> * Tevékenységek kimeneteinek elküldése a soron következő tevékenységek számára
> * Paraméter átadása és rendszerváltozók használata
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Most már folytathatja a fogalmak szakaszt a Azure Data Factoryával kapcsolatos további információkért.
> [!div class="nextstepaction"]
>[Folyamatok és tevékenységek](concepts-pipelines-activities.md)