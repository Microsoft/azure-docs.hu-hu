---
title: 'Azure Cosmos DB: Teendőkezelő alkalmazás fejlesztése Xamarin használatával'
description: A cikk egy Xamarin-kódmintát mutat be, amellyel csatlakozhat egy Cosmos DB-adatbázishoz, és lekérdezéseket hajthat végre az adatbázisra vonatkozóan.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/07/2021
ms.author: anfeldma
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a940f4bb519332e147577e4a9172406c401d152
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365739"
---
# <a name="quickstart-build-a-todo-app-with-xamarin-using-azure-cosmos-db-sql-api-account"></a>Rövid útmutató: Todo app létrehozása a Xamarin használatával Azure Cosmos DB SQL API-fiók használatával
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

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket.

> [!NOTE]
> A GitHubon [itt](https://github.com/xamarinhq/app-geocontacts) megtalálható egy teljes Canonical Xamarin-mintaalkalmazás mintakódja, amely több Azure-ajánlatot is bemutat (például a CosmosDB-t). Ez az alkalmazás földrajzilag elosztott kapcsolattartók megtekintését mutatja be, és lehetővé teszi e kapcsolattartók számára, hogy frissítsék a tartózkodási helyüket.

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Cosmos DB SQL API-fiókot, dokumentum-adatbázist és tárolót az Azure Portal segítségével. Ezután egy, az [SQL .NET API-n](sql-api-sdk-dotnet.md) és a [Xamarinon](/xamarin/) készült, a [Xamarin.Forms](/xamarin/) és az [MVVM](/xamarin/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm)architekturális minta használatával készült todolist mobilalkalmazást fog felépíteni és üzembe helyezni.

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-todo-screen.png" alt-text="iOS rendszeren futó teendőkezelő Xamarin-alkalmazás":::

## <a name="prerequisites"></a>Előfeltételek

Ha Windows rendszeren fejleszt, és még nincs telepítve Visual Studio 2019, letöltheti  és használhatja az [ingyenes Visual Studio 2019 Community Edition kiadását.](https://www.visualstudio.com/downloads/) Ügyeljen arra, hogy engedélyezze az **Azure-fejlesztési** és a **mobilalkalmazások .NET rendszerrel való fejlesztése** számítási feladatot a Visual Studio telepítése során.

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

Most pedig klónozzuk a Xamarin SQL API-alkalmazást a GitHubról, áttekintjük a kódot, lekérhetők az API-kulcsok, és futtatjuk azt. Látni fogja, mennyire egyszerű programozott módon dolgozni az adatokkal.

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

4. A Visual Studio nyissa meg a **következőt: C:\git-samples\azure-cosmos-db-sql-xamarin-getting-started\src\ToDoItems.sln** 

## <a name="obtain-your-api-keys"></a>Az API-kulcsok beszerzése

Lépjen vissza az Azure Portalra az API-kulccsal kapcsolatos adatokért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](https://portal.azure.com/) az Azure Cosmos DB SQL API-fiókban a bal oldali navigációs sávon kattintson a **Kulcsok** elemre, majd kattintson az **Írható és olvasható kulcsok** lehetőségre. A következő lépésben a képernyő jobb oldalán lévő másolási gombokkal másolhatja az URI-t és az elsődleges kulcsot az APIKeys.cs fájlba.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/keys.png" alt-text="Hozzáférési kulcs megtekintése és másolása az Azure Portal Kulcsok paneljén":::

2. A Visual Studio nyissa meg a **ToDoItems.Core/Helpers/APIKeys.cs et.**

3. A Azure Portal másolás gombbal másolja az **URI** értéket, és azt az `CosmosEndpointUrl` APIKeys.cs változó értékeként.

    ```csharp
    //#error Enter the URL of your Azure Cosmos DB endpoint here
    public static readonly string CosmosEndpointUrl = "[URI Copied from Azure portal]";
    ```

4. A Azure Portal másolás gombbal másolja ki az **ELSŐDLEGES KULCS** értékét, és azt az `Cosmos Auth Key` APIKeys.cs fájlban található értékként.

    ```csharp
    //#error Enter the read/write authentication key of your Azure Cosmos DB endpoint here
    public static readonly string CosmosAuthKey = "[PRIMARY KEY copied from Azure portal";
    ```

[!INCLUDE [cosmos-db-auth-key-info](../../includes/cosmos-db-auth-key-info.md)]

## <a name="review-the-code"></a>A kód áttekintése

Ez a megoldás bemutatja, hogy hogyan hozhat létre egy teendőkezelő alkalmazást az Azure Cosmos DB SQL API és a Xamarin.Forms segítségével. Az alkalmazás két lappal rendelkezik, az első lap a még el nem végzett teendőket megjelenítő listanézetet tartalmazza. A második lap a már elvégzett teendőket jeleníti meg. Az első lapon található, még el nem végzett teendők megtekintése mellett új teendőket is hozzáadhat, szerkesztheti a meglévőket, és befejezettként jelölheti meg a lista tételeit.

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/android-todo-screen.png" alt-text="Másolja át a json-adatokat, és kattintson a Mentés gombra az Adatkezelőben az Azure-portálon":::

A ToDoItems megoldásban található kód az alábbiakat tartalmazza:

* **ToDoItems.Core**
   * Ez egy .NET Standard-projekt, amely egy Xamarin.Forms-projektet és egy megosztott alkalmazáslogika-kódot tartalmaz, amely a fájlokban lévő todo Azure Cosmos DB.
* **ToDoItems.Android**
  * Ez a projekt tartalmazza az Android-alkalmazást.
* **ToDoItems.iOS**
  * Ez a projekt tartalmazza az iOS-alkalmazást.

Most tekintsük át röviden, hogyan kommunikál az alkalmazás az Azure Cosmos DB-vel.

* A [Microsoft.Azure.DocumentDb.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/) NuGet-csomagot minden projekthez hozzá kell adni.
* A `ToDoItem` **ToDoItems.Core/Models** mappában lévő osztály a fent létrehozott **Items** tárolóban lévő dokumentumokat modellzi. Vegye figyelembe, hogy a tulajdonságok elnevezése különbséget tesz a kis- és nagybetűk között.
* A `CosmosDBService` **ToDoItems.Core/Services** mappában lévő osztály a következővel való kommunikációt Azure Cosmos DB.
* A `CosmosDBService` osztályban egy `DocumentClient` típusú változó található. A a fiókra vonatkozó kérések konfigurálásán és `DocumentClient` végrehajtásán Azure Cosmos DB, és példányosul:

    ```csharp
    docClient = new DocumentClient(new Uri(APIKeys.CosmosEndpointUrl), APIKeys.CosmosAuthKey);
    ```

* A dokumentumok tárolóinak lekérdezésekor a `DocumentClient.CreateDocumentQuery<T>` metódust használjuk, ahogyan az itt látható a `CosmosDBService.GetToDoItems` függvényben:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=GetToDoItems)] 

    A az előző szakaszban létrehozott tárolóra `CreateDocumentQuery<T>` mutató URI-t vesz fel. Emellett LINQ-operátorokat is megadhat, például egy `Where` záradékot. Ebben az esetben a rendszer csak a nem elvégzett teendőket adja vissza.

    A `CreateDocumentQuery<T>` függvény végrehajtása szinkron módon történik, és egy `IQueryable<T>` objektumot ad vissza. A metódus azonban a objektumot objektumká `AsDocumentQuery` `IQueryable<T>` `IDocumentQuery<T>` konvertálja, amely aszinkron módon hajtható végre. Így nem blokkolja a mobilalkalmazások felhasználói felületi szálját.

    A függvény lekéri az eredmények lapját a Azure Cosmos DB, amely megvizsgálja, hogy a rendszer további eredményeket ad-e `IDocumentQuery<T>.ExecuteNextAsync<T>` `HasMoreResults` vissza.

> [!TIP]
> Az Azure Cosmos-tárolókon és -dokumentumokon működő számos függvény paraméterként egy URI-t fog használni, amely meghatározza a tároló vagy a dokumentum címét. Ezen URI létrehozása az `URIFactory` osztállyal történik. Ezzel az osztálysal adatbázisok, tárolók és dokumentumok URI-ját lehet létrehozni.

* A `ComsmosDBService.InsertToDoItem` függvény bemutatja, hogyan szúrható be egy új dokumentum:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=InsertToDoItem)] 

    Az elem URI-ját és a beszúrni kívánt elemet is meg kell adni.

* A függvény bemutatja, hogyan cserélhető le egy meglévő dokumentum `CosmosDBService.UpdateToDoItem` egy újra:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=UpdateToDoItem)] 

    Itt egy új URI-t kell használni a lecserélni szükséges dokumentum egyedi azonosításához, amelyet a használatával lehet beszerezni, majd át kell helyezni neki az adatbázis és a tároló nevét, valamint a dokumentum `UriFactory.CreateDocumentUri` azonosítóját.

    A `DocumentClient.ReplaceDocumentAsync` a paraméterként meghatározott dokumentumra cseréli le az URI által azonosított dokumentumot.

* Az elem törlését a függvény mutatja `CosmosDBService.DeleteToDoItem` be:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=DeleteToDoItem)] 

    Figyelje meg ismét a létrehozott és a függvénynek átadott egyedi dokumentum `DocumentClient.DeleteDocumentAsync` URI-ját.

## <a name="run-the-app"></a>Az alkalmazás futtatása

Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges.

A következő lépések mutatják be, hogyan futtathatja az alkalmazást a Visual Studio for Mac hibakeresőjével.

> [!NOTE]
> Az Android verziójú alkalmazás használata pontosan ugyanilyen, az alábbi lépésekben kiemelünk minden különbséget. Ha Windows rendszeren szeretne hibakeresést végezni a Visual Studióval, ennek dokumentációját [itt találja az iOS](/xamarin/ios/deploy-test/debugging-in-xamarin-ios?tabs=vswin), illetve [itt találja az Android rendszerre](/xamarin/android/deploy-test/debugging/) vonatkozóan.

1. Először válassza ki a kívánt platformot. Ehhez kattintson a kiemelt legördülő listára, és válassza a ToDoItems.iOS elemet az iOS, vagy a ToDoItems.Android elemet az Android esetén.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-select-platform.png" alt-text="A hibakeresés céljaként használni kívánt platform kiválasztása a Visual Studio for Mac alkalmazásban":::

2. Az alkalmazás hibakeresésének elkezdéséhez nyomja le a Command+Enter billentyűkombinációt vagy kattintson a Lejátszás gombra.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-start-debug.png" alt-text="Hibakeresés elkezdése a Visual Studio for Mac alkalmazásban":::

3. Amikor az iOS-szimulátor vagy az Android-emulátor indítása befejeződik, az alkalmazás két lapot jelenít meg az iOS-hez készült képernyő alján, illetve a képernyő tetején Android rendszeren. Az első a nem befejezett todo (todo) elemeket mutatja, a második pedig a befejezetteket.

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

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure Cosmos-fiókot, hogyan hozhat létre tárolót a Adatkezelő használatával, és hogyan hozhat létre és helyezhet üzembe egy Xamarin-alkalmazást. Most már további adatokat importálhat az Azure Cosmos-fiókjába.

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)
