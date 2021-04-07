---
title: Adatbázis felskálázása
description: A ShardMapManager és a rugalmas adatbázis-ügyféloldali kódtár használata
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 03bf92a2d77fb262ed6506bf18c0d27006e435a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201199"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Adatbázisok horizontális felskálázása a szegmenses Térkép kezelőjével
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A Azure SQL Database adatbázisok egyszerű horizontális felskálázásához használjon egy szegmens Térkép-kezelőt. A szegmenses Térkép-kezelő egy olyan speciális adatbázis, amely globális leképezési információkat tart fenn a szegmensben lévő összes szegmensről (adatbázisról). A metaadatok lehetővé teszik, hogy az alkalmazások a megfelelő adatbázishoz kapcsolódjanak a horizontális Felskálázási **kulcs** értéke alapján. Emellett a készletben lévő összes szegmens olyan térképeket is tartalmaz, amelyek nyomon követik a helyi szegmensek ( **shardletek**) adatkészleteit.

![A szilánkleképezés kezelése](./media/elastic-scale-shard-map-management/glossary.png)

A térképek összeépítésének megértése elengedhetetlen a szegmensek közötti Térkép-felügyelethez. Ezt a ShardMapManager osztály ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) használatával végezheti el, amely a [Elastic Database ügyféloldali függvénytárában](elastic-database-client-library.md) található a szegmens térképek kezeléséhez.  

## <a name="shard-maps-and-shard-mappings"></a>Szegmenses térképek és szegmens megfeleltetések

Minden szegmens esetében ki kell választania a létrehozandó szegmens-hozzárendelés típusát. A választás az adatbázis-architektúrától függ:

1. Egyetlen bérlő/adatbázis  
2. Több bérlő/adatbázis (két típus):
   1. Lista megfeleltetése
   2. Tartomány-hozzárendelés

Egybérlős modell esetén hozzon létre egy **lista-hozzárendelési** szegmens térképet. Az egybérlős modell bérlőként egy adatbázist rendel hozzá. Ez egy hatékony modell az SaaS-fejlesztők számára, mivel leegyszerűsíti a szegmenses hozzárendelések kezelését.

![Lista megfeleltetése][1]

A több-bérlős modell több bérlőt rendel egy adott adatbázishoz (és több adatbázisból is terjesztheti a bérlők csoportjait). Akkor használja ezt a modellt, ha várható, hogy az egyes bérlők kis mennyiségű adatra van szükségük. Ebben a modellben a **tartomány-hozzárendelés** használatával rendeljen hozzá bérlőket egy adatbázishoz.

![Tartomány-hozzárendelés][2]

Akár több-bérlős adatbázis-modellt is alkalmazhat egy *lista-hozzárendeléssel* , hogy több bérlőt rendeljen hozzá egy adott adatbázishoz. A DB1 például az 1. és az 5. bérlői azonosító adatainak tárolására szolgál, a DB2 pedig a 7. Bérlő és a bérlő 10 adatait tárolja.

![Több bérlő egyetlen ADATBÁZISon][3]

### <a name="supported-types-for-sharding-keys"></a>Felskálázási kulcsok támogatott típusai

A rugalmas méretezés a következő típusokat támogatja a horizontális Felskálázási kulcsokként:

| .NET | Java |
| --- | --- |
| egész szám |egész szám |
| hosszú |hosszú |
| guid |uuid |
| bájt []  |bájt [] |
| dátum/idő | időbélyeg |
| időtartomány | duration|
| DateTimeOffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Szegmensek listája és tartománya

**A szegmenses** térképek létrehozhatók az egyes horizontális Felskálázási kulcsok listájával, vagy a kiépíthető **kulcsok értékének tartománya** alapján.

### <a name="list-shard-maps"></a>Szegmens térképek listázása

A szegmensek **shardletek** **tartalmaznak,** és a shardletek a szegmensekre való hozzárendelését egy szegmenses Térkép tartja karban. A szegmensek **listájának leképezése** a shardletek és a szegmensként szolgáló adatbázisok azonosítására szolgáló egyedi kulcs értékeinek társítása.  A **lista-hozzárendelések** explicitek, és a különböző kulcs típusú értékeket ugyanahhoz az adatbázishoz lehet hozzárendelni. Például a Key Value 1 az A adatbázisra, A 3. és a 6. kulcs pedig a B adatbázisra mutat.

| Kulcs | Szegmens helye |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Tartományhoz tartozó szegmens térképek

A **tartományon belüli szegmensek leképezésében** a kulcs tartományát egy pár **[alacsony érték, magas érték)** jellemzi, ahol az *alacsony érték* a tartomány minimális kulcsa, a *magas érték* pedig az első érték, amely a tartománynál nagyobb.

Például a **[0, 100)** az összes olyan egész számot tartalmazza, amely nagyobb vagy egyenlő, mint 0, és kisebb, mint 100. Vegye figyelembe, hogy több tartomány ugyanarra az adatbázisra mutathat, és a különálló tartományok (például a [100 200) és a [400 600) egyaránt a C adatbázisra mutatnak a következő példában.)

| Kulcs | Szegmens helye |
| --- | --- |
| [1, 50) |Database_A |
| [50 100) |Database_B |
| [100 200) |Database_C |
| [400 600) |Database_C |
| ... |... |

A fent látható táblák mindegyike egy **ShardMap** objektum fogalmi példája. Minden sor egy egyszerűsített példa az egyes **PointMapping** (a szegmensek felsorolása) vagy a **RangeMapping** (tartományhoz tartozó felosztási leképezés) objektumra.

## <a name="shard-map-manager"></a>Szilánkleképezés-kezelő

Az ügyféloldali függvénytárban a szegmens Térkép-kezelő a szegmens térképek gyűjteménye. A **ShardMapManager** -példány által kezelt adat három helyen tart:

1. **Globális szegmenses Térkép (GSM)**: megadhat egy adatbázist, amely az összes szegmensének térképéhez és leképezéséhez tartozó tárházként szolgál. A speciális táblák és tárolt eljárások automatikusan létrejönnek az adatok kezeléséhez. Ez általában egy kisméretű adatbázis, és nem használható az alkalmazás más igényeihez. A táblák egy **__ShardManagement** nevű speciális sémában találhatók.
2. **Helyi szegmenses Térkép (LSM)**: minden olyan adatbázis, amelyet szegmensként határoz meg, úgy módosul, hogy több kisméretű táblázatot és speciális tárolt eljárásokat tartalmazzon, amelyek az adott szegmensre vonatkozó, a szegmensekre vonatkozó térképi információkat tartalmaznak és felügyelnek. Ezek az információk redundánsak a GSM-vel kapcsolatos információkkal, és lehetővé teszik az alkalmazás számára a gyorsítótárazott szegmensek leképezési adatainak érvényesítését anélkül, hogy terhelést kellene elhelyezni a GSM-ben. az alkalmazás a LSM használatával határozza meg, hogy a gyorsítótárazott hozzárendelések továbbra is érvényesek-e. Az egyes szegmensek LSM megfelelő táblázatok a séma **__ShardManagement** is.
3. **Alkalmazás-gyorsítótár**: a **ShardMapManager** -objektumhoz hozzáférő egyes alkalmazás-példányok a leképezések helyi memóriában tárolt gyorsítótárát kezelik. A nemrég lekért útválasztási adatokat tárolja.

## <a name="constructing-a-shardmapmanager"></a>ShardMapManager építése

A **ShardMapManager** -objektumok gyári ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)) minta használatával készültek. A **ShardMapManagerFactory. GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) metódus a hitelesítő adatokat (beleértve a GSM-t tároló kiszolgáló nevét és az adatbázis nevét) **, és egy** **ShardMapManager** egy példányát adja vissza.  

Vegye **figyelembe:** Az **ShardMapManager** csak egyszer kell példányként létrehozni az alkalmazások inicializálási kódjában. Az ShardMapManager további példányainak létrehozása ugyanabban az alkalmazás-tartományban az alkalmazás megnövekedett memóriáját és CPU-kihasználtságát eredményezi. Egy **ShardMapManager** tetszőleges számú szegmenses térképet tartalmazhat. Habár számos alkalmazás esetében elegendő lehet egy szegmenses Térkép, előfordulhat, hogy a különböző adatbázisok különböző készletei eltérő sémához vagy egyedi célokra használatosak. Ezekben az esetekben előfordulhat, hogy több szegmens Térkép is előnyösebb.

Ebben a kódban az alkalmazás megpróbál megnyitni egy meglévő **ShardMapManager** a TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.trygetsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) metódussal). Ha a globális **ShardMapManager** (GSM) jelképező objektumok még nem léteznek az adatbázisban, az ügyféloldali kódtár a CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.createsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) metódussal hozza létre őket.

```Java
// Try to get a reference to the Shard Map Manager in the shardMapManager database.
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager = null;
boolean shardMapManagerExists = ShardMapManagerFactory.tryGetSqlShardMapManager(shardMapManagerConnectionString,ShardMapManagerLoadPolicy.Lazy, refShardMapManager);
shardMapManager = refShardMapManager.argValue;

if (shardMapManagerExists) {
    ConsoleUtils.writeInfo("Shard Map %s already exists", shardMapManager);
}
else {
    // The Shard Map Manager does not exist, so create it
    shardMapManager = ShardMapManagerFactory.createSqlShardMapManager(shardMapManagerConnectionString);
    ConsoleUtils.writeInfo("Created Shard Map %s", shardMapManager);
}
```

```csharp
// Try to get a reference to the Shard Map Manager via the Shard Map Manager database.  
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager;
bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString,
                                        ShardMapManagerLoadPolicy.Lazy,
                                        out shardMapManager);

if (shardMapManagerExists)
{
    Console.WriteLine("Shard Map Manager already exists");
}
else
{
    // Create the Shard Map Manager.
    ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
    Console.WriteLine("Created SqlShardMapManager");

    shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString,
            ShardMapManagerLoadPolicy.Lazy);

// The connectionString contains server name, database name, and admin credentials for privileges on both the GSM and the shards themselves.
}
```

A .NET verzióban a PowerShell használatával hozhat létre új szegmenses Térkép-kezelőt. [Itt](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)egy példa érhető el.

## <a name="get-a-rangeshardmap-or-listshardmap"></a>RangeShardMap vagy ListShardMap beszerzése

A szegmensek közötti Térkép-kezelő létrehozása után a[RangeShardMap (](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap)[Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap) [, .net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) [),](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)a TryGetListShardMap ( [Java, .net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1) [),](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)illetve a GetShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap)[, .net](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap) [) metódus](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)használatával szerezheti be[a következőt](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap):.

```Java
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
static <T> RangeShardMap<T> createOrGetRangeShardMap(ShardMapManager shardMapManager,
            String shardMapName,
            ShardKeyType keyType) {
    // Try to get a reference to the Shard Map.
    ReferenceObjectHelper<RangeShardMap<T>> refRangeShardMap = new ReferenceObjectHelper<>(null);
    boolean isGetSuccess = shardMapManager.tryGetRangeShardMap(shardMapName, keyType, refRangeShardMap);
    RangeShardMap<T> shardMap = refRangeShardMap.argValue;

    if (isGetSuccess && shardMap != null) {
        ConsoleUtils.writeInfo("Shard Map %1$s already exists", shardMap.getName());
    }
    else {
        // The Shard Map does not exist, so create it
        try {
            shardMap = shardMapManager.createRangeShardMap(shardMapName, keyType);
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        ConsoleUtils.writeInfo("Created Shard Map %1$s", shardMap.getName());
    }

    return shardMap;
}
```

```csharp
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
{
    // Try to get a reference to the Shard Map.
    RangeShardMap<T> shardMap;
    bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

    if (shardMapExists)
    {
        ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
    }
    else
    {
        // The Shard Map does not exist, so create it
        shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
        ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
    }

    return shardMap;
}
```

### <a name="shard-map-administration-credentials"></a>A szegmens Térkép felügyeleti hitelesítő adatai

A szegmens térképek felügyeletére és kezelésére szolgáló alkalmazások különböznek azoktól, amelyek a szegmens térképeket használják a kapcsolatok irányításához.

A szegmenses térképek felügyeletéhez (szegmensek, szegmens térképek, szegmens leképezések stb.) a **ShardMapManager** példányát olyan hitelesítő adatok használatával kell létrehoznia, **amelyek olvasási/írási jogosultsággal rendelkeznek mind a GSM-adatbázis,** mind pedig a szegmensként szolgáló minden adatbázis esetében. A hitelesítő adatoknak lehetővé kell tenniük, hogy az írásokat a GSM-és LSM lévő táblákon is meg lehessen adni vagy módosítani, valamint a LSM-táblákat az új szegmenseken.  

Tekintse [meg az Elastic Database ügyféloldali függvénytár eléréséhez használt hitelesítő adatokat](elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Csak az érintett metaadatok

A **ShardMapManager** adatok feltöltéséhez vagy módosításához használt metódusok nem módosítják magukat a szegmensekben tárolt felhasználói adatokat. Például a **CreateShard**, a **DeleteShard**, a **UpdateMapping** stb. hasonló metódusok csak a szilánkokra vonatkozó térképi metaadatokat érintik. Nem távolítják el, nem vehetik fel vagy nem változtathatják meg a szegmensekben található felhasználói adategységeket. Ehelyett ezeket a metódusokat úgy tervezték, hogy a tényleges adatbázisok létrehozásához vagy eltávolításához, illetve az egyik szegmensből a másikra történő áthelyezést végző különálló műveletekkel legyenek felhasználva.  (A rugalmas adatbázis-eszközökhöz mellékelt **felosztott egyesítési** eszköz ezeket az API-kat használja, valamint a szegmensek közötti tényleges adatáthelyezést.) Lásd: [Méretezés a Elastic Database felosztási-egyesítési eszköz használatával](elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Adatfüggő útválasztás

A szegmenses Térkép kezelője olyan alkalmazásokban használatos, amelyekhez adatbázis-kapcsolatokra van szükség az alkalmazásspecifikus adatműveletek végrehajtásához. Ezeket a kapcsolatokat a megfelelő adatbázishoz kell társítani. Ez az **adatkezelési útvonal**. Ezeknek az alkalmazásoknak a használatával a gyári kiosztású Térkép-kezelő objektumot egy olyan hitelesítő adatokkal hozza létre, amelyek csak olvasási hozzáféréssel rendelkeznek a GSM-adatbázishoz. A későbbi kapcsolatokra vonatkozó egyedi kérések biztosítják a megfelelő szegmens adatbázishoz való csatlakozáshoz szükséges hitelesítő adatokat.

Vegye figyelembe, hogy ezek az alkalmazások (a csak olvasható hitelesítő adatokkal megnyitott **ShardMapManager** használatával) nem módosíthatják a térképeket és a leképezéseket. Ezeknek az igényeknek megfelelően hozzon létre olyan felügyeleti specifikus alkalmazásokat vagy PowerShell-parancsfájlokat, amelyek a korábban tárgyalt magasabb szintű jogosultságú hitelesítő adatokat biztosítanak. Tekintse [meg az Elastic Database ügyféloldali függvénytár eléréséhez használt hitelesítő adatokat](elastic-scale-manage-credentials.md).

További információ: [Adatfüggő útválasztás](elastic-scale-data-dependent-routing.md).

## <a name="modifying-a-shard-map"></a>Szegmens Térkép módosítása

A szegmenses Térkép többféleképpen módosítható. Az alábbi módszerek mindegyike módosítja a szegmenseket és a hozzájuk tartozó hozzárendeléseket leíró metaadatokat, de nem módosítja fizikailag a szegmensekben lévő adatokat, és nem hozza létre vagy törli a tényleges adatbázisokat.  Előfordulhat, hogy az alább ismertetett szegmens-Térkép egyes műveleteit össze kell hangolni olyan rendszergazdai műveletekkel, amelyek fizikailag áthelyezik az adatok átvitelét, vagy amelyek a szegmensként szolgáló adatbázisok hozzáadására és eltávolítására szolgálnak.

Ezek a módszerek együttesen működnek, mint a felosztott adatbázis-környezetekben tárolt adatmennyiségek teljes eloszlásának módosítására szolgáló építőelemek.  

* Szegmensek hozzáadása vagy eltávolítása: használja a shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap) [, .net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap)) osztály **CreateShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)) és **DeleteShard** [(Java,](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.deleteshard) [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard)).
  
    Ezen műveletek végrehajtásához már léteznie kell a célként megadott szegmenst jelképező kiszolgálónak és adatbázisnak. Ezek a módszerek nincsenek hatással az adatbázisokra, csak a szegmensek közötti térképen található metaadatokon.
* A szegmensekre leképezett pontok vagy tartományok létrehozásához vagy eltávolításához használja a **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.net](/previous-versions/azure/dn841993(v=azure.100))),  a DeleteMapping [(Java,](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.deletemapping) [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) és a [ListShardMap (Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap) [,](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1) [.net)](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)osztályt **, valamint a (Java** [, .net)](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)osztály [RangeShardMapping (Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap)[, .net](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap.createpointmapping)) osztályát.
  
    Számos különböző pont vagy tartomány képezhető le ugyanahhoz a szegmenshez. Ezek a módszerek csak a metaadatokat érintik – nem érintik azokat az adatokat, amelyek esetleg már szerepelnek a szegmensekben. Ha a **DeleteMapping** -műveletekkel való konzisztencia érdekében el kell távolítani az adatait az adatbázisból, ezeket a műveleteket külön kell végrehajtani, de a módszerek használatával együtt.  
* A meglévő tartományok két értékre való felosztásához vagy a szomszédos tartományok egyesítéséhez: használja a **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.splitmapping), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) és a **MergeMappings** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.mergemappings), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Vegye figyelembe, hogy a felosztási és egyesítési műveletek **nem változtatják meg azt a szegmenst, amelybe a fő értékek le vannak képezve**. A felosztott két részre bontja a meglévő tartományt, de mindkettőt ugyanahhoz a szegmenshez leképezve hagyja. Az egyesítés két szomszédos tartományon működik, amelyek már ugyanahhoz a szegmenshez vannak rendelve, coalescing őket egyetlen tartományba.  A pontok vagy tartományok egymás közötti áthelyezését össze kell hangolni a **UpdateMapping** és a tényleges adatáthelyezés együttes használatával.  A rugalmas adatbázis-eszközök részét képező **felosztási/egyesítési** szolgáltatással összehangolhatja a szegmensek közötti Térkép változásait az adatáthelyezéssel, ha a mozgásra van szükség.
* Az egyes pontok vagy tartományok ismételt leképezése (vagy áthelyezése) a különböző szegmensekre: a **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.updatemapping), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) használata.  
  
    Mivel előfordulhat, hogy az adatoknak az egyik szegmensről a másikra kell áthelyezniük, hogy konzisztensek legyenek a **UpdateMapping** -műveletekkel, ezt a módszert külön kell végrehajtani, de az ilyen módszerek használatával együtt.

* A leképezések online és offline állapotba helyezéséhez használja a **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingoffline), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) és a **MarkMappingOnline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingonline), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) lehetőséget a leképezés online állapotának szabályozásához.
  
    A szegmenses hozzárendelések bizonyos műveletei csak akkor engedélyezettek, ha a leképezés "offline" állapotban van, beleértve a **UpdateMapping** és a **DeleteMapping**. Ha a leképezés offline állapotú, a leképezésben szereplő egyik kulcson alapuló, Adatfüggő kérelem hibát ad vissza. Emellett, ha a tartomány először offline állapotba kerül, a rendszer automatikusan lekéri az érintett szegmens összes kapcsolatát, hogy megakadályozza az inkonzisztens vagy hiányos eredményeket a módosult tartományokra irányított lekérdezéseknél.

A leképezések nem változtatható objektumok a .NET-ben.  Az összes fenti módszer, amely módosítja a leképezéseket, érvényteleníti a kódban lévő rájuk mutató hivatkozásokat is. Annak érdekében, hogy könnyebb legyen ellátni a leképezés állapotát megváltoztató műveletek folyamatait, a leképezést módosító összes módszer új leképezési referenciát ad vissza, így a műveletek láncba helyezhetők. Ha például törölni szeretne egy meglévő leképezést a shardmap SM-ben, amely a 25. kulcsot tartalmazza, a következőket végezheti el:

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Szegmens hozzáadása

Az alkalmazásoknak gyakran új szegmenseket kell felvenniük ahhoz, hogy kezelni tudják az új kulcsokból vagy a kulcsok tartományokból várt adatok kezelését. Előfordulhat például, hogy egy, a bérlői azonosító által áthelyezett alkalmazásnak új szegmenst kell kiépítenie, vagy a havonta felhasznált adatokat egy új, minden új hónap kezdete előtt létre kell hozni.

Ha a kulcsfontosságú értékek új tartománya még nem része egy meglévő leképezésnek, és nincs szükség adatáthelyezésre, egyszerűen hozzáadhatja az új szegmenst, és az új kulcsot vagy tartományt hozzárendelheti a szegmenshez. Az új szegmensek hozzáadásával kapcsolatos részletekért lásd: [új szegmens hozzáadása](elastic-scale-add-a-shard.md).

Az adatáthelyezést igénylő forgatókönyvek esetében azonban a felosztott egyesítés eszközre van szükség a szegmensek közötti adatáthelyezés előkészítéséhez a szükséges szegmenses Térkép-frissítésekkel együtt. A felosztás-egyesítés eszköz használatáról további részleteket a [Split-Merge áttekintése](elastic-scale-overview-split-and-merge.md) című témakörben talál.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-shard-map-management/listmapping.png
[2]: ./media/elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/elastic-scale-shard-map-management/multipleonsingledb.png
