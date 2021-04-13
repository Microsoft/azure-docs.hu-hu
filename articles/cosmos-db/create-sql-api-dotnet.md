---
title: Rövid útmutató – .NET-konzolalkalmazás összeállítása az SQL API Azure Cosmos DB kezeléséhez
description: Ebből a rövid útmutatóból megtudhatja, hogyan építhet ki egy .NET-konzolalkalmazást az SQL API Azure Cosmos DB erőforrásainak kezeléséhez.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/07/2021
ms.custom: devx-track-dotnet
ms.openlocfilehash: c7dc3a230e2cd3f990771c297de29891de347089
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363410"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Rövid útmutató: .NET-konzolalkalmazás összeállítása az SQL API Azure Cosmos DB erőforrásainak kezeléséhez
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Spark v3-összekötő](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

A .NET-hez Azure Cosmos DB SQL API ügyféloldali kódtárának első lépések. A dokumentum lépéseit követve telepítse a .NET-csomagot, készítsen alkalmazást, és próbálja ki az alapszintű CRUD-műveletekhez szükséges példakódot a Azure Cosmos DB. 

Azure Cosmos DB a Microsoft gyors NoSQL-adatbázisa, amely bármilyen mérethez nyílt API-kat igényel. Az adatbázis-Azure Cosmos DB kulcs-/érték-, dokumentum- és gráfadatbázisok gyors létrehozásához és lekérdezéséhez használhatja. Használja a Azure Cosmos DB SQL API .NET-hez való ügyféloldali kódtárát a következőre:

* Azure Cosmos-adatbázis és -tároló létrehozása
* Mintaadatok hozzáadása a tárolóhoz
* Adatok lekérdezése 
* Az adatbázis törlése

[API-referenciadokumentáció](/dotnet/api/microsoft.azure.cosmos)  |  [Kódtár forráskódja](https://github.com/Azure/azure-cosmos-dotnet-v3)  |  [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– hozzon létre](https://azure.microsoft.com/free/) egyet ingyenesen, vagy [kipróbálhatja](https://azure.microsoft.com/try/cosmosdb/) Azure Cosmos DB azure-előfizetés, ingyenes és kötelezettségvállalások nélkül. 
* A [.NET Core 2.1 SDK vagy újabb.](https://dotnet.microsoft.com/download/dotnet-core/2.1)

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti egy Azure Cosmos-fiók létrehozásának és egy olyan projekt beállításának folyamatán, amely a .NET-hez Azure Cosmos DB SQL API ügyféloldali kódtárát használja az erőforrások kezeléséhez. A cikkben leírt példakód létrehoz egy adatbázist és egy családtagot `FamilyDatabase` (minden családtag egy elem) az adatbázisban. Minden családtag rendelkezik olyan tulajdonságokkal, mint a `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` . A `LastName` tároló partíciókulcsa a tulajdonság. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Azure Cosmos-fiók létrehozása

Ha az Ingyenes [Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) lehetőséget használja egy Azure Cosmos-fiók létrehozásához, létre kell hoznia egy SQL API típusú **Azure Cosmos DB-fiókot.** Egy Azure Cosmos DB tesztfiók már létre van hozva. Nem kell explicit módon létrehoznia a fiókot, így kihagyhatja ezt a szakaszt, és továbbléphet a következő szakaszra.

Ha saját Azure-előfizetéssel rendelkezik, vagy ingyenes előfizetést hozott létre, explicit módon hozzon létre egy Azure Cosmos-fiókot. A következő kód létrehoz egy Azure Cosmos-fiókot munkamenet-konzisztenciával. A fiók replikálása a és a `South Central US` rendszerben `North Central US` történt.  

A fiókkal Azure Cloud Shell Azure Cosmos-fiókot. Az Azure Cloud Shell interaktív, hitelesített, böngészőben elérhető felület az Azure-erőforrások felügyeletéhez. Rugalmasságot nyújt a munkamódszeréhez leginkább illő héjfelület (akár a Bash, akár a PowerShell) kiválasztásának lehetőségével. Ebben a rövid útmutatóban válassza a **Bash mód** lehetőséget. Azure Cloud Shell tárfiókra is szükség van, akkor létrehozhat egyet, amikor a rendszer erre kéri.

Kattintson a **következő kód melletti Kipróbálom** gombra, válassza a **Bash** mód lehetőséget, válassza **a Tárfiók** létrehozása lehetőséget, és jelentkezzen be az Cloud Shell. Ezután másolja és illessze be a következő kódot Azure Cloud Shell és futtassa. Az Azure Cosmos-fiók nevének globálisan egyedinek kell lennie, ezért a parancs futtatása előtt frissítse `mysqlapicosmosdb` az értéket.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Az Azure Cosmos-fiók létrehozása egy kis időt vesz igénybe. Ha a művelet sikeres volt, megjelenik a megerősítési kimenet. Miután a parancs sikeresen lefutott, jelentkezzen be a Azure Portal, és ellenőrizze, hogy létezik-e a megadott nevű Azure Cosmos-fiók. [](https://portal.azure.com/) Az erőforrás létrehozása után bezárhatja Azure Cloud Shell ablakát. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Új .NET-alkalmazás létrehozása

Hozzon létre egy új .NET-alkalmazást a kívánt szerkesztőben vagy IDE-ban. Nyissa meg a Windows-parancssort vagy a Terminál ablakot a helyi számítógépről. A következő szakaszokban található összes parancsot a parancssorból vagy terminálból fogja futtatni.  Futtassa a következő dotnet new parancsot egy nevű új alkalmazás `todo` létrehozásához. A --langVersion paraméter beállítja a LangVersion tulajdonságot a létrehozott projektfájlban.

```console
dotnet new console --langVersion 7.1 -n todo
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazásmappába. Az alkalmazást a következővel építheti fel:

```console
cd todo
dotnet build
```

A build várt kimenetének a következő módon kell kinéznie:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Az Azure Cosmos DB csomag telepítése

Az alkalmazás könyvtárában maradva telepítse a .NET Core-Azure Cosmos DB ügyféloldali kódtárát a dotnet add package paranccsal.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Másolja az Azure Cosmos-fiók hitelesítő adatait a Azure Portal

A mintaalkalmazásnak hitelesítenie kell magát az Azure Cosmos-fiókjában. A hitelesítéshez át kell adni az Azure Cosmos-fiók hitelesítő adatait az alkalmazásnak. Az Azure Cosmos-fiók hitelesítő adatait az alábbi lépésekkel lehet lehozni:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Lépjen az Azure Cosmos-fiókjához.

1. Nyissa meg **a Kulcsok panelt,** és másolja ki a fiók **URI-ját** és **ELSŐDLEGES** KULCSát. Az URI és a kulcsok értékeit a következő lépésben fogja hozzáadni egy környezeti változóhoz.

### <a name="set-the-environment-variables"></a>A környezeti változók beállítása

Miután átmásolta a fiók **URI-ját** és **ELSŐDLEGES** KULCSát, mentse őket egy új környezeti változóba az alkalmazást futtató helyi gépen. A környezeti változó beállításhoz nyisson meg egy konzolablakot, és futtassa a következő parancsot. Ügyeljen arra, hogy a és az `<Your_Azure_Cosmos_account_URI>` értékeket `<Your_Azure_Cosmos_account_PRIMARY_KEY>` cserélje le.

**Windows**

```console
setx EndpointUrl "<Your_Azure_Cosmos_account_URI>"
setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Linux**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**macOS**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a name="object-model"></a><a id="object-model"></a>Objektummodell

Az alkalmazás létrehozása előtt nézzük meg az erőforrás-hierarchiát a Azure Cosmos DB és az erőforrások létrehozásához és eléréséhez használt objektummodellhez. A Azure Cosmos DB a következő sorrendben hozza létre az erőforrásokat:

* Azure Cosmos-fiók 
* Adatbázisok 
* Tárolók 
* Elemek

A különböző entitások hierarchiájának további tudnivalókért tekintse meg az [adatbázisok,](account-databases-containers-items.md) tárolók és elemek a Azure Cosmos DB cikkét. Ezeket az erőforrásokat a következő .NET-osztályokkal fogja használni:

* [CosmosClient](/dotnet/api/microsoft.azure.cosmos.cosmosclient) – Ez az osztály ügyféloldali logikai megjelenítést biztosít a Azure Cosmos DB számára. Az ügyfélobjektum a szolgáltatásra vonatkozó kérések konfigurálára és végrehajtására használható.

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync) – Ez a metódus (ha még nem létezik) vagy (ha már létezik) adatbázis-erőforrást hoz létre aszinkron műveletként. 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync)– – Ez a metódus aszinkron műveletként hoz létre (ha még nem létezik) vagy (ha már létezik) egy tárolót. A válasz állapotkódját ellenőrizve megállapíthatja, hogy a tároló újonnan lett létrehozva (201) vagy egy meglévő tároló lett visszaadva (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync) – Ez a metódus egy elemet hoz létre a tárolóban. 

* [UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync) – Ez a metódus létrehoz egy elemet a tárolóban, ha még nem létezik, vagy lecseréli az elemet, ha már létezik. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator) – Ez a metódus paraméteres értékeket tartalmazó SQL-utasítással hoz létre lekérdezést egy Azure Cosmos-adatbázisban lévő tárolóban lévő elemekhez. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync) – Törli a megadott adatbázist az Azure Cosmos-fiókból. `DeleteAsync` A metódus csak az adatbázist törli. A példány törlésének külön kell történnie `Cosmosclient` (ezt a DeleteDatabaseAndCleanupAsync metódusban teszi meg). 

 ## <a name="code-examples"></a><a id="code-examples"></a>Kódpéldák

A cikkben leírt mintakód létrehoz egy családadatbázist a Azure Cosmos DB. A családadatbázis olyan családadatokat tartalmaz, mint a név, a cím, a hely, a kapcsolódó szülők, gyermekek és kisállatok. Mielőtt az adatokat az Azure Cosmos-fiókba berakja, definiálja egy családelem tulajdonságait. Hozzon létre egy nevű új osztályt a mintaalkalmazás gyökérszintjénél, és adja hozzá a `Family.cs` következő kódot:

```csharp
using Newtonsoft.Json;

namespace todo
{
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        // The ToString() method is used to format the output, it's used for demo purpose only. It's not required by Azure Cosmos DB
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
}
```

### <a name="add-the-using-directives--define-the-client-object"></a>Adja hozzá a using irányelveket & ügyfélobjektum definiálása érdekében

A projektkönyvtárból nyissa meg a fájlt a szerkesztőben, és adja hozzá a következő using irányelveket az `Program.cs` alkalmazás tetején:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

A **Program.cs fájlhoz** adja hozzá az előző lépésben beállított környezeti változókat beolvasó kódot. Határozza meg `CosmosClient` a , a és az `Database` `Container` objektumokat. Ezután adja hozzá a kódot a fő metódushoz, amely az Azure Cosmos-fiók erőforrásainak kezelésére `GetStartedDemoAsync` használt metódust hívja meg. 

```csharp
namespace todo
{
public class Program
{

    /// The Azure Cosmos DB endpoint for running this GetStarted sample.
    private string EndpointUrl = Environment.GetEnvironmentVariable("EndpointUrl");

    /// The primary key for the Azure DocumentDB account.
    private string PrimaryKey = Environment.GetEnvironmentVariable("PrimaryKey");

    // The Cosmos client instance
    private CosmosClient cosmosClient;

    // The database we will create
    private Database database;

    // The container we will create.
    private Container container;

    // The name of the database and container we will create
    private string databaseId = "FamilyDatabase";
    private string containerId = "FamilyContainer";

    public static async Task Main(string[] args)
    {
       try
       {
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();

        }
        catch (CosmosException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}", de.StatusCode, de);
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: {0}", e);
        }
        finally
        {
            Console.WriteLine("End of demo, press any key to exit.");
            Console.ReadKey();
        }
    }
}
}
```

### <a name="create-a-database"></a>Adatbázis létrehozása 

Definiálja `CreateDatabaseAsync` a metódust a `program.cs` osztályon belül. Ez a metódus létrehozza `FamilyDatabase` a et, ha még nem létezik.

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Tároló létrehozása

Definiálja `CreateContainerAsync` a metódust a `program.cs` osztályon belül. Ez a metódus létrehozza `FamilyContainer` a metódust, ha még nem létezik. 

```csharp
/// Create the container if it does not exist. 
/// Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
private async Task CreateContainerAsync()
{
    // Create a new container
    this.container = await this.database.CreateContainerIfNotExistsAsync(containerId, "/LastName");
    Console.WriteLine("Created Container: {0}\n", this.container.Id);
}
```

### <a name="create-an-item"></a>Elem létrehozása

Hozzon létre egy családelemet a `AddItemsToContainerAsync` metódus hozzáadásával az alábbi kóddal. Elemet a `CreateItemAsync` vagy a metódus használatával hozhat `UpsertItemAsync` létre:

```csharp
private async Task AddItemsToContainerAsync()
{
    // Create a family object for the Andersen family
    Family andersenFamily = new Family
    {
        Id = "Andersen.1",
        LastName = "Andersen",
        Parents = new Parent[]
        {
           new Parent { FirstName = "Thomas" },
           new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
           new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = false
    };

    try
    {
        // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen".
        ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));
        // Note that after creating the item, we can access the body of the item with the Resource property of the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
        Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
    }
    catch (CosmosException ex) when (ex.StatusCode == HttpStatusCode.Conflict)
    {
        Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamily.Id);                
    }
}

```

### <a name="query-the-items"></a>Az elemek lekérdezése

Az elem beszúrása után egy lekérdezés futtatásával lekérdezheti az "Andersen" család részleteit. Az alábbi kód bemutatja, hogyan hajthatja végre a lekérdezést közvetlenül az SQL-lekérdezés használatával. A "Annak" család részleteit lekért SQL-lekérdezés a következő: `SELECT * FROM c WHERE c.LastName = 'Andersen'` . Definiálja `QueryItemsAsync` a metódust a `program.cs` osztályon belül, és adja hozzá a következő kódot:


```csharp
private async Task QueryItemsAsync()
{
    var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";

    Console.WriteLine("Running query: {0}\n", sqlQueryText);

    QueryDefinition queryDefinition = new QueryDefinition(sqlQueryText);
    FeedIterator<Family> queryResultSetIterator = this.container.GetItemQueryIterator<Family>(queryDefinition);

    List<Family> families = new List<Family>();

    while (queryResultSetIterator.HasMoreResults)
    {
        FeedResponse<Family> currentResultSet = await queryResultSetIterator.ReadNextAsync();
        foreach (Family family in currentResultSet)
        {
            families.Add(family);
            Console.WriteLine("\tRead {0}\n", family);
        }
    }
}

```

### <a name="delete-the-database"></a>Az adatbázis törlése 

Végül törölheti az adatbázist a metódus hozzáadásával `DeleteDatabaseAndCleanupAsync` a következő kóddal:

```csharp
private async Task DeleteDatabaseAndCleanupAsync()
{
   DatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
   // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

   Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

   //Dispose of CosmosClient
    this.cosmosClient.Dispose();
}
```

### <a name="execute-the-crud-operations"></a>CRUD-műveletek végrehajtása

Miután meghatározta az összes szükséges metódust, hajtsa végre őket a `GetStartedDemoAsync` metódusban. A metódus megjegyzésbe tette ezt a kódot, mert a metódus végrehajtása esetén nem fog látni `DeleteDatabaseAndCleanupAsync` erőforrásokat. Miután érvényesítette, hogy a Azure Cosmos DB erőforrások létrejöttek a Azure Portal. 

```csharp
public async Task GetStartedDemoAsync()
{
    // Create a new instance of the Cosmos Client
    this.cosmosClient = new CosmosClient(EndpointUrl, PrimaryKey);
    await this.CreateDatabaseAsync();
    await this.CreateContainerAsync();
    await this.AddItemsToContainerAsync();
    await this.QueryItemsAsync();
}
```

Az összes szükséges metódus hozzáadása után mentse a `Program.cs` fájlt. 

## <a name="run-the-code"></a>A kód futtatása

Ezután hozza létre és futtassa az alkalmazást a Azure Cosmos DB létrehozásához. Nyisson meg egy új parancssori ablakot, és ne használja ugyanazt a példányt, mint a környezeti változók beállítására. Mivel a környezeti változók nincsenek beállítva az aktuális megnyitott ablakban. Meg kell nyitnia egy új parancssort, hogy lássa a frissítéseket. 

```console
dotnet build
```

```console
dotnet run
```

Az alkalmazás futtatásakor a következő kimenet jön létre. Bejelentkezhet a Azure Portal és ellenőrizheti, hogy az erőforrások létrejöttek-e:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

Az adatok létrejöttének ellenőrzéséhez bejelentkezhet a Azure Portal és láthatja az Azure Cosmos-fiókban szükséges elemeket. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az Azure CLI vagy a Azure PowerShell eltávolíthatja az Azure Cosmos-fiókot és a megfelelő erőforráscsoportot. A következő parancs bemutatja, hogyan törölheti az erőforráscsoportot az Azure CLI használatával:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure Cosmos-fiókot, hogyan hozhat létre adatbázist és tárolót egy .NET Core-alkalmazással. Most már további adatokat importálhat Azure Cosmos-fiókjába az alábbi cikkben található utasításokkal. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)
