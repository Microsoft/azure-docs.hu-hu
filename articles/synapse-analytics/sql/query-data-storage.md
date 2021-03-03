---
title: Adattárolás lekérdezése kiszolgáló nélküli SQL-készlettel
description: Ez a cikk azt ismerteti, hogyan lehet lekérdezni az Azure Storage-t a kiszolgáló nélküli SQL Pool-erőforrás használatával az Azure szinapszis Analytics szolgáltatásban.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: d299afca0bd8070a1da738e02812b64c41a7101c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675056"
---
# <a name="query-storage-files-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Tárolási fájlok lekérdezése kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analyticsben

A kiszolgáló nélküli SQL-készlet lehetővé teszi, hogy lekérdezze az adatait a saját adattóban. Egy T-SQL-lekérdezési felületet kínál, amely a félig strukturált és a strukturálatlan adatlekérdezéseket is tartalmazza. A lekérdezéshez a következő T-SQL-szempontok támogatottak:

- Teljes [kijelölés](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) felületi terület, beleértve az [SQL-függvények és-operátorok](overview-features.md)többségét.
- KÜLSŐ tábla létrehozása SELECT ([CETAS](develop-tables-cetas.md)) – létrehoz egy [külső táblát](develop-tables-external-tables.md) , majd párhuzamosan exportálja a Transact-SQL SELECT utasítás eredményeit az Azure Storage-ba.

További információ a mi és a jelenleg nem támogatott kapcsolatban: a [kiszolgáló nélküli SQL-készlet áttekintő](on-demand-workspace-overview.md) cikke, vagy a következő cikkek:
- [Tárterület-hozzáférés fejlesztése](develop-storage-files-overview.md) , amelyből megtudhatja, hogyan lehet a [külső tábla](develop-tables-external-tables.md) és a [OpenRowset](develop-openrowset.md) függvény használatával beolvasni az adatait a tárolóból.
- A [tárterület-hozzáférés vezérlése](develop-storage-files-storage-access-control.md) , amelyből megtudhatja, hogyan engedélyezheti a szinapszis SQL számára a tárhely ELÉRÉSét sas-hitelesítés vagy a munkaterület felügyelt identitása segítségével.

## <a name="overview"></a>Áttekintés

Az Azure Storage-fájlokban található adatlekérdezés zökkenőmentes működésének támogatásához a kiszolgáló nélküli SQL-készlet a [OpenRowset](develop-openrowset.md) függvényt használja további képességekkel:

- [Több fájl vagy mappa lekérdezése](#query-multiple-files-or-folders)
- [PARKETTA fájlformátum](#query-parquet-files)
- [CSV és tagolt szöveg lekérdezése (mező lezáró, sor lezáró, escape-karakter)](#query-csv-files)
- [Oszlopok kiválasztott részhalmazának olvasása](#read-a-chosen-subset-of-columns)
- [Séma-következtetés](#schema-inference)
- [filename függvény](#filename-function)
- [filepath függvény](#filepath-function)
- [Összetett típusok és beágyazott vagy ismétlődő adatstruktúrák használata](#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="query-parquet-files"></a>PARQUEt-fájlok lekérdezése

A parketta-forrásadatok lekérdezéséhez használja a FORMAT = "PARQUEt"

```syntaxsql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net//mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (C1 int, C2 varchar(20), C3 varchar(max)) as rows
```

A használati példákért tekintse át a [lekérdezési parketta fájljait](query-parquet-files.md) ismertető cikket.

## <a name="query-csv-files"></a>CSV-fájlok lekérdezése

CSV-forrásadatok lekérdezéséhez használja a FORMAT = "CSV" formátumot. A CSV-fájl sémáját a függvény részeként is megadhatja a `OPENROWSET` CSV-fájlok lekérdezése során:

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.csv', FORMAT = 'CSV', PARSER_VERSION='2.0') 
WITH (C1 int, C2 varchar(20), C3 varchar(max)) as rows
```

Néhány további lehetőség is használható az elemzési szabályok egyéni CSv-formátumra való beállításához:
- A ESCAPE_CHAR = "char" a fájlban szereplő karaktert határozza meg, amely a fájlban lévő összes elválasztó érték kiszökésére szolgál. Ha az Escape-karaktert a saját maga vagy az elválasztó értékek egyike követi, az escape-karakter eldobása az érték beolvasása közben történik.
A ESCAPE_CHAR paraméter akkor lesz alkalmazva, ha a FIELDQUOTE vagy nincs engedélyezve. A rendszer nem használja fel az idézett karakter megmenekülésére. Az idézőjel karakternek egy másik idézőjel karakterrel kell megszöknie. Az idézőjel karakter csak akkor szerepelhet az oszlop értékén belül, ha az érték idézőjelekkel van ellátva.
- A FIELDTERMINATOR = ' field_terminator ' meghatározza a használni kívánt lezáró mezőt. Az alapértelmezett lezáró mező egy vessző ("**,**")
- A ROWTERMINATOR = ' row_terminator ' megadja a használandó sort. Az alapértelmezett sor lezáró egy sortörési karakter: **\r\n**.

## <a name="file-schema"></a>Fájl sémája

A szinapszis SQL SQL nyelve lehetővé teszi a fájl sémájának megadását a függvény részeként, illetve az `OPENROWSET` oszlopok beolvasását, vagy az oszlopok összes részhalmazát, vagy megpróbálja automatikusan meghatározni a fájl típusú oszlop típusát a Schema következtetés használatával.

### <a name="read-a-chosen-subset-of-columns"></a>Oszlopok kiválasztott részhalmazának olvasása

Az olvasni kívánt oszlopok megadásához megadhat egy opcionális záradékot az `OPENROWSET` utasításon belül.

- Ha CSV-adatfájlok vannak, az összes oszlop olvasásához adja meg az oszlopnevek és az adattípusok nevét. Ha az oszlopok egy részhalmazát szeretné használni, a sorszámok használatával válassza ki az oszlopokat a származó adatfájlokból a sorszám alapján. Az oszlopokat a sorszám megjelölése fogja kötni.
- Ha vannak parketta-adatfájlok, adjon meg olyan oszlopnevek, amelyek egyeznek a kezdeményező adatfájlokban lévő oszlopnevek. Az oszlopok név szerint lesznek kötve.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (
      C1 int, 
      C2 varchar(20),
      C3 varchar(max)
) as rows
```

Minden oszlop esetében meg kell adnia az oszlopnév nevet és a Type in `WITH` záradékot.
Minták esetében tekintse meg a [CSV-fájlok olvasása az összes oszlop megadása nélkül](query-single-csv-file.md#return-a-subset-of-columns)című témakört.

## <a name="schema-inference"></a>Séma-következtetés

Ha kihagyja a WITH záradékot az `OPENROWSET` utasításból, utasíthatja a szolgáltatást, hogy automatikusan érzékelje (következteti) a sémát az alapul szolgáló fájlokból.

> [!NOTE]
> Ez jelenleg csak a PARQUEt fájlformátum esetében működik.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
```

Győződjön meg arról, hogy a [megfelelő késleltetett adattípusok](best-practices-sql-on-demand.md#check-inferred-data-types) használatos az optimális teljesítmény érdekében. 

## <a name="query-multiple-files-or-folders"></a>Több fájl vagy mappa lekérdezése

Ha T-SQL-lekérdezést szeretne futtatni egy mappa vagy mappák készletén belül, miközben egyetlen entitásként vagy sorhalmazként kezeli őket, adjon meg egy mappát vagy egy mintát (helyettesítő karakterek használatával) egy fájlon vagy mappán keresztül.

A következő szabályok érvényesek:

- A minták a könyvtár elérési útjának vagy a fájlnevek egy részében is megjelenhetnek.
- Több minta is szerepelhet ugyanabban a címtárbeli lépésben vagy fájlnévben.
- Ha több helyettesítő karakter is van, akkor az összes egyező elérési úton található fájlok szerepelni fognak az eredményül kapott fájlban.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/myroot/*/mysubfolder/*.parquet', FORMAT = 'PARQUET' ) as rows
```

A használati példákat a [lekérdezési mappákban és több fájlban](query-folders-multiple-csv-files.md) találja.

## <a name="file-metadata-functions"></a>Fájl metaadatainak függvények

### <a name="filename-function"></a>Filename függvény

Ez a függvény annak a fájlnak a nevét adja vissza, amelyből a sor származik. 

Adott fájlok lekérdezéséhez olvassa el a fájl- [specifikus fájlok lekérdezése](query-specific-files.md#filename) című cikket.

A visszatérési adattípus a következő: nvarchar (1024). Az optimális teljesítmény érdekében a filename függvény eredményét mindig a megfelelő adattípusra konvertálja. Ha a karakter adattípust használja, ügyeljen arra, hogy a megfelelő hossz legyen használatban.

### <a name="filepath-function"></a>Filepath függvény

Ez a függvény teljes elérési utat vagy az elérési út egy részét adja vissza:

- Ha paraméter nélkül hívja meg, a a fájl teljes elérési útját adja vissza, amelyből a sor származik.
- Ha paraméterrel hívja meg a metódust, az az elérési út azon részét adja vissza, amely megfelel a paraméterben megadott helyettesítő karakternek. Például az 1. paraméter értéke az elérési út azon részét fogja visszaadni, amely megfelel az első helyettesítő karakternek.

További információért olvassa el az [adott fájlok lekérdezése](query-specific-files.md#filepath) című cikk filepath című szakaszát.

A visszatérési adattípus a következő: nvarchar (1024). Az optimális teljesítmény érdekében a filepath függvény eredményét mindig a megfelelő adattípusra konvertálja. Ha a karakter adattípust használja, ügyeljen arra, hogy a megfelelő hossz legyen használatban.

## <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Összetett típusok és beágyazott vagy ismétlődő adatstruktúrák használata

A beágyazott vagy ismétlődő adattípusokban (például a [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) -fájlokban) tárolt adathalmazok zökkenőmentes használatának lehetővé tételéhez a kiszolgáló nélküli SQL-készlet hozzáadta a következő bővítményeket.

#### <a name="project-nested-or-repeated-data"></a>Beágyazott vagy ismétlődő projekt

Az adatok kivetítéséhez futtasson egy SELECT utasítást a beágyazott adattípusú oszlopokat tartalmazó parketta-fájlon. A kimenetben a beágyazott értékek a JSON-be lesznek szerializálva, és varchar (8000) SQL-adattípusként lesznek visszaadva.

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Részletesebb információkért tekintse meg a [lekérdezési parketta beágyazott típusai](query-parquet-nested-types.md#project-nested-or-repeated-data) című cikket a projekt beágyazott vagy ismétlődő adatok szakaszában.

#### <a name="access-elements-from-nested-columns"></a>Elemek elérése beágyazott oszlopokból

Egy beágyazott oszlop (például a struct) beágyazott elemeinek eléréséhez használja a "dot jelölést", hogy összefűzse a mezőneveket az elérési útra. Adja meg az elérési utat column_nameként a függvény WITH záradékában `OPENROWSET` .

A szintaxis kódrészlet példája a következő:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Alapértelmezés szerint a `OPENROWSET` függvény megegyezik a forrás mező nevével és elérési útjával a with záradékban megadott oszlopnevek alapján. Az azonos forrásban lévő parketta-fájl különböző beágyazási szintjein található elemek a WITH záradék használatával érhetők el.

**Visszatérési értékek**

- A függvény egy skaláris értéket ad vissza, például: int, decimális és varchar, a megadott elemből és a megadott elérési úton, a beágyazott Type csoportban nem szereplő összes parketta típushoz.
- Ha az elérési út egy beágyazott típusú elemre mutat, a függvény egy JSON-töredéket ad vissza a megadott elérési út felső elemétől kezdve. A JSON-töredék varchar (8000) típusú.
- Ha a tulajdonság nem található a megadott column_name, a függvény hibát ad vissza.
- Ha a tulajdonság nem található a megadott column_pathban, az [elérési út módjától](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true#PATHMODE)függően, a függvény hibát ad vissza, ha a szigorú vagy a null értéket adja meg LAX módban.

A lekérdezési minták esetében tekintse át a beágyazott oszlopok hozzáférési elemeit a [lekérdezési parketta beágyazott típusai](query-parquet-nested-types.md#read-properties-from-nested-object-columns) című cikkben.

#### <a name="access-elements-from-repeated-columns"></a>Elemek elérése ismétlődő oszlopokból

Ha ismétlődő oszlop elemeit szeretné elérni, például egy tömb vagy Térkép elemét, használja a [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) függvényt minden olyan skaláris elemnél, amelyet a projekthez és a biztosításához szükséges:

- Beágyazott vagy ismétlődő oszlop, az első paraméterként
- Egy [JSON-elérési út](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) , amely megadja az elérni kívánt elemet vagy tulajdonságot második paraméterként

A nem skaláris elemek ismétlődő oszlopokból való eléréséhez használja a [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?view=azure-sqldw-latest&preserve-view=true) függvényt minden olyan nem skaláris elemhez, amelyet a projekthez és a biztosításához szükséges:

- Beágyazott vagy ismétlődő oszlop, az első paraméterként
- Egy [JSON-elérési út](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) , amely megadja az elérni kívánt elemet vagy tulajdonságot második paraméterként

Lásd az alábbi szintaxist:

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Lekérdezési mintákat talál az elemek az ismétlődő oszlopokból való eléréséhez a [lekérdezési parketta beágyazott típusai](query-parquet-nested-types.md#access-elements-from-repeated-columns) cikkben.

## <a name="query-samples"></a>Példák lekérdezésekre

A különböző típusú adattípusok lekérdezésével kapcsolatos további információkért tekintse meg a minta lekérdezéseket.

### <a name="tools"></a>Eszközök

A lekérdezések kibocsátásához szükséges eszközök:
    - Azure Synapse Studio 
    - Azure Data Studio
    - Az SQL Server Management Studio

### <a name="demo-setup"></a>Bemutató beállítása

Első lépésként létre kell **hoznia egy adatbázist** , amelyen végre kell hajtania a lekérdezéseket. Ezután inicializálja az objektumokat a [telepítési parancsfájl](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) végrehajtásával az adatbázison. 

Ez a telepítési parancsfájl létrehozza az adatforrásokat, az adatbázis-hatókörrel rendelkező hitelesítő adatokat, valamint az ezekben a mintákban lévő adatok olvasásához használt külső fájlformátumokat.

> [!NOTE]
> Az adatbázisokat csak a metaadatok megtekintésére használják, nem a tényleges adatokhoz.  Jegyezze fel a használni kívánt adatbázis nevét, később szüksége lesz rá.

```sql
CREATE DATABASE mydbname;
```

### <a name="provided-demo-data"></a>Megadott bemutatói információk

A demo-adat a következő adatkészleteket tartalmazza:

- New York-i taxi – Yellow taxi Trip Records – a nyilvános NYC-adathalmaz része CSV-ben és parketta formátumban
- Populációs adatkészlet CSV formátumban
- Parketta-fájlok beágyazott oszlopokkal
- Könyvek JSON formátumban

| Mappa elérési útja                                                  | Leírás                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| CSV                                                        | CSV formátumú adatszülő mappa                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Különböző CSV formátumú adatfájlokkal rendelkező mappák. |
| /csv/taxi/                                                   | A New York-i nyilvános adatfájlok mappája CSV formátumban              |
| Parquet                                                    | Az adatszülő mappa a parketta formátumban                     |
| /parquet/taxi                                                | A New York-i nyilvános adatfájlok parkettás formátumban, évről évre, a kaptár/Hadoop particionálási sémával particionálva. |
| /parquet/nested/                                             | Parketta-fájlok beágyazott oszlopokkal                     |
| JSON                                                       | JSON formátumú adatszülő mappa                        |
| /json/books/                                                 | Könyvekből származó JSON-fájlok                                   |


## <a name="next-steps"></a>Következő lépések

A különböző fájltípusok lekérdezésével, valamint a nézetek létrehozásával és használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

- [CSV-fájlok lekérdezése](query-single-csv-file.md)
- [Parquet-fájlok lekérdezése](query-parquet-files.md)
- [JSON-fájlok lekérdezése](query-json-files.md)
- [Beágyazott értékek lekérdezése](query-parquet-nested-types.md)
- [Mappák és több CSV-fájl lekérdezése](query-folders-multiple-csv-files.md)
- [Fájl metaadatainak használata a lekérdezésekben](query-specific-files.md)
- [Nézetek létrehozása és használata](create-use-views.md)
