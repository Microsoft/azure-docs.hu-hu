---
title: Rugalmas adatbázis-ügyféloldali kódtár használata Entity Framework
description: Elastic Database ügyféloldali kódtár és Entity Framework használata kódolási adatbázisokhoz
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/04/2019
ms.openlocfilehash: 48d43cb2d3c51194d0708a2b9b739a0ee87843d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793398"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Ügyféloldali kódtár Elastic Database Entity Framework
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a dokumentum egy Entity Framework alkalmazás azon módosításait mutatja be, amelyek a [Elastic Database eszközökkel](elastic-scale-introduction.md)való integráláshoz szükségesek. A hangsúly a szegmensek közötti [Térkép kezelésének](elastic-scale-shard-map-management.md) és az [Adatfüggő útválasztásnak](elastic-scale-data-dependent-routing.md) a Entity Framework **Code első** megközelítéssel való létrehozásán alapul. A [kód első –](/ef/ef6/modeling/code-first/workflows/new-database) az EF-hez készült adatbázis-oktatóanyag a jelen dokumentumban futó példaként szolgál. A dokumentumot kísérő mintakód a Visual Studio Code-mintákban található, a rugalmas adatbázis-eszközök mintájának részét képezi.

## <a name="downloading-and-running-the-sample-code"></a>A mintakód letöltése és futtatása

A cikk kódjának letöltéséhez:

* A Visual Studio 2012-es vagy újabb verziójára van szükség.
* Töltse le az [Azure SQL-Entity Framework Integration Sample-hez készült rugalmas adatbázis-eszközöket](https://github.com/Azure/elastic-db-tools/). Bontsa ki a mintát a választott helyre.
* Indítsa el a Visual Studiót.
* A Visual Studióban válassza a fájl-> nyitott projekt/megoldás lehetőséget.
* A **projekt megnyitása** párbeszédpanelen navigáljon a letöltött mintához, és válassza a **EntityFrameworkCodeFirst. SLN** elemet a minta megnyitásához.

A minta futtatásához három üres adatbázist kell létrehoznia Azure SQL Databaseban:

* Szegmens Map Manager-adatbázis
* 1. szegmens adatbázis
* 2. szegmens adatbázis

Miután létrehozta ezeket az adatbázisokat, töltse ki a **program. cs** hely tulajdonosainak a kiszolgáló nevét, az adatbázis nevét és az adatbázisokhoz való kapcsolódáshoz szükséges hitelesítő adatokat. Hozza létre a megoldást a Visual Studióban. A Visual Studio a létrehozási folyamat részeként letölti a rugalmas adatbázis ügyféloldali kódtár, Entity Framework és átmeneti hibák kezelésére vonatkozó szükséges NuGet-csomagokat. Győződjön meg arról, hogy az NuGet-csomagok visszaállítása engedélyezve van a megoldáshoz. A beállítás engedélyezéséhez kattintson a jobb gombbal a megoldás fájlra a Visual Studio Megoldáskezelő.

## <a name="entity-framework-workflows"></a>Munkafolyamatok Entity Framework

Entity Framework fejlesztők az alábbi négy munkafolyamat egyikét használják az alkalmazások létrehozásához és az alkalmazás-objektumok megőrzésének biztosításához:

* **Első kód (új adatbázis)**: az EF-fejlesztő létrehozza a modellt az alkalmazás kódjában, majd az EF létrehozza az adatbázist.
* **Első kód (meglévő adatbázis)**: a fejlesztő lehetővé teszi, hogy az EF létrehozza a modellhez tartozó alkalmazás kódját egy meglévő adatbázisból.
* **Első modell**: a fejlesztő létrehozza a MODELLT az EF Designerben, majd az EF létrehozza az adatbázist a modellből.
* **Adatbázis először**: a fejlesztő az EF-eszközök használatával következteti ki a modellt egy meglévő adatbázisból.

Mindezek a módszerek a DbContext osztályra támaszkodnak, hogy transzparens módon kezeljék az adatbázis-kapcsolatokat és az adatbázis-sémát egy alkalmazáshoz. A DbContext alaposztály különböző konstruktorai lehetővé teszik a kapcsolatok létrehozásához, az adatbázis-indításhoz és a séma létrehozásához szükséges különböző szintű szabályozást. A kihívások elsősorban abból erednek, hogy az EF által biztosított adatbázis-kapcsolat kezelése a rugalmas adatbázis ügyféloldali kódtár által biztosított adatkezelési képességekkel összefügg.

## <a name="elastic-database-tools-assumptions"></a>Rugalmas adatbázis-eszközök feltételezései

A kifejezések definícióit lásd: [Elastic Database eszközök Szószedet](elastic-scale-glossary.md).

A rugalmas adatbázis-ügyféloldali kódtár a shardletek nevű alkalmazásadatok partícióit határozza meg. A shardletek egy horizontális Felskálázási kulcs azonosítja, és meghatározott adatbázisokra van leképezve. Előfordulhat, hogy egy alkalmazásnak szüksége van egy tetszőleges számú adatbázisra, és terjesztenie kell a shardletek, hogy az aktuális üzleti igényeknek megfelelő kapacitást vagy teljesítményt biztosítson. Az adatbázisokra vonatkozó horizontális kulcsok értékének hozzárendelését a rugalmas adatbázis-ügyfél API-k által biztosított szegmenses Térkép tárolja. Ezt a képességet a rendszer a szegmenses **hozzárendelések felügyeletére** vagy rövid SMM. A szegmenses Térkép az adatbázis-kapcsolatok közvetítőjét is szolgálja a horizontális Felskálázási kulcsot tartalmazó kérelmek esetében. Ezt a képességet **adatátviteli útválasztásnak** nevezzük.

A szegmensek közötti Térkép kezelője védi a felhasználókat abban, hogy inkonzisztens nézeteket shardletbe adatokba, amelyek akkor fordulhatnak elő, ha egyidejű shardletbe-kezelési műveletek történnek (például az adatoknak az egyik szegmensből egy másikba való áthelyezése). Ehhez az ügyféloldali kódtár által felügyelt szegmens térképek egy alkalmazás adatbázis-kapcsolatai. Ez lehetővé teszi az adatbázis-kapcsolatok automatikus megölését, ha a szegmens-felügyeleti műveletek hatással lehetnek arra a shardletbe, amelyhez a kapcsolódás létrejött. Ennek a megközelítésnek integrálva kell lennie az egyes EF-funkciókkal, például új kapcsolatok létrehozásával egy meglévőből az adatbázis létezésének ellenőrzéséhez. Általánosságban elmondható, hogy a standard DbContext-konstruktorok csak megbízhatóan működnek olyan zárt adatbázis-kapcsolatok esetében, amelyek biztonságosan klónozottak az EF-munkavégzéshez. A rugalmas adatbázis tervezési elve csak a közvetítő nyitott kapcsolatainak megtervezése. Előfordulhat, hogy úgy gondolja, hogy az ügyféloldali kódtár által felügyelt kapcsolat bezárása előtt az EF-DbContext való átadása megoldhatja ezt a problémát. Azonban a kapcsolódás bezárásával és az EF-re való támaszkodva újra megnyithatja a könyvtárat. Az EF-ben lévő áttelepítési funkció azonban ezeket a kapcsolatokat használja az alapul szolgáló adatbázis-séma kezelésére az alkalmazás számára transzparens módon. Ideális esetben a rugalmas adatbázis-ügyfél függvénytárában és az EF-ben is megőrizheti és egyesítheti ezeket a képességeket ugyanabban az alkalmazásban. A következő szakasz részletesebben ismerteti ezeket a tulajdonságokat és követelményeket.

## <a name="requirements"></a>Követelmények

Ha a rugalmas adatbázis ügyféloldali függvénytárával és Entity Framework API-kkal dolgozik, a következő tulajdonságokat szeretné megőrizni:

* Horizontális **felskálázás**: a többrétegű alkalmazás adatszintjéből származó adatbázisok hozzáadásához vagy eltávolításához az alkalmazás kapacitási igényei szerint. Ez azt jelenti, hogy szabályozhatja az adatbázisok létrehozását és törlését, valamint a rugalmas adatbázis-szegmens Map Manager API-kat az adatbázisok kezeléséhez és a shardletek leképezéséhez.
* **Konzisztencia**: az alkalmazás horizontális felskálázást alkalmaz, és az ügyféloldali függvénytár adatkezelési funkcióit használja. A sérülés vagy helytelen lekérdezési eredmények elkerülése érdekében a kapcsolatok a szegmenses Térkép kezelőjén keresztül lesznek közvetítve. Ez megtartja az érvényesítést és a konzisztenciát is.
* **Első kód**: az EF Code első paradigma kényelmének megőrzése. Először a Code-ban az alkalmazásban szereplő osztályok transzparens módon vannak leképezve az alapul szolgáló adatbázis-struktúrákra. Az alkalmazás kódja együttműködik a DbSets, amelyek az alapul szolgáló adatbázis feldolgozásának legfontosabb szempontjait fedik le.
* **Séma**: Entity Framework kezeli a kezdeti adatbázis-séma létrehozását és az azt követő sémák alakulását az áttelepítés során. A képességek megőrzése révén az alkalmazás alkalmazkodik az adatváltozásokhoz.

Az alábbi útmutatás arra utasítja, hogyan teljesítheti ezeket a követelményeket a kód első alkalmazásaihoz rugalmas adatbázis-eszközök használatával.

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Adatfüggő útválasztás az EF DbContext

A Entity Frameworkekkel rendelkező adatbázis-kapcsolatokat általában a **DbContext** alosztályain keresztül kezeli a rendszer. Hozza létre ezeket az alosztályokat a **DbContext**-ből származtatva. Itt határozhatja meg a **DbSets** , amely megvalósítja az alkalmazáshoz tartozó CLR-objektumok adatbázis-alapú gyűjteményeit. Az Adatfüggő útválasztás kontextusában számos olyan hasznos tulajdonságot azonosíthat, amelyek nem feltétlenül tartanak fenn más EF Code első alkalmazási forgatókönyvek esetén:

* Az adatbázis már létezik, és regisztrálva van a rugalmas adatbázis-szegmenses térképen.
* Az alkalmazás sémája már telepítve van az adatbázison (lásd alább).
* Az adatbázishoz tartozó, adatkapcsolattal rendelkező útválasztási kapcsolatokat a szegmenses Térkép felügyeli.

A **DbContexts** integrálása Adatfüggő útválasztással a kibővíthető szolgáltatáshoz:

1. Hozzon létre fizikai adatbázis-kapcsolatokat a szegmens Map Manager rugalmas adatbázis-ügyfél felületén keresztül.
2. A **DbContext** alosztályhoz tartozó kapcsolatok tördelése
3. Továbbítsa a kapcsolódást a **DbContext** alaposztályaiba, így biztosítva, hogy az EF-oldal összes feldolgozása is megtörténjen.

A következő kódrészlet szemlélteti ezt a megközelítést. (Ez a kód a kapcsolódó Visual Studio-projektben is szerepel)

```csharp
public class ElasticScaleContext<T> : DbContext
{
public DbSet<Blog> Blogs { get; set; }
...

    // C'tor for data-dependent routing. This call opens a validated connection
    // routed to the proper shard by the shard map manager.
    // Note that the base class c'tor call fails for an open connection
    // if migrations need to be done and SQL credentials are used. This is the reason for the
    // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
    public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
        : base(CreateDDRConnection(shardMap, shardingKey, connectionStr),
        true /* contextOwnsConnection */)
    {
    }

    // Only static methods are allowed in calls into base class c'tors.
    private static DbConnection CreateDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
    {
        // No initialization
        Database.SetInitializer<ElasticScaleContext<T>>(null);

        // Ask shard map to broker a validated connection for the given key
        SqlConnection conn = shardMap.OpenConnectionForKey<T>
                            (shardingKey, connectionStr, ConnectionOptions.Validate);
        return conn;
    }
```

## <a name="main-points"></a>Fő pontok

* Egy új konstruktor helyettesíti az alapértelmezett konstruktort a DbContext alosztályban.
* Az új konstruktor azon argumentumokat veszi igénybe, amelyek az Adatfüggő útválasztáshoz szükségesek a rugalmas adatbázis ügyféloldali függvénytárán keresztül:
  
  * az Adatfüggő útválasztási felületek eléréséhez szükséges szegmenses leképezés
  * a shardletbe azonosítására szolgáló horizontális Felskálázási kulcs
  * egy olyan kapcsolódási karakterlánc, amely a szegmenshez tartozó Adatfüggő útválasztási kapcsolatok hitelesítő adataival rendelkezik.
* Az alaposztály konstruktorának hívása egy statikus metódusba kerül, amely elvégzi az adatkezeléshez szükséges összes lépést.
  
  * A rugalmas adatbázis-ügyféloldali felületek OpenConnectionForKey hívja meg a szegmenses térképen egy nyitott kapcsolat létesítéséhez.
  * A szegmenses Térkép létrehozza a nyitott kapcsolatokat a szegmenshez, amely az adott shardletbe tárolja.
  * Ezt a nyitott kapcsolatot a DbContext alaposztály-konstruktora adja vissza, amely azt jelzi, hogy a kapcsolatot az EF-nek kell használnia ahelyett, hogy az EF új kapcsolatot hozzon létre automatikusan. Ily módon a rendszer a rugalmas adatbázis-ügyfél API-val címkézte a kapcsolatokat, hogy az képes legyen a konzisztencia biztosítására a szegmensek közötti Térkép-felügyeleti műveletek során.

A kódban szereplő alapértelmezett konstruktor helyett használja az új konstruktort a DbContext alosztályhoz. Alább bemutatunk egy példát:

```csharp
// Create and save a new blog.

Console.Write("Enter a name for a new blog: ");
var name = Console.ReadLine();

using (var db = new ElasticScaleContext<int>(
                        sharding.ShardMap,  
                        tenantId1,  
                        connStrBldr.ConnectionString))
{
    var blog = new Blog { Name = name };
    db.Blogs.Add(blog);
    db.SaveChanges();

    // Display all Blogs for tenant 1
    var query = from b in db.Blogs
                orderby b.Name
                select b;
    …
}
```

Az új konstruktor megnyitja a kapcsolatot a szegmensben, amely a **tenantid1** értékével azonosított shardletbe vonatkozó adatmennyiséget tárolja. A **using** blokkban lévő kód változatlan marad, hogy hozzáférhessen a **DbSet** a blogok számára az EF használatával a **tenantid1**. Ez megváltoztatja a kód szemantikai feladatait a using blokkban úgy, hogy az összes adatbázis-művelet hatóköre a **tenantid1** által megőrzött egy szegmensre vonatkozik. A blogok **DbSet** tartozó LINQ-lekérdezések például csak az aktuális szegmensen tárolt, de más szegmenseken tárolt blogokat fogják visszaadni.  

### <a name="transient-faults-handling"></a>Átmeneti hibák kezelésére

A Microsoft Patterns & Practices csapata közzétette az [átmeneti hibák kezelésére szolgáló alkalmazás blokkját](/previous-versions/msp-n-p/dn440719(v=pandp.60)). A kódtárat az EF-sel együtt, rugalmasan méretezhető ügyféloldali kódtár használják. Azonban győződjön meg arról, hogy az átmeneti kivétel olyan helyre tér vissza, ahol biztosíthatja, hogy az új konstruktor egy átmeneti hiba után legyen használatban, hogy az új kapcsolódási kísérleteket a csípett konstruktorok használatával hajtsa végre. Ellenkező esetben a megfelelő szegmenshez való kapcsolódás nem garantált, és nincs garancia arra, hogy a rendszer megtartja a kapcsolódást a szegmenses Térkép változásakor.

Az alábbi mintakód azt szemlélteti, hogyan használható az SQL újrapróbálkozási szabályzata az új **DbContext** alosztály-konstruktorok köré:

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString))
        {
                var blog = new Blog { Name = name };
                db.Blogs.Add(blog);
                db.SaveChanges();
        …
        }
    });
```

A fenti kódban található **SqlDatabaseUtils. SqlRetryPolicy** egy 10 értékű újrapróbálkozási számmal rendelkező **SqlDatabaseTransientErrorDetectionStrategy** van definiálva, és 5 másodperc várakozási idő az újrapróbálkozások között. Ez a megközelítés hasonló az EF-hez és a felhasználó által kezdeményezett tranzakciókra vonatkozó útmutatáshoz (lásd: [korlátozások a végrehajtási stratégiák újrapróbálkozásával (EF6)](/ef/ef6/fundamentals/connection-resiliency/retry-logic). Mindkét esetben meg kell adnia, hogy az alkalmazás programja szabályozza azt a hatókört, amelyre az átmeneti kivétel vonatkozik: a tranzakció újbóli megnyitásához, vagy (ahogy azt látható) a rugalmas adatbázis-ügyféloldali függvénytárat használó megfelelő konstruktorból hozza létre újra a környezetet.

Annak szabályozására van szükség, hogy az átmeneti kivételek milyen mértékben kerülnek vissza a hatókörbe, az EF-hez készült beépített **SqlAzureExecutionStrategy** használatát is kizárja. A **SqlAzureExecutionStrategy** újra megnyit egy-kapcsolatot, de nem használja a **OpenConnectionForKey** -t, ezért megkerüli a **OpenConnectionForKey** -hívás részeként végrehajtott összes érvényesítést. Ehelyett a mintakód a beépített **DefaultExecutionStrategy** használja, amely az EF-hez is tartozik. A **SqlAzureExecutionStrategy** ellentétben az újrapróbálkozási szabályzattal együtt megfelelően működik az átmeneti hibák kezelésére. A végrehajtási házirend a **ElasticScaleDbConfiguration** osztályban van beállítva. Vegye figyelembe, hogy nem használjuk a **DefaultSqlExecutionStrategy** -t, mivel a **SqlAzureExecutionStrategy** használatát javasolja, ha átmeneti kivételek történnek – ami a tárgyalt helytelen viselkedéshez vezethet. A különböző újrapróbálkozási szabályzatokkal és az EF-vel kapcsolatos további információkért lásd: [a kapcsolatok rugalmassága az EF-ben](/ef/ef6/fundamentals/connection-resiliency/retry-logic).

#### <a name="constructor-rewrites"></a>Konstruktor-újraírások

A fenti kód a fenti példák szemléltetik az alkalmazáshoz szükséges alapértelmezett konstruktorok újraírásait, hogy az Adatfüggő útválasztást a Entity Framework használatával használhassa. A következő táblázat általánosítja ezt a megközelítést más konstruktorok számára.

| Aktuális konstruktor | Újraírható konstruktor az adatforgalomhoz | Alapkonstruktor | Jegyzetek |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(ShardMap, TKey) |DbContext (DbConnection, bool) |A kapcsolódásnak a szegmens Térkép és az Adatfüggő útválasztási kulcs függvényének kell lennie. Az EF használatával kell átadnia az automatikus kapcsolódást, és ehelyett a szegmenses leképezést kell használnia a kapcsolatok közvetítéséhez. |
| MyContext (karakterlánc) |ElasticScaleContext(ShardMap, TKey) |DbContext (DbConnection, bool) |A kötés a szegmens Térkép és az Adatfüggő útválasztási kulcs függvénye. A rögzített adatbázis-név vagy a kapcsolatok karakterlánca nem működik, mert a szegmenses Térkép átadja az ellenőrzést. |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |A rendszer létrehozza a hozzáférést a megadott szegmenses térképhez és a horizontális Felskálázási kulcshoz a megadott modellel. A lefordított modellt a rendszer átadja az alapszintű c'tor. |
| MyContext (DbConnection, bool) |ElasticScaleContext (ShardMap, TKey, bool) |DbContext (DbConnection, bool) |A kapcsolódást a szegmens térképből és a kulcsból kell kikövetkeztetni. Nem adható meg bemenetként (kivéve, ha a bemenet már használta a szegmenses térképet és a kulcsot). A rendszer átadja a logikai értéket. |
| MyContext (karakterlánc, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |A kapcsolódást a szegmens térképből és a kulcsból kell kikövetkeztetni. Nem adható meg bemenetként (kivéve, ha a bemenet a szegmenses térképet és a kulcsot használta). A lefordított modell át lett adva. |
| MyContext (ObjectContext, bool) |ElasticScaleContext (ShardMap, TKey, ObjectContext, bool) |DbContext (ObjectContext, bool) |Az új konstruktornak meg kell győződnie arról, hogy a ObjectContext átadott összes kapcsolatot átirányítja a rendszer a rugalmas skálázással kezelt kapcsolatok felé. A ObjectContexts részletes témája a jelen dokumentum hatókörén kívül esik. |
| MyContext (DbConnection, DbCompiledModel, bool) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel, bool) |DbContext (DbConnection, DbCompiledModel, bool); |A kapcsolódást a szegmens térképből és a kulcsból kell kikövetkeztetni. A kapcsolatok nem adhatók meg bemenetként (kivéve, ha a bemenet már használta a szegmenses térképet és a kulcsot). A modell és a logikai érték átadása az alaposztály konstruktorának. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Szegmenses séma üzembe helyezése EF-Migrálás útján

Az automatikus séma-felügyelet a Entity Framework által biztosított kényelem. A rugalmas adatbázis-eszközöket használó alkalmazások kontextusában meg kívánja őrizni ezt a képességet, hogy automatikusan kiépítse a sémát az újonnan létrehozott szegmensekre, amikor az adatbázisok hozzáadódnak az alkalmazáshoz. Az elsődleges használati eset az, hogy növelje a kapacitást az adatszinten az EF-t használó horizontálisan használt alkalmazások esetében. Az EF-re épülő séma-felügyeleti funkciókra való támaszkodás csökkenti az adatbázis-adminisztrációs erőfeszítéseket egy EF-re épülő, horizontálisan használt alkalmazással.

Az EF-alapú Migrálás révén a séma a legjobban a nem **nyitott kapcsolatokon** működik. Ez ellentétben áll a rugalmas adatbázis-ügyfél API által biztosított nyitott kapcsolódásra támaszkodó, adatkezelési útválasztási forgatókönyvvel. Egy másik különbség a konzisztencia követelménye: Bár célszerű biztosítani, hogy az összes adatkapcsolattal ellátott útválasztási kapcsolat konzisztens legyen az egyidejű szegmenses leképezések kezelésével szemben, nem érinti a kezdeti séma központi telepítését egy olyan új adatbázisra, amely még nincs regisztrálva a szegmenses Térképben, és még nincs kiosztva a shardletek tárolásához. Így a forgatókönyvhöz hasonlóan rendszeres adatbázis-kapcsolatokra is támaszkodhat, az adatoktól függő útválasztással szemben.  

Ez egy olyan megközelítéshez vezet, ahol az EF-Migrálás révén a séma üzembe helyezése szorosan össze van kapcsolva az új adatbázisnak az alkalmazás szegmenses térképének szegmensként való regisztrálásával. Ez a következő előfeltételekre támaszkodik:

* Az adatbázis már létre lett hozva.
* Az adatbázis üres – nem rendelkezik felhasználói sémával és felhasználói adattal.
* Az adatbázis még nem érhető el a rugalmas adatbázis-ügyfél API-kkal az Adatfüggő útválasztáshoz.

Ezeknek az előfeltételeknek a végrehajtásához létrehozhat egy rendszeres, nem megnyitott **SqlConnection** , amelyekkel elindíthatja a séma központi telepítésének EF-alapú áttelepítéseit. A következő mintakód ezt a megközelítést mutatja be.

```csharp
// Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
// and kick off EF initialization of the database to deploy schema

public void RegisterNewShard(string server, string database, string connStr, int key)
{

    Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database));

    SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr);
    connStrBldr.DataSource = server;
    connStrBldr.InitialCatalog = database;

    // Go into a DbContext to trigger migrations and schema deployment for the new shard.
    // This requires an un-opened connection.
    using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString))
    {
        // Run a query to engage EF migrations
        (from b in db.Blogs
            select b).Count();
    }

    // Register the mapping of the tenant to the shard in the shard map.
    // After this step, data-dependent routing on the shard map can be used

    this.ShardMap.CreatePointMapping(key, shard);
}
```

Ez a példa azt a metódust mutatja be, amely a **RegisterNewShard** regisztrálja a szegmenst a szegmensek közötti térképen, telepíti a sémát az EF-Migrálás segítségével, és egy horizontális Felskálázási kulcs hozzárendelését tárolja a szegmensben. A **DbContext** alosztály konstruktorán alapul (a mintában szereplő **ElasticScaleContext** ), amely egy SQL-kapcsolatot megadó karakterláncot használ bemenetként. A konstruktor kódja azonnal továbbítva van, ahogy az alábbi példában látható:

```csharp
// C'tor to deploy schema and migrations to a new shard
protected internal ElasticScaleContext(string connectionString)
    : base(SetInitializerForConnection(connectionString))
{
}

// Only static methods are allowed in calls into base class c'tors
private static string SetInitializerForConnection(string connectionString)
{
    // You want existence checks so that the schema can get deployed
    Database.SetInitializer<ElasticScaleContext<T>>(
new CreateDatabaseIfNotExists<ElasticScaleContext<T>>());

    return connectionString;
}
```

Az egyik lehetséges, hogy használta az alaposztálytól örökölt konstruktor verzióját. A kódnak azonban biztosítania kell, hogy a csatlakozáskor a rendszer az EF alapértelmezett inicializáló használja. Ezért a statikus metódus rövid lefoglalása, mielőtt az alaposztály konstruktorában meghívja a kapcsolatot megadó karakterláncot. Vegye figyelembe, hogy a szegmensek regisztrációjának egy másik alkalmazás-tartományban vagy folyamatban kell futnia, hogy az EF-hez tartozó inicializálási beállítások ne legyenek ütköznek.

## <a name="limitations"></a>Korlátozások

A jelen dokumentumban ismertetett megközelítések néhány korlátozást foglalnak magukban:

* Az **LocalDb** -t használó EF-alkalmazásoknak először rendszeres SQL Server adatbázisba kell áttérniük a rugalmas adatbázis-ügyféloldali kódtár használata előtt. Az alkalmazások horizontális méretezéssel történő horizontális felskálázása nem lehetséges a **LocalDb**. Vegye figyelembe, hogy a fejlesztés továbbra is használhatja a **LocalDb**-t.
* Az alkalmazásnak az adatbázis-sémát érintő változásait az összes szegmensen el kell végezni az EF-Migrálás során. Ennek a dokumentumnak a mintakód nem mutatja be ennek módját. Érdemes lehet egy ConnectionString paraméterrel Update-Database használni az összes szegmensre való iterációhoz. vagy bontsa ki a T-SQL-szkriptet a függőben lévő áttelepítéshez Update-Database használatával a-script kapcsolóval, és alkalmazza a T-SQL-szkriptet a szegmensekre.  
* A kérést a rendszer feltételezi, hogy az összes adatbázis-feldolgozás egy szegmensen belül található, amelyet a kérelem által biztosított horizontális kulcs azonosít. Ez a feltételezés azonban nem mindig igaz. Például, ha nem lehet elérhetővé tenni egy horizontális Felskálázási kulcsot. Ennek megoldásához az ügyféloldali kódtár biztosítja a **MultiShardQuery** osztályt, amely egy kapcsolati absztrakciót valósít meg több szegmensben való lekérdezéshez. A **MultiShardQuery** és az EF együttes használatának megismerése a jelen dokumentum hatókörén kívül esik

## <a name="conclusion"></a>Összegzés

A jelen dokumentumban ismertetett lépések végrehajtásával az EF-alkalmazások a rugalmas adatbázis ügyféloldali függvénytárát használhatják az Adatfüggő útválasztáshoz az EF-alkalmazásban használt **DbContext** alosztályok újrabontásával. Ez korlátozza azoknak a helyeknek a módosításait, amelyekben a **DbContext** -osztályok már léteznek. Emellett az EF-alkalmazások továbbra is kihasználhatják az automatikus séma telepítését azáltal, hogy összekapcsolják a szükséges EF-áttelepítéseket meghívó lépéseket az új szegmensek és leképezések a szegmenses térképen való regisztrálásával.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png