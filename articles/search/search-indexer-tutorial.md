---
title: C#-oktatóanyag az Azure SQL-adatindexeléshez
titleSuffix: Azure Cognitive Search
description: Ebben a C# oktatóanyagban csatlakozhat a Azure SQL Databasehoz, kinyerheti a kereshető adatok kinyerését, és betöltheti azt egy Azure Cognitive Search-indexbe.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/23/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: e2ca5f42120661b887d07e697596f41cb7a7fce4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98745767"
---
# <a name="tutorial-index-azure-sql-data-using-the-net-sdk"></a>Oktatóanyag: az Azure SQL-adatainak indexelése a .NET SDK használatával

Konfiguráljon egy [Indexelő](search-indexer-overview.md) a kereshető adatok kinyeréséhez Azure SQL Databaseről, majd küldje el azt egy keresési indexbe az Azure Cognitive Searchban. 

Ez az oktatóanyag a C# és a [.net SDK](/dotnet/api/overview/azure/search) használatával hajtja végre a következő feladatokat:

> [!div class="checklist"]
> * Azure SQL Databasehoz csatlakozó adatforrás létrehozása
> * Indexelő létrehozása
> * Indexelő futtatása az adatgyűjtés indexbe való betöltéséhez
> * Index lekérdezése ellenőrzési lépésként

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

+ [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Meglévő keresési szolgáltatás](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [létrehozása](search-create-service-portal.md) vagy keresése 

> [!Note]
> Ehhez az oktatóanyaghoz használhatja az ingyenes szolgáltatást. Az ingyenes keresési szolgáltatás három indexre, három indexelő elemre és három adatforrásra korlátozza a szolgáltatást. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a szolgáltatásban az új erőforrások elfogadására szolgáló helyiséggel.

## <a name="download-files"></a>Fájlok letöltése

Az oktatóanyag forráskódja az [Azure-Samples/Search-DotNet-Getting-Started GitHub-](https://github.com/Azure-Samples/search-dotnet-getting-started) tárház [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) mappájában található.

## <a name="1---create-services"></a>1 – szolgáltatások létrehozása

Ez az oktatóanyag az Azure Cognitive Searcht használja az indexeléshez és a lekérdezésekhez, és Azure SQL Database külső adatforrásként. Ha lehetséges, hozzon létre mindkét szolgáltatást ugyanabban a régióban és erőforráscsoporthoz a közelség és a kezelhetőség érdekében. A gyakorlatban Azure SQL Database bármely régióban lehet.

### <a name="start-with-azure-sql-database"></a>Kezdés Azure SQL Database

Ebben a lépésben hozzon létre egy külső adatforrást Azure SQL Database, amelyet az indexelő képes feltérképezni. Az adatkészlet létrehozásához használja a Azure Portal és a *Hotels. SQL* fájlt a minta letöltésből Azure SQL Database. Az Azure Cognitive Search felhasználja az összeolvasztott sorhalmazokat, például egy nézetből vagy lekérdezésből generált egyet. A minta megoldásban található SQL-fájl egyetlen táblát hoz létre és tölt fel.

Ha meglévő Azure SQL Database erőforrással rendelkezik, a 4. lépéstől kezdődően felveheti a Hotels táblát.

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/).

1. **SQL Database** keresése vagy létrehozása. Használhatja az alapértelmezett beállításokat és a legalacsonyabb szintű tarifacsomagot. A kiszolgáló létrehozásának egyik előnye, hogy megadhat egy rendszergazdai felhasználónevet és jelszót, amelyekre egy későbbi lépésben lesz szüksége a táblák létrehozásához és betöltéséhez.

   :::image type="content" source="media/search-indexer-tutorial/indexer-new-sqldb.png" alt-text="Új adatbázis oldal" border="false":::

1. Kattintson a **felülvizsgálat + létrehozás** lehetőségre az új kiszolgáló és adatbázis telepítéséhez. Várjon, amíg a rendszer elvégzi a kiszolgáló és az adatbázis üzembe helyezését.

1. A navigációs ablaktáblán kattintson a **Lekérdezés-szerkesztő (előzetes verzió)** elemre, és adja meg a kiszolgáló-rendszergazda felhasználónevét és jelszavát. 

   Ha a hozzáférés meg van tagadva, másolja az ügyfél IP-címét a hibaüzenetből, majd kattintson a **kiszolgáló tűzfalának beállítása** hivatkozásra egy olyan szabály hozzáadásához, amely lehetővé teszi a hozzáférést az ügyfélszámítógépről a tartományhoz tartozó ügyfél IP-címének használatával. A szabály érvénybe léptetése több percet is igénybe vehet.

1. A lekérdezés-szerkesztőben kattintson a **lekérdezés megnyitása** lehetőségre, és navigáljon a *Hotels. SQL* fájl helyére a helyi számítógépen. 

1. Jelölje ki a fájlt, és kattintson a **Megnyitás** lehetőségre. A szkriptnek az alábbi képernyőfelvételhez hasonlóan kell kinéznie:

   :::image type="content" source="media/search-indexer-tutorial/sql-script.png" alt-text="SQL-szkript" border="false":::

1. Kattintson a **Futtatás** gombra a lekérdezés végrehajtásához. Az Eredmények ablaktáblán a „Sikeres lekérdezés” üzenetnek kell megjelennie 3 sorra vonatkozóan.

1. A jelen táblából származó sorhalmaz visszaadásához hajtsa végre a következő lekérdezést ellenőrzési lépésként:

    ```sql
    SELECT * FROM Hotels
    ```

1. Másolja az adatbázishoz tartozó ADO.NET-kapcsolatok karakterláncát. A **Beállítások**  >  **kapcsolódási karakterláncok** alatt másolja a ADO.net-kapcsolódási karakterláncot az alábbi példához hasonló módon.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

A következő gyakorlatban szüksége lesz erre a hálózati karakterláncra a környezet beállításához.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

A következő összetevő az Azure Cognitive Search, amelyet [a portálon lehet létrehozni](search-create-service-portal.md). A bemutató elvégzéséhez használhatja az ingyenes szintet. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Rendszergazdai API-kulcs és URL-cím beszerzése az Azure Cognitive Search

Az API-hívásokhoz a szolgáltatás URL-címe és egy hozzáférési kulcs szükséges. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások**  >  **kulcsaiban** kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="HTTP-végpont és elérési kulcs beszerzése" border="false":::

## <a name="2---set-up-your-environment"></a>2 – a környezet beállítása

1. Indítsa el a Visual studiót, és nyissa meg a **dotnethowtoindexers elemre. SLN**.

1. Megoldáskezelő a kapcsolódási adatok megadásához nyissa meg a **appsettings.js** .

1. A (z) esetében `SearchServiceEndPoint` , ha a szolgáltatás áttekintő oldalán található teljes URL-cím " https://my-demo-service.search.windows.net ", akkor a megadni kívánt érték az URL-cím.

1. A esetében `AzureSqlConnectionString` a karakterlánc formátuma a következőhöz hasonló: `"Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"`

    ```json
    {
      "SearchServiceEndPoint": "<placeholder-search-url>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-search-service>",
      "AzureSqlConnectionString": "<placeholder-ADO.NET-connection-string",
    }
    ```

1. A kapcsolatok karakterláncában ellenőrizze, hogy a kapcsolatok karakterlánca tartalmaz-e érvényes jelszót. Amíg az adatbázis és a felhasználónevek át lesznek másolva, a jelszót manuálisan kell megadnia.

## <a name="3---create-the-pipeline"></a>3 – a folyamat létrehozása

Az indexelő adatforrást és indexet igényel. A kapcsolódó kód két fájlban található:

  + az indexet meghatározó sémát tartalmazó **Hotel. cs**
  + A szolgáltatásban található struktúrák létrehozására és kezelésére szolgáló függvényeket tartalmazó **program. cs**

### <a name="in-hotelcs"></a>A hotel.cs fájlban

Az indexséma határozza meg a mezők gyűjteményét, beleértve az attribútumokat, amelyek megadják az engedélyezett műveleteket, például azt, hogy az adott mező alkalmas-e teljes szöveges keresésre, szűrésre vagy rendezésre, ahogy a HotelName alábbi mezőmeghatározása esetében is látható. A [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) teljes szöveges kereshetőség a definíció alapján. A többi attribútum explicit módon van hozzárendelve.

```csharp
. . . 
[SearchableField(IsFilterable = true, IsSortable = true)]
[JsonPropertyName("hotelName")]
public string HotelName { get; set; }
. . .
```

A sémák tartalmazhatnak egyéb elemeket is, például keresési pontszámok növelésére használt pontozási profilokat, egyéni elemzőket és egyéb szerkezeteket. Jelenlegi céljainknak megfelelően azonban a sémát kevés elem határozza meg, így csak a minta adatkészletekben lévő mezők találhatók meg benne.

### <a name="in-programcs"></a>A Program.cs fájlban

A fő program [egy indexelő ügyfél](/dotnet/api/azure.search.documents.indexes.models.searchindexer), egy index, egy adatforrás és egy indexelő létrehozására szolgáló logikát tartalmaz. A kód észleli és törli az azonos nevű meglévő erőforrásokat, azt feltételezve, hogy többször is futtatja ezt a programot.

Az adatforrás-objektum a Azure SQL Database erőforrásokra vonatkozó beállításokkal van konfigurálva, beleértve a [részleges vagy növekményes indexelést](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) is az Azure SQL beépített [változás-észlelési funkcióinak](/sql/relational-databases/track-changes/about-change-tracking-sql-server) kihasználásához. Az Azure SQL-ben a forrásként szolgáló demo Hotels-adatbázis a **IsDeleted** nevű "Soft Delete" oszlopot tartalmaz. Ha ez az oszlop igaz értékre van állítva az adatbázisban, az indexelő eltávolítja a megfelelő dokumentumot az Azure Cognitive Search indexből.

```csharp
Console.WriteLine("Creating data source...");

var dataSource =
      new SearchIndexerDataSourceConnection(
         "hotels-sql-ds",
         SearchIndexerDataSourceType.AzureSql,
         configuration["AzureSQLConnectionString"],
         new SearchIndexerDataContainer("hotels"));

indexerClient.CreateOrUpdateDataSourceConnection(dataSource);
```

Az indexelő objektum a platform-agnosztikus, ahol a konfiguráció, az ütemezés és a hívás azonos a forrástól függetlenül. Ez a példa indexelő tartalmaz egy ütemtervet, egy alaphelyzetbe állítási lehetőséget, amely törli az indexelő előzményeit, és azonnal meghívja a metódust az indexelő létrehozásához és futtatásához. Indexelő létrehozásához vagy frissítéséhez használja a [CreateOrUpdateIndexerAsync](/dotnet/api/azure.search.documents.indexes.searchindexerclient.createorupdateindexerasync).

```csharp
Console.WriteLine("Creating Azure SQL indexer...");

var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
      StartTime = DateTimeOffset.Now
};

var parameters = new IndexingParameters()
{
      BatchSize = 100,
      MaxFailedItems = 0,
      MaxFailedItemsPerBatch = 0
};

// Indexer declarations require a data source and search index.
// Common optional properties include a schedule, parameters, and field mappings
// The field mappings below are redundant due to how the Hotel class is defined, but 
// we included them anyway to show the syntax 
var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
      Description = "Data indexer",
      Schedule = schedule,
      Parameters = parameters,
      FieldMappings =
      {
         new FieldMapping("_id") {TargetFieldName = "HotelId"},
         new FieldMapping("Amenities") {TargetFieldName = "Tags"}
      }
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

Az indexelő futtatások általában ütemezettek, de a fejlesztés során előfordulhat, hogy azonnal futtatni szeretné az indexelő a [RunIndexerAsync](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexerasync)használatával.

```csharp
Console.WriteLine("Running Azure SQL indexer...");

try
{
      await indexerClient.RunIndexerAsync(indexer.Name);
}
catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
{
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
}
```

## <a name="4---build-the-solution"></a>4 – a megoldás összeállítása

A megoldás létrehozásához és futtatásához nyomja le az F5 billentyűt. A program hibakeresési módban lesz végrehajtva. Az egyes műveletek állapota a konzolablakban látható.

   :::image type="content" source="media/search-indexer-tutorial/console-output.png" alt-text="Konzolkimenet" border="false":::

A kód helyileg fut a Visual Studióban, és csatlakozik a keresési szolgáltatáshoz az Azure-ban, amely összekapcsolja a Azure SQL Database és beolvassa az adatkészletet. Ezzel a sok művelettel több lehetséges meghibásodási pont is van. Ha hibaüzenetet kap, először ellenőrizze az alábbi feltételeket:

+ Az Ön által megadott keresési szolgáltatás elérhetőségi adatai a teljes URL-cím. Ha csak a szolgáltatás nevét adta meg, akkor a művelet nem a kapcsolódási hiba miatt leáll az index létrehozásakor.

+ Az adatbázis kapcsolati adatai az **appsettings.json** fájlban. Ennek a portálról beszerzett ADO.NET kapcsolati sztringnek kell lennie, amelyet úgy módosítottunk, hogy tartalmazza az adatbázishoz tartozó felhasználónevet és jelszót. A felhasználói fióknak megfelelő engedéllyel kell rendelkeznie az adatok lekérdezéséhez. A helyi ügyfél IP-címének engedélyezve kell lennie a tűzfalon keresztüli bejövő hozzáférésnek.

+ Erőforráskorlátok. Ne felejtse el, hogy az ingyenes szinten 3 index, indexelő és adatforrásra vonatkozó korlát szerepel. A felső korlátot elért szolgáltatások nem képesek új objektumok létrehozására.

## <a name="5---search"></a>5 – keresés

A Azure Portal használatával ellenőrizze az objektumok létrehozását, majd a **keresési ablak** használatával kérdezheti le az indexet.

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **áttekintése** lapon nyissa meg az egyes listákat, és ellenőrizze, hogy létrejött-e az objektum. Az **indexek**, az **Indexelő** és **az adatforrások** "Hotels", "Azure-SQL-indexelő" és "Azure-SQL" lesz.

   :::image type="content" source="media/search-indexer-tutorial/tiles-portal.png" alt-text="Indexelők és adatforrások csempéi" border="false":::

1. Válassza ki a hotelek indexét. A szállodák lapon a **Search Explorer** az első lap. 

1. Ha üres lekérdezést szeretne kiadni, kattintson a **Keresés** gombra. 

   A rendszer az indexben lévő három bejegyzést JSON-dokumentumként adja vissza. A keresési ablak a dokumentumokat JSON-formátumban adja vissza, így a teljes struktúra megtekinthető.

   :::image type="content" source="media/search-indexer-tutorial/portal-search.png" alt-text="Index lekérdezése" border="false":::
   
1. A következő lépésben adja meg ezt a keresési sztringet: `search=river&$count=true`. 

   Ez a lekérdezés teljes szöveges keresést indít a `river` kifejezésre, az eredmény pedig tartalmazza az egyező dokumentumok darabszámát. Az egyező dokumentumok darabszámának visszaadása hasznos lehet az olyan forgatókönyvek tesztelése esetében, amelyekben több ezer vagy több millió dokumentumot tartalmazó, nagy méretű indexszel rendelkezik. Ebben az esetben a lekérdezésnek csak egy dokumentum felel meg.

1. Végül adjon meg egy olyan keresési sztringet, amely a JSON-kimeneteket a kívánt mezőkre korlátozza: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   A lekérdezési válasz csak a kiválasztott mezőket tartalmazza, így a kimenet tömörebb lesz.

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A fejlesztés korai kísérleti szakaszaiban a tervezési iteráció legalkalmasabb megközelítése az objektumok törlése az Azure Cognitive Search és a kód újraépítésének engedélyezése. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

Az oktatóanyaghoz tartozó mintakód ellenőrzi a meglévő objektumokat, és törli őket, hogy újra lehessen futtatni a kódot.

A portál segítségével indexeket, indexelő fájlokat és adatforrásokat is törölhet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, a projekt végén érdemes lehet eltávolítani a már nem szükséges erőforrásokat. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a minden erőforrás vagy erőforráscsoport hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri a SQL Database indexelés alapjait, ismerkedjen meg közelebbről az indexelő konfigurációjában.

> [!div class="nextstepaction"]
> [SQL Database indexelő konfigurálása](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)