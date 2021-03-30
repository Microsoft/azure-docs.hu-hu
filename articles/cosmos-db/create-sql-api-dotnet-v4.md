---
title: Azure Cosmos DB SQL API-erőforrások kezelése a .NET v4 SDK-val
description: Útmutató a konzolos alkalmazások .NET v4 SDK használatával történő létrehozásához Azure Cosmos DB SQL API-fiók erőforrásainak kezeléséhez.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: devx-track-dotnet
ms.openlocfilehash: 224a1b67ff0282c216763229593fcfed81d7567b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93090103"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Gyors útmutató: konzol-alkalmazás létrehozása a .NET v4 SDK használatával Azure Cosmos DB SQL API-fiók erőforrásainak kezeléséhez.
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ismerkedés a Azure Cosmos DB SQL API .NET-hez készült ügyféloldali kódtáraval. Kövesse a jelen dokumentum lépéseit a .NET v4 (Azure. Cosmos) csomag telepítéséhez, egy alkalmazás létrehozásához, és próbálja ki az alapszintű szifilisz-műveletekre vonatkozó példát a Azure Cosmos DBban tárolt adatokon. 

Azure Cosmos DB a Microsoft gyors NoSQL-adatbázisa, amely bármilyen méretű nyitott API-val rendelkezik. A Azure Cosmos DB a kulcs/érték, a dokumentum és a Graph-adatbázisok gyors létrehozásához és lekérdezéséhez használható. A .NET-hez készült Azure Cosmos DB SQL API ügyféloldali kódtára a következőre használható:

* Azure Cosmos-adatbázis és-tároló létrehozása
* Mintaadatok hozzáadása a tárolóhoz
* Adatok lekérdezése 
* Az adatbázis törlése

[Könyvtár forráskódja](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/) , vagy ingyenes Azure-előfizetés nélkül is [kipróbálhat Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) 
* [Net Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). A futtatásával ellenőrizheti, hogy melyik verzió érhető el a környezetben `dotnet --version` .

## <a name="setting-up"></a>Beállítás

Ez a szakasz végigvezeti egy Azure Cosmos-fiók létrehozásán és egy olyan projekt beállításán, amely Azure Cosmos DB SQL API-ügyfél-függvénytárat használ a .NET-hez az erőforrások kezeléséhez. A cikkben ismertetett mintakód létrehoz egy `FamilyDatabase` adatbázist és egy családtagot (az egyes családtagok egy elem) az adott adatbázison belül. Minden családtag olyan tulajdonságokkal rendelkezik, mint például a `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` . A `LastName` tulajdonságot a tároló partíciós kulcsaként használja a rendszer. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Azure Cosmos-fiók létrehozása

Ha az [ingyenes kipróbálás Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) lehetőséget használja az Azure Cosmos-fiók létrehozásához, létre kell hoznia egy **SQL API** típusú Azure Cosmos db fiókot. Már létrehoztak egy Azure Cosmos DB tesztelési fiókot. Nem kell explicit módon létrehoznia a fiókot, így kihagyhatja ezt a szakaszt, és átléphet a következő szakaszra.

Ha rendelkezik saját Azure-előfizetéssel, vagy ingyenes előfizetést hozott létre, explicit módon hozzon létre egy Azure Cosmos-fiókot. A következő kód egy Azure Cosmos-fiókot hoz létre a munkamenet konzisztenciájával. A fiók a és a-ben replikálódik `South Central US` `North Central US` .  

Az Azure Cosmos-fiók létrehozásához Azure Cloud Shell használható. Az Azure Cloud Shell interaktív, hitelesített, böngészőben elérhető felület az Azure-erőforrások felügyeletéhez. Rugalmasságot nyújt a munkamódszeréhez leginkább illő héjfelület (akár a Bash, akár a PowerShell) kiválasztásának lehetőségével. Ebben a rövid útmutatóban válassza a **bash** mód lehetőséget. Azure Cloud Shell is szükség van egy Storage-fiókra, ha a rendszer kéri, létrehozhat egyet.

Válassza az alábbi kód melletti **kipróbálás** gombot, válassza a **bash** mód lehetőséget, válassza a **Storage-fiók létrehozása** és a Cloud Shellra való bejelentkezés lehetőséget. Ezután másolja és illessze be a következő kódot a Azure Cloud Shell és futtatásához. Az Azure Cosmos-fiók nevének globálisan egyedinek kell lennie, hogy a `mysqlapicosmosdb` parancs futtatása előtt frissítse az értéket.

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

Az Azure Cosmos-fiók létrehozása eltarthat egy ideig, amint a művelet sikeres, láthatja a megerősítő kimenetet. A parancs sikeres befejeződése után jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és ellenőrizze, hogy létezik-e a megadott nevű Azure Cosmos-fiók. Az erőforrás létrehozása után a Azure Cloud Shell ablakot is lezárhatja. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Új .NET-alkalmazás létrehozása

Hozzon létre egy új .NET-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben. Nyissa meg a Windows-parancssort vagy egy terminál-ablakot a helyi számítógépről. A következő szakaszban lévő összes parancsot a parancssorból vagy a terminálból fogja futtatni.  A következő DotNet új parancs futtatásával hozzon létre egy új alkalmazást a névvel `todo` . A--langVersion paraméter beállítja a LangVersion tulajdonságot a létrehozott projektfájl számára.

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

   ```bash
   cd todo
   dotnet build
   ```

A Build várt kimenetének a következőhöz hasonlóan kell kinéznie:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>A Azure Cosmos DB csomag telepítése

Miközben továbbra is az alkalmazás könyvtárában található, telepítse a .NET Core-hoz készült Azure Cosmos DB ügyféloldali kódtárat a DotNet-csomag hozzáadása paranccsal.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Az Azure Cosmos-fiók hitelesítő adatainak másolása a Azure Portal

A minta alkalmazásnak hitelesítenie kell magát az Azure Cosmos-fiókban. A hitelesítéshez át kell adni az Azure Cosmos-fiók hitelesítő adatait az alkalmazásnak. Az Azure Cosmos-fiók hitelesítő adatait a következő lépésekkel szerezheti be:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Navigáljon az Azure Cosmos-fiókjához.

1. Nyissa meg a **kulcsok** ablaktáblát, és másolja a fiókjának **URI-JÁT** és **elsődleges kulcsát** . A következő lépésben hozzá kell adnia az URI és a kulcsok értékeit egy környezeti változóhoz.

## <a name="object-model"></a><a id="object-model"></a>Objektummodell

Az alkalmazás létrehozásának megkezdése előtt tekintsük át a Azure Cosmos DB erőforrásainak hierarchiáját, és az erőforrások létrehozásához és eléréséhez használt objektummodell. A Azure Cosmos DB erőforrásokat hoz létre a következő sorrendben:

* Azure Cosmos-fiók 
* Adatbázisok 
* Tárolók 
* Elemek

A különböző entitások hierarchiájának megismeréséhez tekintse meg az [adatbázisok, tárolók és elemek használata Azure Cosmos db](account-databases-containers-items.md) cikkben. A következő .NET-osztályokat fogja használni az alábbi erőforrásokkal való interakcióhoz:

* CosmosClient – ez az osztály a Azure Cosmos DB szolgáltatás ügyféloldali logikai ábrázolását biztosítja. Az ügyfél-objektum a kérések konfigurálására és végrehajtására szolgál a szolgáltatáson.
* Createdatabaseifnotexistasync metódusának – ez a metódus (ha nem létezik) vagy (ha már létezik) egy adatbázis-erőforrás aszinkron műveletként való létrehozása. 
* CreateContainerIfNotExistsAsync – ez a metódus (ha nem létezik) vagy (ha már létezik) a tároló aszinkron műveletként jön létre. A válaszban található állapotkód alapján megállapíthatja, hogy a tárolót újonnan hozták-e létre (201), vagy egy meglévő tárolót adott vissza (200). 
* CreateItemAsync – ez a metódus létrehoz egy tételt a tárolón belül.
* UpsertItemAsync – ez a metódus létrehoz egy tételt a tárolón belül, ha még nem létezik, vagy lecseréli az adott elemre, ha az már létezik. 
* GetItemQueryIterator – ez a metódus létrehoz egy lekérdezést az Azure Cosmos-adatbázis tárolói alá tartozó elemekhez egy paraméteres értékeket tartalmazó SQL-utasítás használatával. 
* DeleteAsync – törli a megadott adatbázist az Azure Cosmos-fiókból. `DeleteAsync` a metódus csak az adatbázist törli.

 ## <a name="code-examples"></a><a id="code-examples"></a>Kódpéldák

A cikkben ismertetett mintakód egy családi adatbázist hoz létre Azure Cosmos DBban. A család adatbázisa olyan családi adatokat tartalmaz, mint például a név, a lakcím, a hely, a társított szülők, a gyermekek és a háziállatok. Mielőtt feltölti az adatokat az Azure Cosmos-fiókjába, definiálja egy családi elem tulajdonságait. Hozzon létre egy új, nevű osztályt `Family.cs` a minta-alkalmazás legfelső szintjén, és adja hozzá a következő kódot:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>Adja hozzá a using direktíva & a Client objektum megadása

A projekt könyvtárában nyissa meg a `Program.cs` fájlt a szerkesztőben, és adja hozzá a következő használati irányelveket az alkalmazás tetején:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Adja hozzá a következő globális változókat az `Program` osztályban. Ezek közé tartozik a végpont és az engedélyezési kulcsok, az adatbázis neve és a létrehozandó tároló. Ügyeljen rá, hogy a végpont és az engedélyezési kulcsok értékét a környezetnek megfelelően cserélje le. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Végül cserélje le a `Main` metódust:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Adatbázis létrehozása 

Adja meg a `CreateDatabaseAsync` metódust a `program.cs` osztályon belül. Ez a metódus hozza létre a `FamilyDatabase` Ha még nem létezik.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Tároló létrehozása

Adja meg a `CreateContainerAsync` metódust a `Program` osztályon belül. Ez a metódus hozza létre a `FamilyContainer` Ha még nem létezik. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Elem létrehozása

Hozzon létre egy családi tételt a metódus hozzáadásával a `AddItemsToContainerAsync` következő kóddal. `CreateItemAsync` `UpsertItemAsync` Egy tétel létrehozásához használhatja a vagy metódust:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Elemek lekérdezése

Egy elem beszúrása után futtathat egy lekérdezést, amely az "Andersen" család részleteit kéri le. A következő kód bemutatja, hogyan hajthatja végre a lekérdezést közvetlenül az SQL-lekérdezés használatával. Az "Anderson" család részleteit lekérdező SQL-lekérdezés a következő: `SELECT * FROM c WHERE c.LastName = 'Andersen'` . Adja meg a `QueryItemsAsync` metódust a `Program` osztályon belül, és adja hozzá a következő kódot:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Elem cseréje 

Olvassa el a termékcsaládot, majd frissítse a metódus hozzáadásával a `ReplaceFamilyItemAsync` következő kóddal.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Elem törlése 

A `DeleteFamilyItemAsync` metódusnak a következő kóddal való hozzáadásával törölje a családi elemeket.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>Az adatbázis törlése 

Végül törölheti az adatbázist a metódus hozzáadásával `DeleteDatabaseAndCleanupAsync` a következő kóddal:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Miután hozzáadta az összes szükséges metódust, mentse a `Program` fájlt. 

## <a name="run-the-code"></a>A kód futtatása

Ezután hozza létre és futtassa az alkalmazást a Azure Cosmos DB erőforrások létrehozásához.

   ```bash
   dotnet run
   ```

Az alkalmazás futtatásakor a következő kimenet jön létre. A Azure Portalba is bejelentkezhet, és ellenőrizheti, hogy az erőforrások létrejöttek-e:

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

Az adatok létrejöttének ellenőrzéséhez jelentkezzen be a Azure Portalba, és tekintse meg az Azure Cosmos-fiókban szükséges elemeket. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja az Azure CLI-t vagy Azure PowerShell az Azure Cosmos-fiók és a hozzá tartozó erőforráscsoport eltávolításához. Az alábbi parancs bemutatja, hogyan törölheti az erőforráscsoportot az Azure CLI használatával:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos-fiókot, hogyan hozhat létre egy adatbázist és egy tárolót egy .NET Core-alkalmazás használatával. Mostantól a következő cikk utasításait követve importálhat további információkat az Azure Cosmos-fiókjába. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)
