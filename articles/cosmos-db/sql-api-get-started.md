---
title: 'Oktatóanyag: .NET-konzolos alkalmazás létrehozása Azure Cosmos DB SQL API-fiókban tárolt adatkezeléshez'
description: 'Oktatóanyag: megtudhatja, hogyan hozhat létre Azure Cosmos DB SQL API-erőforrásokat C# konzolos alkalmazás használatával.'
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: kirankk
ms.custom: devx-track-csharp
ms.openlocfilehash: 2ece9ed5cb6eb6ea706db1ed615a64d001f2b17c
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102423952"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Oktatóanyag: .NET-konzolos alkalmazás létrehozása Azure Cosmos DB SQL API-fiókban tárolt adatkezeléshez
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](./create-sql-api-java.md)
> * [Async Java](./create-sql-api-java.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Üdvözöljük a Azure Cosmos DB SQL API első lépések oktatóanyagában. Az oktatóanyag lépéseinek követésével egy olyan konzolalkalmazást készít, amely Azure Cosmos DB-erőforrásokat hoz létre és kérdez le.

Ez az oktatóanyag a [Azure Cosmos db .net SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)3,0-es vagy újabb verzióját használja. A [.NET-keretrendszer vagy a .net Core](https://dotnet.microsoft.com/download)használatával dolgozhat.

Ez az oktatóanyag az alábbiakkal foglalkozik:

> [!div class="checklist"]
>
> * Azure Cosmos-fiók létrehozása és csatlakoztatása
> * A projekt konfigurálása a Visual Studióban
> * Adatbázis és tároló létrehozása
> * Elemek hozzáadása a tárolóhoz
> * A tároló lekérdezése
> * Létrehozási, olvasási, frissítési és törlési (szifilisz) műveletek végrehajtása az elemen
> * Adatbázis törlése

Nincs elég ideje? Ne aggódjon! A teljes megoldás elérhető a [GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). A gyors utasításokért ugorjon a [teljes oktatóanyag-megoldás beszerzése szakaszra](#GetSolution) .

Most pedig lássunk neki!

## <a name="prerequisites"></a>Előfeltételek

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1. lépés: Azure Cosmos DB-fiók létrehozása

Hozzunk létre egy Azure Cosmos DB-fiókot. Ha már rendelkezik egy használni kívánt fiókkal, ugorja át ezt a szakaszt. A Azure Cosmos DB-emulátor használatához kövesse a [Azure Cosmos db emulatorban](local-emulator.md) leírt lépéseket az emulátor beállításához. Ezután ugorjon a [2. lépés: a Visual Studio-projekt beállítása szakaszra](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="step-2-set-up-your-visual-studio-project"></a><a id="SetupVS"></a>2. lépés: a Visual Studio-projekt beállítása

1. Nyissa meg a Visual studiót, és válassza **az új projekt létrehozása** lehetőséget.
1. A **create a New Project (új projekt létrehozása**) területen válassza a **konzol alkalmazás (.NET-keretrendszer)** lehetőséget a C# számára, majd kattintson a **tovább** gombra.
1. Nevezze el a projekt *CosmosGettingStartedTutorial*, majd válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="./media/sql-api-get-started/configure-cosmos-getting-started-2019.png" alt-text="A projekt konfigurálása":::

1. A **megoldáskezelő** kattintson a jobb gombbal az új Console-alkalmazásra, amely a Visual Studio-megoldás alatt található, majd válassza a **NuGet-csomagok kezelése** lehetőséget.
1. A **NuGet csomagkezelő eszközben** válassza a **Tallózás** lehetőséget, és keresse meg a *Microsoft. Azure. Cosmos* elemet. Válassza a **Microsoft. Azure. Cosmos** lehetőséget, és válassza a **telepítés** lehetőséget.

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png" alt-text="A NuGet telepítése Azure Cosmos DB ügyfél-SDK-hoz":::

   Az Azure Cosmos DB SQL API ügyfélkódtárának csomagazonosítója a következő: [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

Nagyszerű! Most, hogy befejeztük a beállítást, lássunk neki a kód megírásának! Az oktatóanyag befejezett projektjét lásd: .NET- [konzol alkalmazás fejlesztése Azure Cosmos db használatával](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>3. lépés: Csatlakozás egy Azure Cosmos DB-fiókhoz

1. Cserélje le a C#-alkalmazás elején található hivatkozásokat a *program.cs* fájlban a következő hivatkozásokkal:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Adja hozzá ezeket az állandókat és változókat a `Program` osztályhoz.

    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

   > [!NOTE]
   > Ha már ismeri a .NET SDK korábbi verzióját, akkor előfordulhat, hogy ismeri a feltételek *gyűjteményét* és a *dokumentumot*. Mivel Azure Cosmos DB több API-modellt is támogat, a .NET SDK 3,0-es verziója az általános feltételek *tárolóját* és *elemét* használja. Egy *tároló* lehet gyűjtemény, gráf vagy tábla. Egy *elem* lehet dokumentum, Edge/csúcspont vagy sor, és a tartalom egy tárolón belül van. További információ: [adatbázisok, tárolók és elemek használata Azure Cosmos DBban](account-databases-containers-items.md).

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Keresse meg Azure Cosmos DB-fiókját, majd válassza a **kulcsok** lehetőséget.

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-portal-keys.png" alt-text="Azure Cosmos DB kulcsok beolvasása Azure Portal":::

1. A *program.cs*-ben cserélje le az `<your endpoint URL>` értéket az **URI** értékre. Cserélje le az `<your primary key>` értéket az **elsődleges kulcs** értékére.

1. A **Main** metódus alatt adjon hozzá egy új, **GetStartedDemoAsync** nevű aszinkron feladatot, amely új példányt hoz létre `CosmosClient` .

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

    A **GetStartedDemoAsync** -t olyan belépési pontként használjuk, amely Azure Cosmos db erőforrásokon működő metódusokat hív meg.

1. Adja hozzá a következő kódot a **GetStartedDemoAsync** aszinkron feladat a **Main** metódusból való futtatásához. A **Main** metódus észleli a kivételeket, és a konzolba írja azokat.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Az alkalmazás futtatásához nyomja le az F5 billentyűt.

    A konzolon megjelenik az üzenet: a **demó vége, a kilépéshez nyomja le bármelyik billentyűt.** Ez az üzenet megerősíti, hogy az alkalmazás kapcsolódott a Azure Cosmos DBhoz. Ezután bezárhatja a konzolablakot.

Gratulálunk! Sikeresen csatlakozott egy Azure Cosmos DB-fiókhoz.

## <a name="step-4-create-a-database"></a>4. lépés: Adatbázis létrehozása

Az adatbázis a tárolók között particionált elemek logikai tárolója. A `CreateDatabaseIfNotExistsAsync` `CreateDatabaseAsync` [CosmosClient](/dotnet/api/microsoft.azure.cosmos.cosmosclient) osztály vagy metódusa létrehozhat egy adatbázist.

1. Másolja és illessze be a metódust a `CreateDatabaseAsync` `GetStartedDemoAsync` metódus alá.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync` egy olyan új adatbázist hoz létre, amely AZONOSÍTÓval `FamilyDatabase` rendelkezik, ha még nem létezik, és amely a mezőben megadott azonosítóval rendelkezik `databaseId` .

1. Másolja és illessze be az alábbi kódot, ahol létrehozza a CosmosClient az imént hozzáadott **ból** metódus meghívásához.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    A *program.cs* így kell kinéznie, a végpont és az elsődleges kulcs kitöltésével.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStartedTutorial
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

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
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

1. Az alkalmazás futtatásához nyomja le az F5 billentyűt.

   > [!NOTE]
   > Ha "503 szolgáltatás nem érhető el" hibaüzenetet kap, lehetséges, hogy a tűzfal blokkolja a szükséges [portokat](sql-sdk-connection-modes.md#service-port-ranges) a közvetlen csatlakozási módhoz. A probléma megoldásához nyissa meg a szükséges portokat, vagy használja az átjáró módú kapcsolatot az alábbi kódban látható módon:
   ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
   ```

Gratulálunk! Sikeresen létrehozott egy Azure Cosmos-adatbázist.  

## <a name="step-5-create-a-container"></a><a id="CreateColl"></a>5. lépés: tároló létrehozása

> [!WARNING]
> A metódus `CreateContainerIfNotExistsAsync` egy új tárolót hoz létre, amely díjszabási vonzatokkal rendelkezik. További részletekért látogasson el az [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

A tárolót a osztály [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) vagy [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) metódusával lehet létrehozni `CosmosDatabase` . A tároló elemekből áll (JSON-dokumentumok, ha az SQL API) és a kapcsolódó kiszolgálóoldali alkalmazás-logikát a JavaScriptben, például tárolt eljárásokat, felhasználó által definiált függvényeket és eseményindítókat.

1. Másolja és illessze be a metódust a `CreateContainerAsync` `CreateDatabaseAsync` metódus alá. `CreateContainerAsync`  létrehoz egy új tárolót az AZONOSÍTÓval `FamilyContainer` , ha még nem létezik, a `containerId` tulajdonság által particionált mező alapján megadott azonosító használatával `LastName` .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Másolja és illessze be az alábbi kódot, ahová létrehozta a CosmosClient az imént hozzáadott **CreateContainer** metódus meghívásához.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

1. Az alkalmazás futtatásához nyomja le az F5 billentyűt.

Gratulálunk! Sikeresen létrehozott egy Azure Cosmos-tárolót.  

## <a name="step-6-add-items-to-the-container"></a><a id="CreateDoc"></a>6. lépés: elemek hozzáadása a tárolóhoz

Az osztály [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) metódusa `CosmosContainer` létrehozhat egy elemeket. Az SQL API használatakor az elemek dokumentumokként vannak kiképezve, amelyek felhasználó által definiált tetszőleges JSON-tartalomnak minősülnek. Most már beszúrhat egy elemeket az Azure Cosmos-tárolóba.

Először hozzon létre egy `Family` osztályt, amely a minta Azure Cosmos db belül tárolt objektumokat jelöli. A alkalmazásban a, a, az `Parent` `Child` `Pet` `Address` alosztályokat is létrehozjuk `Family` . Az objektumnak a `Id` JSON-ban szerializált tulajdonsággal kell rendelkeznie `id` .

1. Válassza a CTRL + SHIFT + A billentyűkombinációt az **új elem hozzáadása** lehetőség megnyitásához. Adjon hozzá egy új osztályt `Family.cs` a projekthez.

    :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png" alt-text="Képernyőkép új Family.cs osztály projekthez való hozzáadásáról":::

1. Másolja és illessze be a,,, `Family` `Parent` `Child` `Pet` és `Address` osztályt a alkalmazásba `Family.cs` .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]


1. A *program.cs* a metódus után adja hozzá a `AddItemsToContainerAsync` metódust `CreateContainerAsync` .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]


    A kód ellenőrzi, hogy az azonos AZONOSÍTÓJÚ elemek már nem léteznek-e. Két elemet szúrunk be, egyet az *Andersen családhoz* és a *Wakefield családhoz*.

1. Adjon hozzá egy hívást a `AddItemsToContainerAsync` `GetStartedDemoAsync` metódushoz.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

1. Az alkalmazás futtatásához nyomja le az F5 billentyűt.

Gratulálunk! Sikeresen létrehozott két Azure Cosmos-elemet.  

## <a name="step-7-query-azure-cosmos-db-resources"></a><a id="Query"></a>7. lépés: Az Azure Cosmos DB-erőforrások lekérdezése

Az Azure Cosmos DB támogatja az egyes tárolókban tárolt JSON-dokumentumokon végzett részletes lekérdezéseket. További információ: [az SQL-lekérdezések első lépései](./sql-query-getting-started.md). Az alábbi mintakód bemutatja, hogyan futtathat lekérdezést az előző lépésben beszúrt elemekhez.

1. Másolja és illessze be a `QueryItemsAsync` metódust a `AddItemsToContainerAsync` metódus után.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Adjon hozzá egy hívást a ``QueryItemsAsync`` ``GetStartedDemoAsync`` metódushoz.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

1. Az alkalmazás futtatásához nyomja le az F5 billentyűt.

Gratulálunk! Sikeresen lekérdezte az Azure Cosmos-tárolót.

## <a name="step-8-replace-a-json-item"></a><a id="ReplaceItem"></a>8. lépés: JSON-elemek cseréje

Most frissíteni fogjuk a Azure Cosmos DB egy elemét. A következő tulajdonságot fogjuk módosítani: a `IsRegistered` `Family` és az `Grade` egyik gyermek.

1. Másolja és illessze be a `ReplaceFamilyItemAsync` metódust a `QueryItemsAsync` metódus után.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Adjon hozzá egy hívást a `ReplaceFamilyItemAsync` `GetStartedDemoAsync` metódushoz.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

1. Az alkalmazás futtatásához nyomja le az F5 billentyűt.

Gratulálunk! Sikeresen lecserélte az Azure Cosmos-elemeket.

## <a name="step-9-delete-item"></a><a id="DeleteDocument"></a>9. lépés: az elemek törlése

Most töröljük a Azure Cosmos DB lévő elemeket.

1. Másolja és illessze be a `DeleteFamilyItemAsync` metódust a `ReplaceFamilyItemAsync` metódus után.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Adjon hozzá egy hívást a `DeleteFamilyItemAsync` `GetStartedDemoAsync` metódushoz.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

1. Az alkalmazás futtatásához nyomja le az F5 billentyűt.

Gratulálunk! Sikeresen törölte az Azure Cosmos-elemeket.

## <a name="step-10-delete-the-database"></a><a id="DeleteDatabase"></a>10. lépés: Az adatbázis törlése

Most töröljük az adatbázist. A létrehozott adatbázis törlésével az adatbázis és az összes gyermek erőforrás is törlődik. Az erőforrások közé tartoznak a tárolók, elemek, valamint a tárolt eljárások, a felhasználó által definiált függvények és az eseményindítók. A példányt is eldobjuk `CosmosClient` .

1. Másolja és illessze be a `DeleteDatabaseAndCleanupAsync` metódust a `DeleteFamilyItemAsync` metódus után.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Adjon hozzá egy hívást a ``DeleteDatabaseAndCleanupAsync`` ``GetStartedDemoAsync`` metódushoz.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. Az alkalmazás futtatásához nyomja le az F5 billentyűt.

Gratulálunk! Sikeresen törölt egy Azure Cosmos-adatbázist.

## <a name="step-11-run-your-c-console-application-all-together"></a><a id="Run"></a>11. lépés: Futtassa a teljes C# konzolalkalmazást!

Az alkalmazás hibakeresési módban való létrehozásához és futtatásához válassza az F5 billentyűt a Visual Studióban.

A teljes alkalmazás kimenetét egy konzol ablakban kell megtekinteni. A kimenet a hozzáadott lekérdezések eredményeit jeleníti meg. Meg kell egyeznie az alábbi példában szereplő szöveggel.

```cmd
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

Gratulálunk! Elvégezte az oktatóanyagot, és egy működőképes C# konzolalkalmazással rendelkezik!

## <a name="get-the-complete-tutorial-solution"></a><a id="GetSolution"></a> Az oktatóanyagban szereplő teljes megoldás beszerzése

Ha nincs ideje az oktatóanyag lépéseinek elvégzésére, vagy csak le szeretné tölteni a kód mintáit, letöltheti azt.

A megoldás létrehozásához `GetStarted` a következő előfeltételek szükségesek:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Egy [Azure Cosmos db-fiók][cosmos-db-create-account].
* A GitHubon elérhető [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) megoldás.

Ha vissza szeretné állítani a Azure Cosmos DB .NET SDK-ra mutató hivatkozásokat a Visual Studióban, kattintson a jobb gombbal a megoldásra **megoldáskezelő**, majd válassza a **NuGet-csomagok visszaállítása** lehetőséget. Ezután a *App.config* fájlban frissítse a `EndPointUri` és az értékeket a `PrimaryKey` [3. lépés: Kapcsolódás egy Azure Cosmos db-fiókhoz című témakörben](#Connect)leírtak szerint.

Ennyi az egész, hogy létrejöjjön, és Ön így van.

## <a name="next-steps"></a>Következő lépések

* Összetettebb ASP.NET MVC-oktatóanyagot szeretne? Lásd [: oktatóanyag: ASP.net Core MVC-webalkalmazás fejlesztése a Azure Cosmos db a .net SDK használatával](sql-api-dotnet-application.md).
* Szeretné elvégezni a méretezést és a teljesítmény tesztelését Azure Cosmos DB? Lásd: [teljesítmény-és méretezési tesztek a Azure Cosmos db](performance-testing.md).
* A Azure Cosmos DB kérelmek, a használat és a tárolás figyelésével kapcsolatos további információkért lásd: [a teljesítmény-és tárolási mérőszámok figyelése a Azure Cosmos DBban](./monitor-cosmos-db.md).
* Ha lekérdezéseket szeretne futtatni a minta adatkészleten, tekintse meg a [lekérdezési demókörnyezet](https://www.documentdb.com/sql/demo).
* További információ az Azure Cosmos DB-ről: [Üdvözli az Azure Cosmos DB](./introduction.md).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account