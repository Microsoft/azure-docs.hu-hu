---
title: Az Azure Cosmos DB SQL API-fiókba való tömeges importálás a .NET SDK használatával
description: Megtudhatja, hogyan importálhat és betöltheti az adatok Azure Cosmos DB egy olyan .NET-konzolos alkalmazás létrehozásával, amely optimalizálja az adatok importálásához szükséges kiépített átviteli sebességet (RU/s).
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 03/15/2021
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 1c178f57a31e02b3dac712a5425db226720200c5
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563623"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>Az Azure Cosmos DB SQL API-fiókba való tömeges importálás a .NET SDK használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez az oktatóanyag bemutatja, hogyan hozhat létre olyan .NET-konzolos alkalmazást, amely optimalizálja az adatok Azure Cosmos DBba való importálásához szükséges kiépített átviteli sebességet (RU/s). Ebben a cikkben az adatok egy mintául szolgáló adatforrásból kerülnek beolvasásra, majd egy Azure Cosmos-tárolóba importáljuk.
Ez az oktatóanyag a Azure Cosmos DB .NET SDK 3.0-s [verzióját](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) használja, amely a .NET-keretrendszerre vagy a .net Core-ra is megcélozható.

Ez az oktatóanyag az alábbiakkal foglalkozik:

> [!div class="checklist"]
> * Azure Cosmos-fiók létrehozása
> * A projekt konfigurálása
> * Csatlakozás egy tömeges támogatást engedélyező Azure Cosmos-fiókhoz
> * Adatimportálás végrehajtása párhuzamos létrehozási műveletekkel

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő utasítások követése előtt győződjön meg arról, hogy rendelkezik a következő erőforrásokkal:

* Aktív Azure-fiók. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Net Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). A futtatásával ellenőrizheti, hogy melyik verzió érhető el a környezetben `dotnet --version` .

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1. lépés: Azure Cosmos DB-fiók létrehozása

[Hozzon létre egy Azure Cosmos db SQL API-fiókot](create-cosmosdb-resources-portal.md) a Azure Portal, vagy hozza létre a fiókot a [Azure Cosmos db Emulator](local-emulator.md)használatával.

## <a name="step-2-set-up-your-net-project"></a>2. lépés: a .NET-projekt beállítása

Nyissa meg a Windows-parancssort vagy egy terminál-ablakot a helyi számítógépről. A következő szakaszban lévő összes parancsot a parancssorból vagy a terminálból fogja futtatni. Futtassa a következő DotNet új parancsot egy olyan új alkalmazás létrehozásához, amelynek a neve *tömeges importálás – bemutató*. A `--langVersion` paraméter beállítja a *LangVersion* tulajdonságot a létrehozott projektfájl számára.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

A Build várt kimenetének a következőhöz hasonlóan kell kinéznie:

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>3. lépés: a Azure Cosmos DB csomag hozzáadása

Miközben továbbra is az alkalmazás könyvtárában található, telepítse a .NET Core-hoz készült Azure Cosmos DB ügyféloldali kódtárat a DotNet-csomag hozzáadása paranccsal.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>4. lépés: az Azure Cosmos-fiók hitelesítő adatainak beszerzése

A minta alkalmazásnak hitelesítenie kell magát az Azure Cosmos-fiókban. A hitelesítéshez át kell adni az Azure Cosmos-fiók hitelesítő adatait az alkalmazásnak. Az Azure Cosmos-fiók hitelesítő adatait a következő lépésekkel szerezheti be:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1.  Navigáljon az Azure Cosmos-fiókjához.
1.  Nyissa meg a **kulcsok** ablaktáblát, és másolja a fiókjának **URI-JÁT** és **elsődleges kulcsát** .

Ha a Azure Cosmos DB emulátort használja, szerezze be az [emulátor hitelesítő adatait ebből a cikkből](local-emulator.md#authenticate-requests).

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>5. lépés: a CosmosClient objektum inicializálása tömeges végrehajtással támogatással

Nyissa meg a generált `Program.cs` fájlt egy Kódszerkesztőben. A CosmosClient egy új példányát kell létrehoznia, amely lehetővé teszi, hogy a tömeges végrehajtás engedélyezve legyen, és a Azure Cosmos DBon végzett műveletek végrehajtásához használja azt. 

Kezdjük azzal, hogy felülírja az alapértelmezett `Main` metódust, és definiálja a globális változókat. Ezek a globális változók tartalmazzák a végponti és az engedélyezési kulcsokat, az adatbázis nevét, a létrehozandó tárolót, valamint a tömegesen beszúrni kívánt elemek számát. Ügyeljen rá, hogy a környezete alapján cserélje le a endpointURL és az engedélyezési kulcs értékeit. 


   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Diagnostics;
   using System.IO;
   using System.Text.Json;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;

   public class Program
   {
        private const string EndpointUrl = "https://<your-account>.documents.azure.com:443/";
        private const string AuthorizationKey = "<your-account-key>";
        private const string DatabaseName = "bulk-tutorial";
        private const string ContainerName = "items";
        private const int AmountToInsert = 300000;

        static async Task Main(string[] args)
        {

        }
   }
   ```

A `Main` metóduson belül adja hozzá a következő kódot a CosmosClient objektum inicializálásához:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

A tömeges végrehajtás engedélyezése után a CosmosClient belsőleg csoportosítja egyidejű műveleteket egyetlen szolgáltatási hívásban. Így optimalizálja az átviteli sebesség kihasználtságát a szolgáltatási hívások partíciók közötti elosztásával, végül pedig az eredeti hívókhoz rendeli az eredményeket.

Ezután létrehozhat egy tárolót az összes elem tárolásához.  Adja meg `/pk` a partíciós kulcsot, a 50000 ru/s-t kiépített átviteli sebességként, valamint egy egyéni indexelési házirendet, amely kizárja az összes mezőt az írási sebesség optimalizálása érdekében. Adja hozzá a következő kódot a CosmosClient inicializálási utasítása után:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>6. lépés: az egyidejű feladatok listájának feltöltése

A tömeges végrehajtás támogatásának kihasználásához hozzon létre az aszinkron feladatok listáját az adatforrások és a végrehajtani kívánt műveletek alapján, és használja `Task.WhenAll` ezeket párhuzamosan.
Kezdjük a "hamis" adatok használatával az adatmodellből származó elemek listájának létrehozásához. Egy valós alkalmazásban az elemek a kívánt adatforrásból származnak.

Először adja hozzá a hamis csomagokat a megoldáshoz a DotNet-csomag hozzáadása parancs használatával.

   ```bash
   dotnet add package Bogus
   ```

Adja meg a menteni kívánt elemek definícióját. Meg kell határoznia az `Item` osztályt a `Program.cs` fájlon belül:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

Ezután hozzon létre egy segítő függvényt az `Program` osztályban. Ez a segítő funkció beolvassa a véletlenszerű adatok beszúrásához és létrehozásához megadott elemek számát:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

Használja a Helper függvényt a következőhöz használható dokumentumok listájának inicializálásához:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

Ezután használja a dokumentumok listáját az egyidejű feladatok létrehozásához és a Feladatlista feltöltéséhez, hogy beszúrja az elemeket a tárolóba. A művelet végrehajtásához adja hozzá a következő kódot a `Program` osztályhoz:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

A rendszer az összes ilyen egyidejű műveletet együtt hajtja végre (ömlesztve), a bevezetés szakaszban leírtak szerint.

## <a name="step-7-run-the-sample"></a>7. lépés: a minta futtatása

A minta futtatásához egyszerűen a következő paranccsal teheti meg `dotnet` :

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>A teljes minta beolvasása

Ha nincs ideje az oktatóanyag lépéseinek végrehajtására, vagy csak szeretné letölteni a mintakódokat, a [GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer) beszerezhetőek.

A projekt klónozása után frissítse a szükséges hitelesítő adatokat a [program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/main/src/Program.cs#L25)-on belül.

A minta futtatásához váltson az adattár könyvtárába, és használja a következőt `dotnet` :

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Azure Cosmos-fiók létrehozása
> * A projekt konfigurálása
> * Csatlakozás egy tömeges támogatást engedélyező Azure Cosmos-fiókhoz
> * Adatimportálás végrehajtása párhuzamos létrehozási műveletekkel

Most folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
>[Azure Cosmos DB lekérdezése az SQL API használatával](tutorial-query-sql-api.md)