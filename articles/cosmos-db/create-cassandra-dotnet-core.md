---
title: 'Gyors útmutató: Cassandra API a .NET Core-Azure Cosmos DB'
description: Ez a rövid útmutató bemutatja, hogyan használható a Azure Cosmos DB Cassandra API egy profil-alkalmazás létrehozásához a Azure Portal és a .NET Core használatával
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
author: TheovanKraay
ms.author: thvankra
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/01/2020
ms.custom: devx-track-dotnet
ms.openlocfilehash: f228386d8cf0e708080b7f6c5f6cef7258b2eafb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93099980"
---
# <a name="quickstart-build-a-cassandra-app-with-net-core-and-azure-cosmos-db"></a>Gyors útmutató: Cassandra-alkalmazás létrehozása a .NET Core és a Azure Cosmos DB használatával
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Ez a rövid útmutató bemutatja, hogyan használható a .NET Core és a Azure Cosmos DB [Cassandra API](cassandra-introduction.md) egy profil-alkalmazás létrehozásához a githubról származó példa klónozásával. Ez a rövid útmutató emellett azt is bemutatja, hogyan hozható létre egy Azure Cosmos DB-fiók a webes alapú Azure Portalon.

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, tábla, kulcs-érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Alternatív lehetőségként [kipróbálhatja ingyenesen az Azure Cosmos DB-t](https://azure.microsoft.com/try/cosmosdb/) Azure-előfizetés, díjfizetés és elköteleződés nélkül.

Emellett szüksége lesz a következőkre: 
* Ha még nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition verziót](https://www.visualstudio.com/downloads/). Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.
* Telepítse a [Git szoftvert](https://www.git-scm.com/) a példa klónozásához.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]


## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig váltsunk át kódok használatára. A következő lépésekben elvégezheti a Cassandra API-alkalmazás klónozását a GitHubról, beállíthatja a kapcsolati sztringet, és futtathatja az alkalmazást. Látni fogja, mennyire egyszerű programozott módon dolgozni az adatokkal. 

1. Nyisson meg egy parancssort. Hozzon létre egy `git-samples` nevű mappát. Ezután zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-dotnet-core-getting-started.git
    ```

4. Ezután nyissa meg a CassandraQuickStartSample megoldásfájlt a Visual Studióban. 

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan hozza létre a kód az adatbázis erőforrásait, tekintse át a következő kódrészleteket. A kódrészletek mind a `Program.cs` metódusban található fájlból származnak `async Task ProcessAsync()` , amelyet a `C:\git-samples\azure-cosmos-db-cassandra-dotnet-core-getting-started\CassandraQuickStart` mappába telepítenek. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra.

* Egy Cassandra fürtvégponthoz való csatlakozással inicializálja a munkamenetet. Az Azure Cosmos DB-n lévő Cassandra API csak a TLS 1.2-es verziót támogatja. 

  ```csharp
      var options = new Cassandra.SSLOptions(SslProtocols.Tls12, true, ValidateServerCertificate);
      options.SetHostNameResolver((ipAddress) => CASSANDRACONTACTPOINT);
      Cluster cluster = Cluster
          .Builder()
          .WithCredentials(USERNAME, PASSWORD)
          .WithPort(CASSANDRAPORT)
          .AddContactPoint(CASSANDRACONTACTPOINT)
          .WithSSL(options)
          .Build()
      ;
      ISession session = await cluster.ConnectAsync();
   ```

* Meglévő szóköz eldobása, ha már létezik.

    ```csharp
    await session.ExecuteAsync(new SimpleStatement("DROP KEYSPACE IF EXISTS uprofile")); 
    ```

* Új kulcsterület létrehozása.

    ```csharp
    await session.ExecuteAsync(new SimpleStatement("CREATE KEYSPACE uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 };"));
    ```

* Új tábla létrehozása.

   ```csharp
  await session.ExecuteAsync(new SimpleStatement("CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"));
   ```

* Felhasználói entitások beszúrása az IMapper objektum használatával a uprofile kulcsterülethez csatlakozó új munkamenettel.

    ```csharp
    await mapper.InsertAsync<User>(new User(1, "LyubovK", "Dubai"));
    ```

* Összes felhasználói adat lekérdezése.

    ```csharp
    foreach (User user in await mapper.FetchAsync<User>("Select * from user"))
    {
        Console.WriteLine(user);
    }
    ```

* Egyetlen felhasználó adatainak lekérdezése.

    ```csharp
    mapper.FirstOrDefault<User>("Select * from user where user_id = ?", 3);
    ```

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba. A kapcsolati sztring adatai lehetővé teszik az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal.

1. Az [Azure Portalon](https://portal.azure.com/) válassza a **Kapcsolati sztring** lehetőséget.

1. A :::image type="icon" source="./media/create-cassandra-dotnet/copy.png"::: Felhasználónév értékének másolásához használja a képernyő jobb oldalán található gombot.

   :::image type="content" source="./media/create-cassandra-dotnet/keys.png" alt-text="Hozzáférési kulcs megtekintése és másolása az Azure Portal Kapcsolati sztring oldalán":::

1. A Visual Studióban nyissa meg a program. cs fájlt. 

1. Illessze be a USERNAME értéket a Portalból a `<PROVIDE>` helyére a 13. sorban.

    A Program.cs 13. sorának ekkor a következőhöz hasonlónak kell kinéznie: 

    `private const string UserName = "cosmos-db-quickstart";`

    Ugyanezt az értéket is beillesztheti a `<PROVIDE>` 15. sorban a kapcsolattartási pont értékéhez:

    `private const string CassandraContactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com"; //  DnsName`

1. Lépjen vissza a Portalba, és másolja a PASSWORD értéket. Illessze be a PASSWORD értéket a Portalból a 14. sorban található `<PROVIDE>` érték helyére.

    A Program.cs 14. sorának ekkor a következőhöz hasonlóan kell kinéznie: 

    `private const string Password = "2Ggkr662ifxz2Mg...==";`

1. Lépjen vissza a Portalba, és másolja a CONTACT POINT értéket. Illessze be a CONTACT POINT értéket a portálról a `<PROVIDE>` 16. sorban.

    A program. cs 16. sora ekkor a következőhöz hasonlóan néz ki: 

    `private const string CASSANDRACONTACTPOINT = "quickstart-cassandra-api.cassandra.cosmos.azure.com";`

1. Mentse a Program.cs fájlt.
    
## <a name="run-the-net-core-app"></a>A .NET Core-alkalmazás futtatása

1. A Visual Studióban válassza az **eszközök**  >  **NuGet Package** Manager  >  **csomagkezelő konzolt**.

2. A parancssorban a következő paranccsal telepítse a .NET illesztő NuGet-csomagját. 

    ```cmd
    Install-Package CassandraCSharpDriver
    ```
3. Az alkalmazás futtatásához nyomja le a CTRL + F5 billentyűkombinációt. Az alkalmazás megjelenik a konzolablakban. 

    :::image type="content" source="./media/create-cassandra-dotnet/output.png" alt-text="A kimenet megtekintése és ellenőrzése":::

    Nyomja le a CTRL + C billentyűkombinációt a program futásának megszakításához, és zárja be a konzolablakot. 
    
4. Ha megnyitja az **Adatkezelőt** az Azure Portalon, lekérdezheti és módosíthatja és használhatja az új adatokat.

    :::image type="content" source="./media/create-cassandra-dotnet/data-explorer.png" alt-text="Adatok megtekintése az Adatkezelőben":::

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével tárolót készíteni, és hogyan lehet futtatni egy webalkalmazást. Most további adatokat importálhat a Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [Cassandra-adatok importálása az Azure Cosmos DB-be](cassandra-import-data.md)
