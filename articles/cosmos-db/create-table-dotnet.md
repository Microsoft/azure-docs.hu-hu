---
title: 'Gyors útmutató: Table API .NET-Azure Cosmos DB'
description: Ez a rövid útmutató ismerteti, hogyan használható az Azure Cosmos DB Table API alkalmazások létrehozására az Azure Portal és a .NET segítségével
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 513af71384fd1fa5d38ee3ec367a42892a662444
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096937"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>Gyors útmutató: Table API-alkalmazás létrehozása .NET SDK-val és Azure Cosmos DB 
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](./table-storage-how-to-use-python.md)
>  

Ez a rövid útmutató ismerteti, hogyan használható a .NET és az Azure Cosmos DB [Table API](table-introduction.md) egy alkalmazás létrehozására egy, a GitHubról származó példa klónozásával. Ez a gyors útmutató emellett azt is bemutatja, hogyan hozható létre egy Azure Cosmos DB-fiók, és hogyan használható az Adatkezelő táblák és entitások létrehozására a webes alapú Azure Portalon.

## <a name="prerequisites"></a>Előfeltételek

Ha még nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition verziót](https://www.visualstudio.com/downloads/). Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Tábla hozzáadása

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy Table-alkalmazást a GitHubról, beállítjuk a kapcsolati sztringet, majd futtatni fogjuk az alkalmazást. Látni fogja, mennyire egyszerű programozott módon dolgozni az adatokkal. 

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű mappát, majd zárja be a parancssort.

   ```bash
   md "C:\git-samples"
   ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

   ```bash
   cd "C:\git-samples"
   ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started.git
   ```

> [!TIP]
> A hasonló kódok részletesebb ismertetése: [Cosmos DB Table API minta](./tutorial-develop-table-dotnet.md) cikk.

## <a name="open-the-sample-application-in-visual-studio"></a>A mintaalkalmazás megnyitása a Visual Studióban

1. A Visual Studio **Fájl** menüjében válassza a **Megnyitás**, majd a **Projekt/Megoldás** elemet. 

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-open-solution.png" alt-text="A megoldás megnyitása"::: 

2. Navigáljon ahhoz a mappához, ahová klónozott a minta alkalmazást, és nyissa meg a TableStorage. SLN fájlt.

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Ellenkező esetben ugorjon [a dokumentum kapcsolódási karakterlánc szakaszának frissítéséhez](#update-your-connection-string) .

* A következő kód bemutatja, hogyan hozhat létre táblát az Azure Storage-ban:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="CreateTable":::

* Az alábbi kód bemutatja, hogyan szúrhat be adatbevitelt a táblába:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="InsertItem":::

* A következő kód bemutatja, hogyan lehet lekérdezni a tábla adatait:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

* A következő kód bemutatja, hogyan törölhet adatok a táblából:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="DeleteItem":::

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba. Ez lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal. 

1. Az [Azure Portalon](https://portal.azure.com/) kattintson a **Kapcsolati sztring** elemre. Az ablak jobb oldalán található Másolás gomb használatával másolja a vágólapra a **PRIMARY CONNECTION STRING** (Elsődleges kapcsolati sztring) értékét.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="Tekintse meg és másolja a vágólapra a PRIMARY CONNECTION STRING (Elsődleges kapcsolati sztring) értékét a Kapcsolati sztring ablaktáblán.":::

2. A Visual Studióban nyissa meg a **Settings.js** fájlt. 

3. Illessze be az **elsődleges kapcsolatok karakterláncát** a portálról a StorageConnectionString értékre. Illessze be a sztringet az idézőjelek közé.

   ```csharp
   {
      "StorageConnectionString": "<Primary connection string from Azure portal>"
   }
   ```

4. Nyomja le a CTRL + S billentyűkombinációt a **Settings.js** fájlba mentéséhez.

Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

## <a name="build-and-deploy-the-app"></a>Az alkalmazás létrehozása és üzembe helyezése

1. A Visual Studióban kattintson a jobb gombbal a **CosmosTableSamples** projektre **megoldáskezelő** , majd kattintson a **NuGet-csomagok kezelése** elemre. 

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-manage-nuget.png" alt-text="NuGet-csomagok kezelése":::

2. A NuGet **Tallózás** mezőjébe írja be a Microsoft. Azure. Cosmos. table parancsot. Ezzel a Cosmos DB Table API-ügyfélkódtárát keresi meg. Vegye figyelembe, hogy ez a tár jelenleg a .NET-keretrendszer és a .NET Standard számára érhető el. 
   
   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-nuget-browse.png" alt-text="A NuGet Tallózás lapja":::

3. A **Microsoft. Azure. Cosmos. table** függvénytár telepítéséhez kattintson a **telepítés** gombra. Ez telepíti az Azure Cosmos DB Table API csomagot és annak összes függőségét.

4. Ha a teljes alkalmazást futtatja, a rendszer beszúrja a táblázat entitásba, és a végén törli a mintákat, így a teljes minta futtatásakor nem jelenik meg az összes beszúrt oszlop. Azonban beszúrhat néhány töréspontot is az adatmegjelenítéshez. Nyissa meg a BasicSamples. cs fájlt, és kattintson a jobb gombbal az 52-es sorra, válassza a **töréspont**, majd a **töréspont beszúrása** lehetőséget. Szúrjon be egy másik töréspontot az 55. sorban.

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-breakpoint.png" alt-text="Töréspont hozzáadása"::: 

5. Az alkalmazás futtatásához nyomja le az F5 billentyűt. A konzol ablaka megjeleníti az új tábla adatbázisának nevét (ebben az esetben a demoa13b1) a Azure Cosmos DBban. 
    
   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-console.png" alt-text="Konzolkimenet":::

   Az első töréspont elérésekor lépjen vissza az Adatkezelőbe az Azure Portalon. Kattintson a **Frissítés** gombra, bontsa ki a demo* táblát, majd kattintson az **Entitások** elemre. A jobb oldali **Entitások** lapon látható a Walter Harp számára újonnan hozzáadott entitás. Láthatja, hogy az új entitáshoz tartozó telefonszám 425-555-0101.

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-entity.png" alt-text="Új entitás":::
    
   Ha olyan hibaüzenetet kap, amely szerint a Settings.jsnem található a projekt futtatásakor, akkor a következő XML-bejegyzés a projekt beállításaihoz való hozzáadásával oldható meg. Kattintson a jobb gombbal a CosmosTableSamples elemre, válassza a CosmosTableSamples. csproj szerkesztése lehetőséget, és adja hozzá a következő itemGroup: 

   ```csharp
     <ItemGroup>
       <None Update="Settings.json">
         <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
       </None>
     </ItemGroup>
   ```

6. Zárjunk be Adatkezelő **entitások** lapját.
    
7. Az F5 billentyű lenyomásával futtassa az alkalmazást a következő töréspontig. 

    A töréspont elérésekor váltson vissza az Azure Portalra, majd kattintson újból az **Entitások** elemre az **Entitások** lap megnyitásához, ahol láthatja, hogy a telefonszám a következőre frissült: 425-555-0105.

8. Az alkalmazás futtatásához nyomja le az F5 billentyűt. 
 
   Az alkalmazás által hozzáadott entitásokat egy fejlett mintaalkalmazás használja, amelyet a Table API jelenleg még nem támogat. Az alkalmazás ezután törli a mintaalkalmazás által létrehozott táblát.

9. A konzolablakban nyomja le az Enter billentyűt az alkalmazás futásának megszakításához. 
  

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével táblát készíteni, és hogyan lehet futtatni az alkalmazást.  Most már le tudja kérdezni adatait a Table API segítségével.  

> [!div class="nextstepaction"]
> [Táblaadatok importálása a Table API-ba](table-import.md)