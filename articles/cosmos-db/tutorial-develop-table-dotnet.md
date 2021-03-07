---
title: Azure Cosmos DB Table API .NET Standard SDK használatával
description: Megtudhatja, hogyan tárolhatja és kérdezheti le a strukturált információkat Azure Cosmos DB Table API-fiókban
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: c641e24a498a6263d6a7c2325eed099b75a82caa
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426433"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Bevezetés az Azure Cosmos DB Table API és az Azure Table Storage a .NET SDK-val való használatába
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

A Azure Cosmos DB Table API vagy az Azure Table Storage használatával strukturált NoSQL-adatmennyiségeket tárolhat a felhőben, így egy kulcs-/attribútum-tárolót biztosít a séma kisebb kialakításával. Mivel Azure Cosmos DB Table API és a Table Storage kevesebb séma, egyszerűen alkalmazkodhat az adataihoz az alkalmazás igényeinek megfelelően. Az Azure Cosmos DB Table API vagy a Table Storage használatával rugalmas adatkészleteket tárolhat, például a webalkalmazások felhasználói adatait, a címjegyzékeket, az eszköz adatait vagy a szolgáltatás által igényelt más típusú metaadatokat. 

Ez az oktatóanyag egy példát mutat be, amely bemutatja, hogyan használható a [.net-hez készült Microsoft Azure Cosmos db Table Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) a Azure Cosmos db Table API és az Azure Table Storage forgatókönyvekkel. Az Azure-szolgáltatáshoz tartozó kapcsolódást kell használnia. Ezeket a forgatókönyveket olyan C#-példákkal vizsgáljuk, amelyek bemutatják, hogyan lehet táblákat létrehozni, adatok beszúrására/frissítésére, adatok lekérdezésére és a táblák törlésére használni.

## <a name="prerequisites"></a>Előfeltételek

A minta sikeres teljesítéséhez a következőkre lesz szüksége:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure CosmosDB a .net-hez](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) – ez a tár jelenleg a .NET Standard és a .NET-keretrendszer számára érhető el. 

* [Azure Cosmos DB Table API fiók](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API-fiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>.NET-konzol projekt létrehozása

Hozzon létre egy új .NET-konzol alkalmazást a Visual Studióban. A következő lépések bemutatják, hogyan hozhat létre egy Console-alkalmazást a Visual Studio 2019-ben. A Azure Cosmos DB Table Library bármilyen típusú .NET-alkalmazásban használható, beleértve az Azure Cloud Service-t vagy a webalkalmazást, valamint az asztali és mobil alkalmazásokat is. Ebben az útmutatóban az egyszerűség kedvéért egy konzolalkalmazást használunk.

1. Válassza a **fájl**  >  **új**  >  **projekt** lehetőséget.

1. Válassza a **Console app (.net Core)** lehetőséget, majd kattintson a **tovább** gombra.

1. A **projekt neve** mezőben adja meg az alkalmazás nevét, például **CosmosTableSamples**. (Szükség szerint más nevet is megadhat.)

1. Válassza a **Létrehozás** lehetőséget.

Az ebben a mintában szereplő összes példa felvehető a konzol alkalmazás **program.cs** fájljának Main () metódusára.

## <a name="install-the-required-nuget-package"></a>A szükséges NuGet-csomag telepítése

A NuGet csomag beszerzéséhez kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a projektjére a **Megoldáskezelőben**, és válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget.

1. Keresse meg a, a, a [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder) és a **telepítés** lehetőséget a Microsoft Azure Cosmos db Table Library telepítéséhez.

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

1. A [Azure Portal](https://portal.azure.com/)navigáljon az Azure Cosmos-fiókjához vagy a Table Storage-fiókhoz. 

1. Nyissa meg a **kapcsolati karakterlánc** vagy a **hozzáférési kulcsok** panelt. Az ablak jobb oldalán található Másolás gomb használatával másolja a vágólapra a **PRIMARY CONNECTION STRING** (Elsődleges kapcsolati sztring) értékét.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="Tekintse meg és másolja a vágólapra a PRIMARY CONNECTION STRING (Elsődleges kapcsolati sztring) értékét a Kapcsolati sztring ablaktáblán.":::
   
1. A kapcsolódási karakterlánc konfigurálásához a Visual studióból kattintson a jobb gombbal a projekt **CosmosTableSamples**.

1. Válassza a **Hozzáadás** , majd az **új elem** lehetőséget. Hozzon létre egy új fájlt, **Settings.jsa** fájltípust **írógéppel JSON konfigurációs** fájlként. 

1. Cserélje le a Settings.jsfájl kódját a következő kódra, és rendelje hozzá az elsődleges kapcsolatok karakterláncát:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Kattintson a jobb gombbal a projekt **CosmosTableSamples**. Válassza a **Hozzáadás**, **új elem elemet** , és adjon hozzá egy **appSettings.cs** nevű osztályt.

1. Adja hozzá a következő kódot a AppSettings.cs fájlhoz. Ez a fájl beolvassa a Settings.jsfájlból a kapcsolódási karakterláncot, és hozzárendeli azt a konfigurációs paraméterhez:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/AppSettings.cs":::

## <a name="parse-and-validate-the-connection-details"></a>A kapcsolat részleteinek elemzése és ellenőrzése

1. Kattintson a jobb gombbal a projekt **CosmosTableSamples**. Válassza a **Hozzáadás**, **új elem elemet** , és adjon hozzá egy **Common.cs** nevű osztályt. A kapcsolat részleteinek érvényesítéséhez és az ebben az osztályban található tábla létrehozásához kódot kell írnia.

1. Definiáljon egy metódust `CreateStorageAccountFromConnectionString` az alább látható módon. Ez a metódus elemzi a kapcsolati sztring részleteit, és ellenőrzi, hogy érvényes-e a fiók neve és a fiók kulcsa a "Settings.json" fájlban.

   :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="createStorageAccount":::

## <a name="create-a-table"></a>Tábla létrehozása 

A [CloudTableClient](/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) osztály segítségével lekérheti a Table Storage-ban tárolt táblákat és entitásokat. Mivel nem található táblázat a Cosmos DB Table API-fiókban, vegyük fel a `CreateTableAsync` metódust a **Common.cs** osztályba egy tábla létrehozásához:

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="CreateTable":::

Ha "503 szolgáltatás nem érhető el" hibaüzenet jelenik meg, lehetséges, hogy a kapcsolódási mód szükséges portjait tűzfal blokkolja. A probléma megoldásához nyissa meg a szükséges portokat, vagy használja az átjáró módú kapcsolatot az alábbi kódban látható módon:

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>Az entitás definiálása 

Az entitások C#-objektumokhoz rendelnek egy egyéni, a [TableEntity](/dotnet/api/microsoft.azure.cosmos.table.tableentity)származtatott osztály használatával. Ha hozzá szeretne adni egy entitást egy táblához, hozzon létre egy osztályt, amely meghatározza az entitás tulajdonságait.

Kattintson a jobb gombbal a projekt **CosmosTableSamples**. Válassza a **Hozzáadás**, **új mappa** lehetőséget, és nevezze el **modellként**. A Model mappában adjon hozzá egy **CustomerEntity.cs** nevű osztályt, és adja hozzá a következő kódot.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Model/CustomerEntity.cs":::

Ez a kód olyan Entity osztályt határoz meg, amely az ügyfél utónevét használja, és a vezetéknevet adja meg a partíció kulcsaként. Egy adott entitás partíció- és sorkulcsa együttesen azonosítja az entitást a táblában. Az ugyanazzal a partíciós kulccsal rendelkező entitások gyorsabban lekérdezhető, mint a különböző partíciós kulcsokkal rendelkező entitások, de a különféle partíciós kulcsok használata lehetővé teszi a párhuzamos műveletek nagyobb méretezhetőségét. A táblákban tárolni kívánt entitásoknak támogatott típusúnak, például a [TableEntity](/dotnet/api/microsoft.azure.cosmos.table.tableentity) osztályból származtatottnak kell lenniük. A táblában tárolni kívánt entitástulajdonságoknak publikusnak kell lenniük, és támogatniuk kell az értékek beolvasását és beállítását is. Az entitástípusnak emellett elérhetővé kell tennie egy paraméter nélküli konstruktort is.

## <a name="insert-or-merge-an-entity"></a>Entitás beszúrása vagy egyesítése

A következő mintakód létrehoz egy entitás objektumot, és hozzáadja azt a táblához. Az entitások beszúrásához vagy egyesítéséhez a [TableOperation](/dotnet/api/microsoft.azure.cosmos.table.tableoperation) osztályon belüli InsertOrMerge metódus használható. A művelet végrehajtásához a [CloudTable.ExecuteAsync](/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync) metódust kell meghívni. 

Kattintson a jobb gombbal a projekt **CosmosTableSamples**. Válassza a **Hozzáadás**, **új elem elemet** , és adjon hozzá egy **SamplesUtils.cs** nevű osztályt. Ez az osztály tárolja az entitásokon a SZIFILISZi műveletek végrehajtásához szükséges összes kódot. 

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="InsertItem":::

## <a name="get-an-entity-from-a-partition"></a>Entitás beolvasása egy partícióból

Az entitásokat a [TableOperation](/dotnet/api/microsoft.azure.cosmos.table.tableoperation) osztály lekérési metódusának használatával szerezheti be a partícióból. A következő kódrészlet a partíciós kulcs sorát, e-mail-címét és telefonszámát kéri le. Ez a példa az entitás lekérdezéséhez felhasznált kérelmek egységeit is kiírja. Az entitások lekérdezéséhez fűzze hozzá a következő kódot a **SamplesUtils.cs** fájlhoz:

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

## <a name="delete-an-entity"></a>Entitás törlése

A lekérdezés után egyszerűen törölheti az entitásokat az entitások frissítésénél bemutatott minta alapján. Az alábbi kód lekérdez, majd töröl egy ügyfélentitást. Entitás törléséhez fűzze hozzá a következő kódot a **SamplesUtils.cs** fájlhoz: 

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="DeleteItem":::

## <a name="execute-the-crud-operations-on-sample-data"></a>A szifilisz-műveletek végrehajtása a mintaadatok alapján

Miután meghatározta a tábla létrehozásához, az entitások beszúrásához vagy egyesítéséhez szükséges metódusokat, futtassa ezeket a metódusokat a mintaadatok alapján. Ehhez kattintson a jobb gombbal a projekt **CosmosTableSamples**. Válassza a **Hozzáadás**, **új elem** lehetőséget, és vegyen fel egy **BasicSamples.cs** nevű osztályt, és adja hozzá a következő kódot. Ez a kód létrehoz egy táblát, entitásokat hoz létre hozzá.

Ha nem szeretné törölni az entitást és a táblát a projekt végén, jegyezze fel a `await table.DeleteIfExistsAsync()` és `SamplesUtils.DeleteEntityAsync(table, customer)` metódusokat a következő kódból. Érdemes kipróbálni ezeket a metódusokat, és érvényesíteni az adatellenőrzést a tábla törlése előtt.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/BasicSamples.cs":::

Az előző kód létrehoz egy táblázatot, amely a "demo" kezdetű, a generált GUID pedig a táblázat nevéhez lesz hozzáfűzve. Ezután hozzáadja a "hárfa Walter" nevű ügyfél-entitást és a vezetéknevét, és később frissíti a felhasználó telefonszámát. 

Ebben az oktatóanyagban a Table API fiókban tárolt adatok alapszintű szifilisz-műveleteinek elvégzésére szolgáló kódot készített. Olyan speciális műveleteket is végrehajthat, mint például a – Batch adatok beszúrása, a partíción belüli összes adat lekérdezése, a partíción belüli adatok lekérdezése, a fiók azon tábláinak felsorolása, amelyek neve a megadott előtaggal kezdődik. Letöltheti a teljes minta űrlapot az [Azure-Cosmos-Table-DotNet-Core-Getting-Started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) GitHub-tárházban. A [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/main/CosmosTableSamples/AdvancedSamples.cs) osztály több műveletet is végrehajthat az adatokon.  

## <a name="run-the-project"></a>A projekt futtatása

A projekt **CosmosTableSamples**. Nyissa meg a **program.cs** nevű osztályt, és adja hozzá a következő kódot a BasicSamples a projekt futtatásakor történő meghívásához.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Program.cs":::

Most hozza létre a megoldást, és nyomja le az F5 billentyűt a projekt futtatásához. A projekt futtatásakor a következő kimenet jelenik meg a parancssorban:

:::image type="content" source="./media/tutorial-develop-table-standard/output-from-sample.png" alt-text="Kimenet a parancssorból":::

Ha olyan hibaüzenetet kap, amely szerint a Settings.jsnem található a projekt futtatásakor, akkor a következő XML-bejegyzés a projekt beállításaihoz való hozzáadásával oldható meg. Kattintson a jobb gombbal a CosmosTableSamples elemre, válassza a CosmosTableSamples. csproj szerkesztése lehetőséget, és adja hozzá a következő itemGroup: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Most jelentkezzen be a Azure Portalba, és ellenőrizze, hogy az adatkészletek szerepelnek-e a táblában. 

:::image type="content" source="./media/tutorial-develop-table-standard/results-in-portal.png" alt-text="Eredmények a portálon":::

## <a name="next-steps"></a>Következő lépések

Most folytassa a következő oktatóanyaggal, és megtudhatja, hogyan telepítheti át az információkat Azure Cosmos DB Table API-fiókba. 

> [!div class="nextstepaction"]
>[Az adatáttelepítés az Azure Comsos DB-be Table API](../cosmos-db/table-import.md)