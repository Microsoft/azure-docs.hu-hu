---
title: Xamarin-alkalmazás létrehozása a .NET-tel és Azure Cosmos DB API-val MongoDB
description: A MongoDB API-jával való kapcsolódáshoz és Azure Cosmos DB lekérdezéshez használható Xamarin-mintakód
author: codemillmatt
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/09/2020
ms.author: masoucou
ms.custom: devx-track-csharp
ms.openlocfilehash: 75db62b4f8a5c6512ca5fc84d149513fe81f6019
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658231"
---
# <a name="quickstart-build-a-xamarinforms-app-with-net-sdk-and-azure-cosmos-dbs-api-for-mongodb"></a>Gyors útmutató: Xamarin. Forms-alkalmazás létrehozása .NET SDK-val és Azure Cosmos DB API-val a MongoDB-hoz
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket.

Ez a rövid útmutató bemutatja, hogyan hozhat létre [Azure Cosmos db API-val konfigurált Cosmos-fiókot a MongoDB](mongodb-introduction.md), a dokumentum-adatbázishoz és a gyűjteményhez a Azure Portal használatával. Ezután létrehoz egy Todo-alkalmazást Xamarin. Forms alkalmazást a [MongoDB .net-illesztőprogram](https://docs.mongodb.com/ecosystem/drivers/csharp/)használatával.

## <a name="prerequisites-to-run-the-sample-app"></a>A mintaalkalmazás futtatásának előfeltételei

A mintaalkalmazás futtatásához szüksége lesz a [Visual Studio](https://www.visualstudio.com/downloads/) vagy a [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) alkalmazásra és egy érvényes Azure CosmosDB-fiókra.

Ha még nem rendelkezik a Visual Studióval, töltse le a [Visual studio 2019 Community Editiont](https://www.visualstudio.com/downloads/) a **Mobile Development** használatával a telepítővel telepített .net-munkaterheléssel.

Ha Mac gépet használ, a [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) alkalmazást töltse le, és futtassa a telepítést.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

A cikkben leírt minta kompatibilis a MongoDB.Driver 2.6.1-es verziójával.

## <a name="clone-the-sample-app"></a>A mintaalkalmazás klónozása

Először töltse le a minta alkalmazást a GitHubról. Ez implementál egy teendőkezelő alkalmazást a MongoDB dokumentumtároló modelljével.



# <a name="windows"></a>[Windows](#tab/windows)

1. A Windows rendszeren nyisson meg egy parancssort, vagy nyissa meg a terminált, és hozzon létre egy git-Samples nevű új mappát, majd a ablak bezárásához.

    ```batch
    md "C:\git-samples"
    ```

    ```bash
    mkdir '$home\git-samples\
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started.git
    ```

Ha nem szeretné a gitet használni, [a projektet ZIP-fájlként is letöltheti](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started/archive/master.zip).

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra.

A következő kódrészletek mind a `MongoService` osztályból származnak, a következő elérési úton található: src/tasklist. Core/Services/MongoService. cs.

* A Mongo Client inicializálása.
    ```cs
    MongoClientSettings settings = MongoClientSettings.FromUrl(new MongoUrl(APIKeys.ConnectionString));

    settings.SslSettings = new SslSettings() { EnabledSslProtocols = SslProtocols.Tls12 };

    settings.RetryWrites = false;

    MongoClient mongoClient = new MongoClient(settings);
    ```

* Kérje le az adatbázisra és a gyűjteményre mutató hivatkozást. A MongoDB .NET SDK automatikusan létrehozza az adatbázist és a gyűjteményt is, ha még nem léteznek.
    ```cs
    string dbName = "MyTasks";
    string collectionName = "TaskList";

    var db = mongoClient.GetDatabase(dbName);

    var collectionSettings = new MongoCollectionSettings 
    {
        ReadPreference = ReadPreference.Nearest
    };

    tasksCollection = db.GetCollection<MyTask>(collectionName, collectionSettings);
    ```
* Kérje le az összes dokumentumot listaként.
    ```cs
    var allTasks = await TasksCollection
                    .Find(new BsonDocument())
                    .ToListAsync();
    ```

* Kérdezzen rá adott dokumentumokra.
    ```cs
    public async Task<List<MyTask>> GetIncompleteTasksDueBefore(DateTime date)
    {
        var tasks = await TasksCollection
                        .AsQueryable()
                        .Where(t => t.Complete == false)
                        .Where(t => t.DueDate < date)
                        .ToListAsync();

        return tasks;
    }
    ```

* Hozzon létre egy feladatot, és szúrja be a gyűjteménybe.
    ```cs
    public async Task CreateTask(MyTask task)
    {
        await TasksCollection.InsertOneAsync(task);
    }
    ```

* Feladat frissítése egy gyűjteményben.
    ```cs
    public async Task UpdateTask(MyTask task)
    {
        await TasksCollection.ReplaceOneAsync(t => t.Id.Equals(task.Id), task);
    }
    ```

* Feladat törlése egy gyűjteményből.
    ```cs
    public async Task DeleteTask(MyTask task)
    {
        await TasksCollection.DeleteOneAsync(t => t.Id.Equals(task.Id));
    }
    ```

<a id="update-your-connection-string"></a>

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](https://portal.azure.com/) az Azure Cosmos DB-fiókban a bal oldalsávon kattintson a **Kapcsolati sztring** elemre, majd kattintson az **írási/olvasási kulcsok** lehetőségre. A következő lépések során a képernyő jobb oldalán található másolási gombok használatával másolhatja a vágólapra az elsődleges kapcsolati sztringet.

2. Nyissa meg a **TaskList.Core** projekt **Helpers** könyvtárában lévő **APIKeys.cs** fájlt.

3. Másolja ki az **elsődleges kapcsolati sztring** értekét a portálról (a másolási gombbal), és ezt adja meg a **ConnectionString** mező értékeként az **APIKeys.cs** fájlban.

4. Eltávolítás `&replicaSet=globaldb` a kapcsolatok sztringből. Futásidejű hibaüzenetet kap, ha nem távolítja el ezt az értéket a lekérdezési karakterláncból.

> [!IMPORTANT]
> A futásidejű hiba elkerülése érdekében el kell távolítania a `&replicaSet=globaldb` kulcs/érték párokat a kapcsolódási karakterlánc lekérdezési karakterláncában.

Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges.

## <a name="run-the-app"></a>Az alkalmazás futtatása

### <a name="visual-studio-2019"></a>Visual Studio 2019

1. A Visual Studióban kattintson a jobb gombbal az egyes projektekre a **Megoldáskezelőben**, majd kattintson a **NuGet-csomagok kezelése** elemre.
2. Kattintson **az összes NuGet-csomagot visszaállító** lehetőségre.
3. Kattintson a jobb gombbal a **TaskList.Android** elemre, és válassza a **Beállítás kezdőprojektként** lehetőséget.
4. Az alkalmazás hibakereséséhez nyomja le az F5 billentyűt.
5. Ha iOS-rendszeren szeretné futtatni, a készüléket először csatlakoztatnia kell egy Machez (ehhez itt talál [útmutatást](/xamarin/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio)).
6. Kattintson a jobb gombbal a **TaskList.iOS** elemre, és válassza a **Beállítás kezdőprojektként** lehetőséget.
7. Az alkalmazás hibakereséséhez nyomja le az F5 billentyűt.

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. A platform legördülő listájából válassza a TaskList.iOS vagy a TaskList.Android elemet attól függően, hogy melyik platformot szeretné használni.
2. Az alkalmazás hibakereséséhez nyomja le a cmd+Enter billentyűkombinációt.

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, és Xamarin.Forms-alkalmazást futtatni a MongoDB API-jával. Most további adatokat importálhat a Cosmos DB-fiókba.

> [!div class="nextstepaction"]
> [Adatimportálás a Azure Cosmos DB Azure Cosmos DB API-jával konfigurált MongoDB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)