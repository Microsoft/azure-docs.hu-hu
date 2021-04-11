---
title: 'Azure Cosmos DB: Teendőkezelő alkalmazás fejlesztése Xamarin használatával'
description: A cikk egy Xamarin-kódmintát mutat be, amellyel csatlakozhat egy Cosmos DB-adatbázishoz, és lekérdezéseket hajthat végre az adatbázisra vonatkozóan.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/09/2020
ms.author: anfeldma
ms.custom: devx-track-csharp
ms.openlocfilehash: f02a0673a3fae33f935ae9a9cbf94f101b25c39f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449783"
---
# <a name="quickstart-build-a-todo-app-with-xamarin-using-azure-cosmos-db-sql-api-account"></a>Gyors útmutató: Azure Cosmos DB SQL API-fiókkal rendelkező Xamarin-alkalmazás létrehozása a használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket.

> [!NOTE]
> A GitHubon [itt](https://github.com/xamarinhq/app-geocontacts) megtalálható egy teljes Canonical Xamarin-mintaalkalmazás mintakódja, amely több Azure-ajánlatot is bemutat (például a CosmosDB-t). Ez az alkalmazás földrajzilag elosztott kapcsolattartók megtekintését mutatja be, és lehetővé teszi e kapcsolattartók számára, hogy frissítsék a tartózkodási helyüket.

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Cosmos DB SQL API-fiókot, dokumentum-adatbázist és tárolót az Azure Portal segítségével. Ezután létrehozhatja és üzembe helyezheti az [SQL .NET API](sql-api-sdk-dotnet.md) -ra és a [Xamarin. Forms](/xamarin/) és a [MVVM építészeti minta](/xamarin/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm)felhasználásával létrehozott [Xamarin](/xamarin/) Mobile-alkalmazást.

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-todo-screen.png" alt-text="iOS rendszeren futó teendőkezelő Xamarin-alkalmazás":::

## <a name="prerequisites"></a>Előfeltételek

Ha Windows rendszeren fejleszt, és még nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition verziót](https://www.visualstudio.com/downloads/). Ügyeljen arra, hogy engedélyezze az **Azure-fejlesztési** és a **mobilalkalmazások .NET rendszerrel való fejlesztése** számítási feladatot a Visual Studio telepítése során.

Mac gépek esetében letöltheti az **ingyenes** [Visual Studio for Mac](https://www.visualstudio.com/vs/mac/) alkalmazást.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Tároló hozzáadása

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most hozzon klónozott Xamarin SQL API-alkalmazást a GitHubról, tekintse át a kódot, szerezze be az API-kulcsokat, és futtassa. Látni fogja, mennyire egyszerű programozott módon dolgozni az adatokkal.

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű mappát, majd zárja be a parancssort.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-xamarin-getting-started.git
    ```

4. A Visual Studióban nyissa meg a **C:\git-samples\azure-Cosmos-db-SQL-xamarin-Getting-started\src\ToDoItems.SLN** 

## <a name="obtain-your-api-keys"></a>Az API-kulcsok beszerzése

Lépjen vissza az Azure Portalra az API-kulccsal kapcsolatos adatokért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](https://portal.azure.com/) az Azure Cosmos DB SQL API-fiókban a bal oldali navigációs sávon kattintson a **Kulcsok** elemre, majd kattintson az **Írható és olvasható kulcsok** lehetőségre. A következő lépésben a képernyő jobb oldalán lévő másolási gombokkal másolhatja az URI-t és az elsődleges kulcsot az APIKeys.cs fájlba.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/keys.png" alt-text="Hozzáférési kulcs megtekintése és másolása az Azure Portal Kulcsok paneljén":::

2. A Visual Studióban nyissa meg a **ToDoItems. Core/Helpers/APIKeys. cs**.

3. Az Azure Portalon a másolás gombbal másolja az **URI** értéket, és tegye a változó értékét a `CosmosEndpointUrl` APIKeys. cs fájlba.

    ```csharp
    //#error Enter the URL of your Azure Cosmos DB endpoint here
    public static readonly string CosmosEndpointUrl = "[URI Copied from Azure Portal]";
    ```

4. Az Azure Portalon a másolás gombbal másolja az **elsődleges kulcs** értékét, és tegye a értékét a `Cosmos Auth Key` APIKeys. cs.

    ```csharp
    //#error Enter the read/write authentication key of your Azure Cosmos DB endpoint here
    public static readonly string CosmosAuthKey = "[PRIMARY KEY copied from Azure Portal";
    ```

[!INCLUDE [cosmos-db-auth-key-info](../../includes/cosmos-db-auth-key-info.md)]

## <a name="review-the-code"></a>A kód áttekintése

Ez a megoldás bemutatja, hogy hogyan hozhat létre egy teendőkezelő alkalmazást az Azure Cosmos DB SQL API és a Xamarin.Forms segítségével. Az alkalmazás két lappal rendelkezik, az első lap a még el nem végzett teendőket megjelenítő listanézetet tartalmazza. A második lap a már elvégzett teendőket jeleníti meg. Az első lapon található, még el nem végzett teendők megtekintése mellett új teendőket is hozzáadhat, szerkesztheti a meglévőket, és befejezettként jelölheti meg a lista tételeit.

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/android-todo-screen.png" alt-text="Másolja át a json-adatokat, és kattintson a Mentés gombra az Adatkezelőben az Azure-portálon":::

A ToDoItems megoldásban található kód az alábbiakat tartalmazza:

* **ToDoItems. Core**
   * Ez egy olyan .NET Standard-projekt, amely egy Xamarin. Forms projektet és egy közös alkalmazás-logikai kódot tart fenn, amely Azure Cosmos DBon belüli teendőket tart fenn.
* **ToDoItems. Android**
  * Ez a projekt tartalmazza az Android-alkalmazást.
* **ToDoItems. iOS**
  * Ez a projekt tartalmazza az iOS-alkalmazást.

Most tekintsük át röviden, hogyan kommunikál az alkalmazás az Azure Cosmos DB-vel.

* A [Microsoft.Azure.DocumentDb.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/) NuGet-csomagot minden projekthez hozzá kell adni.
* A `ToDoItem` **ToDoItems. Core/models** mappában található osztály a fent létrehozott **Items** -tárolóban lévő dokumentumokat modellezi. Vegye figyelembe, hogy a tulajdonságok elnevezése különbséget tesz a kis- és nagybetűk között.
* A `CosmosDBService` **ToDoItems. Core/Services** mappában lévő osztály a Azure Cosmos db felé irányuló kommunikációt foglalja magában.
* A `CosmosDBService` osztályban egy `DocumentClient` típusú változó található. A (z `DocumentClient` ) a Azure Cosmos db fiókra vonatkozó kérelmek konfigurálásához és végrehajtásához használatos, és a példánya a következő:

    ```csharp
    docClient = new DocumentClient(new Uri(APIKeys.CosmosEndpointUrl), APIKeys.CosmosAuthKey);
    ```

* A dokumentumok tárolójának lekérdezése során a `DocumentClient.CreateDocumentQuery<T>` rendszer a metódust használja, ahogy az itt látható a `CosmosDBService.GetToDoItems` függvényben:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=GetToDoItems)] 

    A fogad `CreateDocumentQuery<T>` egy URI-t, amely az előző szakaszban létrehozott tárolóra mutat. Emellett LINQ-operátorokat is megadhat, például egy `Where` záradékot. Ebben az esetben a rendszer csak a nem elvégzett teendőket adja vissza.

    A `CreateDocumentQuery<T>` függvény végrehajtása szinkron módon történik, és egy `IQueryable<T>` objektumot ad vissza. Az `AsDocumentQuery` metódus azonban a `IQueryable<T>` objektumot `IDocumentQuery<T>` objektummá alakítja át, amely aszinkron módon hajtható végre. Így nem blokkolja a mobilalkalmazások felhasználói felületi szálját.

    Az `IDocumentQuery<T>.ExecuteNextAsync<T>` függvény lekéri az eredmények oldalát az Azure Cosmos DB-ből, amely a `HasMoreResults` használatával ellenőrzi, hogy vannak-e még visszaadandó eredmények.

> [!TIP]
> Az Azure Cosmos-tárolók és-dokumentumok által működtetett függvények számos funkciója egy URI-t fogad el paraméterként, amely meghatározza a tároló vagy a dokumentum címeit. Ezen URI létrehozása az `URIFactory` osztállyal történik. Az adatbázisokhoz, tárolóhoz és dokumentumokhoz tartozó URI-k mindegyike létrehozható ezzel az osztállyal.

* A `ComsmosDBService.InsertToDoItem` függvény bemutatja, hogyan szúrhat be új dokumentumot:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=InsertToDoItem)] 

    Az Item URI meg van adva, valamint a beszúrandó elemet is.

* A `CosmosDBService.UpdateToDoItem` függvény bemutatja, hogyan cserélhet le egy meglévő dokumentumot egy újat:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=UpdateToDoItem)] 

    Itt új URI-t kell megadnia a lecserélni kívánt dokumentum egyedi azonosításához, amelyet `UriFactory.CreateDocumentUri` a rendszer az adatbázis és a tárolók nevének és a dokumentum azonosítójának átadásával kapott.

    A `DocumentClient.ReplaceDocumentAsync` a paraméterként meghatározott dokumentumra cseréli le az URI által azonosított dokumentumot.

* Az elemek törlését a függvény mutatja be `CosmosDBService.DeleteToDoItem` :

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=DeleteToDoItem)] 

    Ismét jegyezze fel, hogy a rendszer létrehozza és átadja a függvénynek az egyedi dokumentum URI-JÁT `DocumentClient.DeleteDocumentAsync` .

## <a name="run-the-app"></a>Az alkalmazás futtatása

Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges.

A következő lépések mutatják be, hogyan futtathatja az alkalmazást a Visual Studio for Mac hibakeresőjével.

> [!NOTE]
> Az Android verziójú alkalmazás használata pontosan ugyanilyen, az alábbi lépésekben kiemelünk minden különbséget. Ha Windows rendszeren szeretne hibakeresést végezni a Visual Studióval, ennek dokumentációját [itt találja az iOS](/xamarin/ios/deploy-test/debugging-in-xamarin-ios?tabs=vswin), illetve [itt találja az Android rendszerre](/xamarin/android/deploy-test/debugging/) vonatkozóan.

1. Először válassza ki a kívánt platformot. Ehhez kattintson a kiemelt legördülő listára, és válassza a ToDoItems.iOS elemet az iOS, vagy a ToDoItems.Android elemet az Android esetén.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-select-platform.png" alt-text="A hibakeresés céljaként használni kívánt platform kiválasztása a Visual Studio for Mac alkalmazásban":::

2. Az alkalmazás hibakeresésének elkezdéséhez nyomja le a Command+Enter billentyűkombinációt vagy kattintson a Lejátszás gombra.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-start-debug.png" alt-text="Hibakeresés elkezdése a Visual Studio for Mac alkalmazásban":::

3. Az iOS-szimulátor vagy az Android-emulátor elindítása után az alkalmazás 2 lapot jelenít meg a képernyő alján az iOS, illetve a képernyő tetején az Android esetén. Az első lapon az el nem végzett teendők láthatók, a másodikon pedig a befejezettek.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-droid-started.png" alt-text="A teendőkezelő alkalmazás indítóképernyője":::

4. Az iOS rendszeren egy teendő elvégzéséhez csúsztassa a teendőt balra > koppintson a **Complete** (Befejezés) gombra. Az Android rendszeren egy teendő elvégzéséhez nyomja le hosszan a teendőt > majd koppintson a Complete (Befejezés) gombra.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-complete.png" alt-text="Teendő elvégzése":::

5. Teendő szerkesztéséhez > koppintson az elemre > megjelenik egy új képernyő, ahol új értékeket adhat meg. A Save (Mentés) gombra koppintva menti a módosításokat az Azure Cosmos DB-ben.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-edit.png" alt-text="Teendő szerkesztése":::

6. Teendő hozzáadásához > koppintson az **Add** (Hozzáadás) gombra a kezdőképernyő jobb felső sarkában > megjelenik egy új, üres szerkesztési lap.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-add.png" alt-text="Teendő hozzáadása":::

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos-fiókot, hogyan hozhat létre tárolót a Adatkezelő használatával, és hogyan építhet ki és helyezhet üzembe egy Xamarin-alkalmazást. Mostantól további adatait is importálhatja az Azure Cosmos-fiókjába.

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)
