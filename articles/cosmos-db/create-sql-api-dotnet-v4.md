---
title: SQL API Azure Cosmos DB erőforrások kezelése a .NET V4 SDK használatával
description: 'Rövid útmutató: Konzolalkalmazás létrehozása a .NET V4 SDK használatával Azure Cosmos DB SQL API-fiók erőforrásainak kezeléséhez.'
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/07/2021
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.openlocfilehash: 495191dfcdfd7a4d318bef508878e951d88b3ae6
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483877"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-preview-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Rövid útmutató: Konzolalkalmazás létrehozása a .NET V4 SDK (előzetes verzió) használatával az SQL API Azure Cosmos DB fiók erőforrásainak kezeléséhez.
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

A .NET-hez Azure Cosmos DB SQL API ügyféloldali kódtárának első lépések. A dokumentumban található lépéseket követve telepítse a .NET V4-es (Azure.Cosmos-) csomagot, készítsen alkalmazást, és próbálja ki az alapszintű CRUD-műveletekhez szükséges példakódot a Azure Cosmos DB.

> [!IMPORTANT]
> A .NET V4 SDK for Azure Cosmos DB jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB a Microsofté™ gyors NoSQL-adatbázisa, amely bármilyen mérethez nyílt API-kat igényel. A Azure Cosmos DB kulcs/érték, dokumentum és gráfadatbázisok gyors létrehozásához és lekérdezéséhez használhatja. Használja a Azure Cosmos DB SQL API .NET-hez ügyféloldali kódtárát a következőre:

* Azure Cosmos-adatbázis és -tároló létrehozása
* Mintaadatok hozzáadása a tárolóhoz
* Adatok lekérdezése 
* Az adatbázis törlése

[Kódtár forráskódja](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4)  |  [Package (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– hozzon létre](https://azure.microsoft.com/free/) egyet ingyenesen, vagy [kipróbálhatja](https://azure.microsoft.com/try/cosmosdb/) Azure Cosmos DB azure-előfizetés, ingyenes díj és kötelezettségvállalások nélkül. 
* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). A futtatásával ellenőrizheti, hogy melyik verzió érhető el a `dotnet --version` környezetben.

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti egy Azure Cosmos-fiók létrehozásának és egy olyan projekt beállításának folyamatán, amely a .NET-hez Azure Cosmos DB SQL API ügyféloldali kódtárát használja az erőforrások kezeléséhez. A cikkben leírt példakód létrehoz egy adatbázist és egy családtagot `FamilyDatabase` (minden családtag egy elem) az adatbázisban. Minden családtag rendelkezik olyan tulajdonságokkal, mint `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` a . A `LastName` tároló partíciókulcsa a tulajdonság. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Azure Cosmos-fiók létrehozása

Ha az Ingyenes [Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) lehetőséget használja egy Azure Cosmos-fiók létrehozásához, létre kell hoznia egy **SQL API** Azure Cosmos DB típusú fiókot. Már Azure Cosmos DB egy tesztfiókot. Nem kell explicit módon létrehoznia a fiókot, így kihagyhatja ezt a szakaszt, és továbbléphet a következő szakaszra.

Ha saját Azure-előfizetéssel rendelkezik, vagy ingyenes előfizetést hozott létre, explicit módon hozzon létre egy Azure Cosmos-fiókot. A következő kód létrehoz egy munkamenet-konzisztenciájú Azure Cosmos-fiókot. A rendszer replikálja a fiókot a és `South Central US` a `North Central US` rendszer.  

Az Azure Cosmos-Azure Cloud Shell használhatja a fiók létrehozásához. Az Azure Cloud Shell interaktív, hitelesített, böngészőben elérhető felület az Azure-erőforrások felügyeletéhez. Rugalmasságot nyújt a munkamódszeréhez leginkább illő héjfelület (akár a Bash, akár a PowerShell) kiválasztásának lehetőségével. Ebben a rövid útmutatóban válassza a **Bash módot.** Azure Cloud Shell tárfiókra is szüksége van, akkor létrehozhat egyet, amikor a rendszer erre kéri.

Válassza a **következő kód melletti** Próbálja ki gombot, válassza a **Bash** mód lehetőséget, válassza **a Tárfiók** létrehozása lehetőséget, és jelentkezzen be az Cloud Shell. Ezután másolja és illessze be a következő kódot Azure Cloud Shell és futtassa. Az Azure Cosmos-fiók nevének globálisan egyedinek kell lennie, ezért a parancs futtatása előtt frissítse `mysqlapicosmosdb` az értéket.

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

   ```bash
   dotnet new console â€“langVersion:8 -n todo
   ```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazásmappába. Az alkalmazást a következővel építheti fel:

   ```bash
   cd todo
   dotnet build
   ```

A build várt kimenetének a következő módon kell kinéznie:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Az Azure Cosmos DB csomag telepítése

Az alkalmazáskönyvtárban maradva telepítse a Azure Cosmos DB .NET Core-ügyféloldali kódtárát a dotnet add package paranccsal.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Másolja az Azure Cosmos-fiók hitelesítő adatait a Azure Portal

A mintaalkalmazásnak hitelesítenie kell magát az Azure Cosmos-fiókjában. A hitelesítéshez át kell adni az Azure Cosmos-fiók hitelesítő adatait az alkalmazásnak. Az Azure Cosmos-fiók hitelesítő adatait az alábbi lépésekkel lehet lekérte:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Lépjen az Azure Cosmos-fiókjához.

1. Nyissa meg **a Kulcsok panelt,** és másolja ki a fiókja **URI-ját** és **ELSŐDLEGES** KULCSát. A következő lépésben hozzáadja az URI és a kulcsok értékeit egy környezeti változóhoz.

## <a name="object-model"></a><a id="object-model"></a>Objektummodell

Az alkalmazás létrehozása előtt nézzük meg az erőforrás-hierarchiát a Azure Cosmos DB és az erőforrások létrehozásához és eléréséhez használt objektummodellhez. A Azure Cosmos DB a következő sorrendben hozza létre az erőforrásokat:

* Azure Cosmos-fiók 
* Adatbázisok 
* Tárolók 
* Elemek

Ha többet szeretne megtudni a különböző entitások hierarchiájáról, tekintse meg az [adatbázisok,](account-databases-containers-items.md) tárolók és elemek a Azure Cosmos DB cikkben. Ezeket az erőforrásokat a következő .NET-osztályokkal fogja használni:

* CosmosClient – Ez az osztály ügyféloldali logikai megjelenítést biztosít a Azure Cosmos DB számára. Az ügyfélobjektum a szolgáltatásra vonatkozó kérések konfigurálára és végrehajtására használható.
* CreateDatabaseIfNotExistsAsync – Ez a metódus aszinkron műveletként létrehoz (ha még nem létezik) vagy lekért (ha már létezik) egy adatbázis-erőforrást. 
* CreateContainerIfNotExistsAsync – Ez a metódus (ha még nem létezik) vagy aszinkron műveletként lekért (ha már létezik) egy tárolót. A válasz állapotkódját ellenőrizve megállapíthatja, hogy a tároló újonnan lett létrehozva (201) vagy egy meglévő tároló lett visszaadva (200). 
* CreateItemAsync – Ez a metódus egy elemet hoz létre a tárolóban.
* UpsertItemAsync – Ez a metódus létrehoz egy elemet a tárolóban, ha még nem létezik, vagy lecseréli az elemet, ha már létezik. 
* GetItemQueryIterator – Ez a metódus paraméteres értékeket tartalmazó SQL-utasítással hoz létre lekérdezést egy Azure Cosmos-adatbázisban lévő tárolóban lévő elemekhez. 
* DeleteAsync – Törli a megadott adatbázist az Azure Cosmos-fiókból. `DeleteAsync` A metódus csak az adatbázist törli.

 ## <a name="code-examples"></a><a id="code-examples"></a>Kódpéldák

A cikkben leírt mintakód létrehoz egy családadatbázist a Azure Cosmos DB. A családadatbázis olyan családadatokat tartalmaz, mint a név, a cím, a hely, a kapcsolódó szülők, gyermekek és kisállatok. Mielőtt az adatokat az Azure Cosmos-fiókba berakja, definiálja egy családelem tulajdonságait. Hozzon létre egy nevű új osztályt a mintaalkalmazás gyökérszintjénél, és adja hozzá a `Family.cs` következő kódot:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>Adja hozzá a using irányelveket & ügyfélobjektum definiálása érdekében

A projektkönyvtárból nyissa meg a fájlt a szerkesztőben, és adja hozzá a következő using irányelveket az `Program.cs` alkalmazás tetején:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Adja hozzá a következő globális változókat a `Program` osztályhoz. Ezek közé tartoznak a végpontok és az engedélyezési kulcsok, az adatbázis neve és a létrehoz majd egy tároló. Ügyeljen arra, hogy a környezetnek megfelelően cserélje le a végpont és az engedélyezési kulcsok értékeit. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Végül cserélje le a `Main` metódust:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Adatbázis létrehozása 

Definiálja `CreateDatabaseAsync` a metódust a `program.cs` osztályon belül. Ez a metódus létrehozza `FamilyDatabase` a et, ha még nem létezik.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Tároló létrehozása

Definiálja `CreateContainerAsync` a metódust a `Program` osztályon belül. Ez a metódus létrehozza `FamilyContainer` a et, ha még nem létezik. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Elem létrehozása

Hozzon létre egy családelemet a `AddItemsToContainerAsync` metódus hozzáadásával az alábbi kóddal. A vagy a `CreateItemAsync` metódus használatával hozhat létre `UpsertItemAsync` elemet:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Az elemek lekérdezése

Egy elem beszúrása után lekérdezés futtatásával lekérdezheti az "Andersen" család részleteit. Az alábbi kód bemutatja, hogyan hajthatja végre a lekérdezést közvetlenül az SQL-lekérdezés használatával. Az "Gordon" család részleteit lekért SQL-lekérdezés a következő: `SELECT * FROM c WHERE c.LastName = 'Andersen'` . Definiálja `QueryItemsAsync` a metódust a `Program` osztályon belül, és adja hozzá a következő kódot:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Elem cseréje 

Olvassa el a családelemet, majd frissítse a metódus hozzáadásával `ReplaceFamilyItemAsync` az alábbi kóddal.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Elem törlése 

Egy családelem törléséhez adja hozzá a `DeleteFamilyItemAsync` metódust az alábbi kóddal.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>Az adatbázis törlése 

Végül törölheti az adatbázist a `DeleteDatabaseAndCleanupAsync` metódus hozzáadásával a következő kóddal:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Az összes szükséges metódus hozzáadása után mentse a `Program` fájlt. 

## <a name="run-the-code"></a>A kód futtatása

Ezután hozza létre és futtassa az alkalmazást a Azure Cosmos DB létrehozásához.

   ```bash
   dotnet run
   ```

Az alkalmazás futtatásakor a következő kimenet jön létre. Bejelentkezhet a Azure Portal és ellenőrizheti, hogy az erőforrások létrejöttek-e:

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

Az adatok létrejöttének ellenőrzéséhez bejelentkezhet a Azure Portal és láthatja a szükséges elemeket az Azure Cosmos-fiókjában. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az Azure CLI vagy a Azure PowerShell eltávolíthatja az Azure Cosmos-fiókot és a megfelelő erőforráscsoportot. A következő parancs bemutatja, hogyan törölheti az erőforráscsoportot az Azure CLI használatával:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Egy Azure Cosmos-fiókot, hogyan hozhat létre egy adatbázist és egy tárolót egy .NET Core-alkalmazással. Most már további adatokat importálhat Az Azure Cosmos-fiókba az alábbi cikk utasításait követve. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)
