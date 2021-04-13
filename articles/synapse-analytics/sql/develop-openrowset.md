---
title: A OPENROWSET használata kiszolgáló nélküli SQL-készletben
description: Ez a cikk a kiszolgáló nélküli SQL-készletben található OPENROWSET szintaxisát ismerteti, és ismerteti az argumentumok használatát.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: c37f6d89d5ebd3e18177db8add048739a62c883f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307945"
---
# <a name="how-to-use-openrowset-using-serverless-sql-pool-in-azure-synapse-analytics"></a>A OPENROWSET használata kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analyticsben

A `OPENROWSET(BULK...)` függvény lehetővé teszi a fájlok elérését az Azure Storage-ban. `OPENROWSET` a függvény egy távoli adatforrás tartalmát olvassa be (például fájl), és a tartalmat sorok halmaza adja vissza. A kiszolgáló nélküli SQL-készlet erőforráson belül a OPENROWSET tömeges sorhalmaz szolgáltatója a OPENROWSET függvény meghívásával és a TÖMEGES beállítás megadásával érhető el.  

A `OPENROWSET` függvény hivatkozhat a `FROM` lekérdezés záradékára úgy, mintha a tábla neve lenne `OPENROWSET` . Olyan beépített TÖMEGES szolgáltatón keresztül támogatja a tömeges műveleteket, amely lehetővé teszi, hogy egy fájlból származó adatok beolvassák és visszaadjanak a sorhalmazban.

## <a name="data-source"></a>Adatforrás

A OPENROWSET függvény a szinapszis SQL-ben beolvassa a fájl (ok) tartalmát egy adatforrásból. Az adatforrás egy Azure Storage-fiók, amely explicit módon hivatkozható a `OPENROWSET` függvényben, vagy dinamikusan következtetni lehet az olvasni kívánt fájlok URL-címére.
A `OPENROWSET` függvény opcionálisan tartalmazhat egy `DATA_SOURCE` paramétert a fájlokat tartalmazó adatforrás megadásához.
- `OPENROWSET` a nem használható a `DATA_SOURCE` fájlok tartalmának közvetlen olvasására a megadott URL-címről a következő `BULK` beállítással:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://<storage account>.dfs.core.windows.net/container/folder/*.parquet',
                    FORMAT = 'PARQUET') AS file
    ```

Ezzel a módszerrel gyorsan és egyszerűen elolvashatja a fájlok tartalmát előzetes konfigurálás nélkül. Ez a beállítás lehetővé teszi, hogy az alapszintű hitelesítési lehetőség használatával hozzáférjen a tárolóhoz (Azure ad áteresztő Azure AD-bejelentkezésekhez és SAS-jogkivonat SQL-bejelentkezésekhez). 

- `OPENROWSET` a `DATA_SOURCE` használatával a megadott Storage-fiókban található fájlok érhetők el:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    FORMAT = 'PARQUET') AS file
    ```


    Ezzel a beállítással konfigurálhatja a Storage-fiók helyét az adatforrásban, és megadhatja a tároló eléréséhez használandó hitelesítési módszert. 
    
    > [!IMPORTANT]
    > `OPENROWSET``DATA_SOURCE`a nem biztosít gyors és egyszerű módszert a tárolási fájlok eléréséhez, de korlátozott hitelesítési lehetőségeket kínál. Az Azure AD-rendszerbiztonsági tag például csak az [Azure ad-identitás](develop-storage-files-storage-access-control.md?tabs=user-identity) vagy nyilvánosan elérhető fájlok használatával férhet hozzá a fájlokhoz. Ha nagyobb teljesítményű hitelesítési beállításokra van szüksége, használja `DATA_SOURCE` a kapcsolót, és adja meg a tárhely eléréséhez használni kívánt hitelesítő adatokat.


## <a name="security"></a>Biztonság

Egy adatbázis-felhasználónak engedéllyel kell rendelkeznie `ADMINISTER BULK OPERATIONS` a `OPENROWSET` függvény használatához.

A tároló rendszergazdájának engedélyeznie kell a felhasználók számára, hogy érvényes SAS-jogkivonatot biztosítanak, vagy engedélyezni kell az Azure AD-rendszerbiztonsági tag számára a tárolási fájlok elérését. További információ a tárterület-hozzáférés-vezérlésről [ebben a cikkben](develop-storage-files-storage-access-control.md).

`OPENROWSET` a következő szabályok segítségével határozhatja meg, hogyan hitelesíthető a tárolóban:
- A `OPENROWSET` `DATA_SOURCE` hitelesítési mechanizmus nélkül a hívó típusától függ.
  - Bármely felhasználó használhatja `OPENROWSET` anélkül `DATA_SOURCE` , hogy nyilvánosan elérhető fájlokat kellene beolvasnia az Azure Storage-ban.
  - Az Azure AD-bejelentkezések saját [Azure ad-identitással](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) férhetnek hozzá a védett fájlokhoz, ha az Azure Storage lehetővé teszi, hogy az Azure ad-felhasználó hozzáférjen a mögöttes fájlokhoz (például ha a hívó rendelkezik `Storage Reader` engedéllyel az Azure Storage szolgáltatásban).
  - Az SQL-bejelentkezések `OPENROWSET` `DATA_SOURCE` a nyilvánosan elérhető fájlok, az SAS-token használatával védett fájlok, illetve a szinapszis munkaterület felügyelt identitása nélkül is használhatók. [Létre kell hoznia egy kiszolgáló-hatókörű hitelesítő adatot](develop-storage-files-storage-access-control.md#examples) a tárolási fájlok elérésének engedélyezéséhez. 
- A `OPENROWSET` és a `DATA_SOURCE` hitelesítési mechanizmus a hivatkozott adatforráshoz rendelt adatbázis-hatókörű hitelesítő adatokban van definiálva. Ez a beállítás lehetővé teszi a nyilvánosan elérhető tárolók elérését, vagy az SAS-token, a munkaterület felügyelt identitása vagy [a hívó Azure ad-identitása](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) (ha a hívó az Azure ad rendszerbiztonsági tag) használatával fér hozzá a tárolóhoz. Ha `DATA_SOURCE` olyan Azure Storage-ra hivatkozik, amely nem nyilvános, [adatbázis-hatókörű hitelesítő adatokat kell létrehoznia](develop-storage-files-storage-access-control.md#examples) , és hivatkozni kell rá a `DATA SOURCE` tároló-fájlok elérésének engedélyezéséhez.

A hívónak engedéllyel kell rendelkeznie `REFERENCES` a hitelesítő adatok tárolásához való használatához.

## <a name="syntax"></a>Szintaxis

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal' | 'json_path'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
[ , HEADER_ROW = { TRUE | FALSE } ]
[ , DATAFILETYPE = { 'char' | 'widechar' } ]
[ , CODEPAGE = { 'ACP' | 'OEM' | 'RAW' | 'code_page' } ]
```

## <a name="arguments"></a>Argumentumok

A lekérdezéshez célként megadott adatokat tartalmazó bemeneti fájlok esetében két lehetőség közül választhat. Az érvényes értékek a következők:

- "CSV" – minden olyan tagolt szövegfájlt tartalmaz, amely sorok/oszlopok elválasztókkal rendelkezik. Bármely karakter használható mező elválasztóként, például TSV: FIELDTERMINATOR = Tab.

- "PARQUEt" – bináris fájl parketta formátumban 

**"unstructured_data_path"**

Az adatelérési utat kiépítő unstructured_data_path abszolút vagy relatív elérési út lehet:
- A "://" formátumú abszolút elérési út \<prefix> \<storage_account_path> / \<storage_path> lehetővé teszi a felhasználók számára, hogy közvetlenül beolvassák a fájlokat.
- Relatív elérési út a (z) "<storage_path>" formátumban, amelyet a paraméterrel kell használni, `DATA_SOURCE` és a <storage_account_path> helyen definiált fájl mintáját ismerteti `EXTERNAL DATA SOURCE` . 

Az alábbiakban megtalálhatja a megfelelő <storage account path> értékeket, amelyek az adott külső adatforráshoz kapcsolódnak. 

| Külső adatforrás       | Előtag | Storage-fiók elérési útja                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | http [s]  | \<storage_account>. blob.core.windows.net/path/file   |
| Azure Blob Storage         | wasb [s]  | \<container>@\<storage_account>. blob.core.windows.net/path/file |
| Azure Data Lake Storage Gen1 | http [s]  | \<storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | http [s]  | \<storage_account>. dfs.core.windows.net/Path/file   |
| Azure Data Lake Storage Gen2 | abfs [s]  | [\<file_system>@\<account_name>. dfs.core.windows.net/path/file](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

Megadja a tárhelyen belüli útvonalat, amely az olvasni kívánt mappára vagy fájlra mutat. Ha az elérési út egy tárolóra vagy mappára mutat, a rendszer az adott tárolóból vagy mappából olvassa be az összes fájlt. Az almappákban található fájlok nem lesznek belefoglalva. 

A helyettesítő karakterek használatával több fájlt vagy mappát is megcélozhat. Több nem egymást követő helyettesítő karakter használata engedélyezett.
Az alábbi példa az összes olyan *CSV* -fájlt beolvassa, amely a */CSV/Population* kezdődő összes mappából származó *populációval* kezdődik:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Ha a unstructured_data_path mappaként adja meg, akkor a kiszolgáló nélküli SQL-készlet lekérdezése lekéri a fájlokat a mappából. 

A kiszolgáló nélküli SQL-készlet arra utasítja a mappákat, hogy az elérési út végén a/* érték megadásával például a következőt írja be: `https://sqlondemandstorage.blob.core.windows.net/csv/population/**`

> [!NOTE]
> A Hadoop és a bázistól eltérően a kiszolgáló nélküli SQL-készlet nem ad vissza almappákat, kivéve, ha az elérési út végén megadja a/* * értéket.

Ha az alábbi példában a unstructured_data_path = `https://mystorageaccount.dfs.core.windows.net/webdata/` , a kiszolgáló nélküli SQL-készlet lekérdezése mydata.txt sorokat ad vissza. Nem ad vissza mydata2.txt és mydata3.txt, mert egy almappában találhatók.

![Rekurzív adatértékek külső táblákhoz](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

A WITH záradék segítségével megadhatja a fájlokból beolvasni kívánt oszlopokat.

- CSV-adatfájlok esetén az összes oszlop olvasásához adja meg az oszlopnevek és az adattípusok nevét. Ha az oszlopok egy részhalmazát szeretné használni, a sorszámok használatával válassza ki az oszlopokat a származó adatfájlokból a sorszám alapján. Az oszlopokat a sorszám megjelölése fogja kötni. Ha a HEADER_ROW = TRUE érték van használatban, az oszlop kötése a sorszám helyett az oszlop neve alapján történik.
    > [!TIP]
    > Kihagyhatja a záradékot a CSV-fájlokhoz is. Az adattípusok automatikusan kikövetkeztetve lesznek a fájl tartalmából. A HEADER_ROW argumentum használatával megadhatja a fejlécsor létezését, amelyben az oszlop neve beolvasható a fejlécsorból. A részletekért lásd: [automatikus séma-felderítés](#automatic-schema-discovery).
    
- A parketta-adatfájlok esetében adja meg az oszlopok neveit, amelyek megfelelnek a kezdeményező adatfájlokban lévő oszlopnevek. Az oszlopok név szerint lesznek kötve, és megkülönböztetik a kis-és nagybetűket. Ha a WITH záradék ki van hagyva, a rendszer a Parquet-fájlokból származó összes oszlopot visszaadja.
    > [!IMPORTANT]
    > A parketta-fájlokban lévő oszlopnevek megkülönböztetik a kis-és nagybetűket. Ha a (z) nevű oszlopnevet a parketta-fájlban szereplő oszlopnév alapján adja meg, a rendszer NULL értékeket ad vissza ehhez az oszlophoz.


column_name = a kimeneti oszlop neve. Ha meg van adni, ez a név felülbírálja a forrásfájl és a JSON-elérési úton megadott oszlopnév nevét, ha van ilyen. Ha json_path nincs megadva, a rendszer automatikusan hozzáadja a "$ .column_name" értéket. A viselkedés json_path argumentumának bejelölése.

column_type = a kimeneti oszlop adattípusa. Az implicit adattípus-konverziót itt fogja megtenni.

column_ordinal = a forrásfájl (ok) oszlopának sorszáma. Ezt az argumentumot a rendszer figyelmen kívül hagyja a Parquet-fájlokban, mivel a kötés név alapján történik. A következő példa csak egy CSV-fájlból származó második oszlopot ad vissza:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

json_path = [JSON-elérésiút kifejezése](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) oszlopra vagy beágyazott tulajdonságra. Az alapértelmezett [elérésiút-mód](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true#PATHMODE) a LAX.

> [!NOTE]
> A szigorú módú lekérdezés sikertelen lesz, ha a megadott elérési út nem létezik. A LAX mód lekérdezése sikeres lesz, és a JSON Path kifejezés kiértékelése NULL értékre történik.

**\<bulk_options>**

FIELDTERMINATOR = ' field_terminator '

Meghatározza a használni kívánt lezáró mezőt. Az alapértelmezett lezáró mező egy vessző ("**,**").

ROWTERMINATOR = ' row_terminator ' '

Meghatározza a használandó sort. Ha a lezáró sor nincs megadva, a rendszer az alapértelmezett megszakítások egyikét fogja használni. PARSER_VERSION = "1,0" alapértelmezett lezárói a következők: \r\n, \n és \r. A PARSER_VERSION = "2,0" alapértelmezett lezárói a következők: \r\n és \n.

ESCAPE_CHAR = "char"

Meghatározza a fájlban található karaktert, amely a fájl összes elválasztó értékének kiszökésére szolgál. Ha az Escape-karaktert a saját maga vagy az elválasztó értékek egyike követi, az escape-karakter eldobása az érték beolvasása közben történik. 

A ESCAPE_CHAR paraméter attól függetlenül lesz alkalmazva, hogy a FIELDQUOTE vagy nincs-e engedélyezve. A rendszer nem használja fel az idézett karakter megmenekülésére. Az idézőjel karakternek egy másik idézőjel karakterrel kell megszöknie. Az idézőjel karakter csak akkor szerepelhet az oszlop értékén belül, ha az érték idézőjelekkel van ellátva.

FIRSTROW = ' first_row ' 

Meghatározza a betöltendő első sor számát. Az alapértelmezett érték 1, és jelzi a megadott adatfájl első sorát. A sorok száma a sorok lezáróinak számlálásával határozható meg. A FIRSTROW 1-alapú.

FIELDQUOTE = ' field_quote ' 

Megadja azt a karaktert, amely a CSV-fájlban idézőjelként lesz használva. Ha nincs megadva, a rendszer az idézőjel karaktert (") fogja használni. 

DATA_COMPRESSION = "data_compression_method"

Meghatározza a tömörítési módszert. Csak PARSER_VERSION = "1.0" esetén támogatott. A következő tömörítési módszer támogatott:

- GZIP

PARSER_VERSION = "parser_version"

A fájlok olvasásakor használandó elemző verzió megadása. Jelenleg támogatott CSV-elemző verziója 1,0 és 2,0:

- PARSER_VERSION = "1,0"
- PARSER_VERSION = "2,0"

A CSV-elemző 1,0-es verziója alapértelmezett, és gazdag funkció. Az 2,0-es verzió a teljesítményre van felépítve, és nem támogatja az összes beállítást és kódolást. 

CSV-elemző 1,0-es verziójának sajátosságai:

- A következő beállítások nem támogatottak: HEADER_ROW.

CSV-elemző 2,0-es verziójának sajátosságai:

- Az adattípusok nem támogatottak.
- A karakteres oszlop maximális hossza 8000.
- A sorok maximális méretének korlátja 8 MB.
- A következő beállítások nem támogatottak: DATA_COMPRESSION.
- Az idézőjelek közé tartozó üres karakterlánc ("") üres sztringként van értelmezve.
- A dátum adattípusának támogatott formátuma: éééé-hh-nn
- Az időadattípus támogatott formátuma: óó: PP: SS [. frakcionált másodperc]
- Támogatott formátum a DATETIME2 adattípushoz: éééé-hh-nn óó: PP: SS [. frakcionált másodperc]

HEADER_ROW = {TRUE | HAMIS

Meghatározza, hogy a CSV-fájl fejlécet tartalmaz-e. Az alapértelmezett érték a FALSE. PARSER_VERSION = "2.0" támogatja. Ha az értéke igaz, az oszlopok nevei az első sorból lesznek beolvasva a FIRSTROW argumentum alapján. Ha az igaz és a séma a WITH paranccsal van megadva, az oszlopnevek kötése oszlop neve alapján történik, nem pedig a sorszámok.

ADATFÁJLTÍPUS = {' char ' | "(widechar)"}

A kódolást adja meg: az UTF8-hoz használt char ((widechar)) a UTF16-fájlok esetében használatos.

KÓDLAP = {"ACP" | "OEM" | "NYERS" | "code_page"}

Megadja az adatfájlban szereplő adatkód oldalát. Az alapértelmezett érték 65001 (UTF-8 kódolás). Erről a lehetőségről [itt](/sql/t-sql/functions/openrowset-transact-sql?view=sql-server-ver15&preserve-view=true#codepage)talál további információt.

## <a name="fast-delimited-text-parsing"></a>Gyors tagolt szöveg elemzése

Két tagolt szöveges elemző verziója használható. A CSV-elemző 1,0-es verziója alapértelmezett, és a szolgáltatás gazdag, míg az elemző verziója a 2,0-es verzióra van építve. A 2,0-es elemző teljesítményének fejlesztése a fejlett elemzési technikáktól és a többszálas elemzéstől származik. A sebességbeli különbség nagyobb lesz, ahogy a fájlméret növekszik.

## <a name="automatic-schema-discovery"></a>Séma automatikus felderítése

Egyszerűen lekérdezheti a CSV-és a Parquet-fájlokat anélkül, hogy a záradékot kihagyva tudná vagy megadhatja a sémát. Az oszlopnevek és az adattípusok kikövetkeztetve lesznek a fájlokból.

A Parquet-fájlok olyan oszlop-metaadatokat tartalmaznak, amelyek beolvasva lesznek, a típusú leképezések a [parketta típus-hozzárendelésekben](#type-mapping-for-parquet)találhatók. Tekintse [meg a parketta-fájlok olvasását a minták sémájának megadása nélkül](#read-parquet-files-without-specifying-schema) .

A CSV-fájlok oszlopainak nevei a fejlécsorból is olvashatók. Megadhatja, hogy a fejlécsor létezik-e HEADER_ROW argumentum használatával. Ha HEADER_ROW = FALSE, az általános oszlopnevek lesznek használatban: C1, C2,... CN, ahol n a fájlban lévő oszlopok száma. Az adattípusok az első 100 adatsorokból lesznek kikövetkeztetve. Tekintse [meg a CSV-fájlok olvasását a minták sémájának megadása nélkül](#read-csv-files-without-specifying-schema) .

> [!IMPORTANT]
> Vannak olyan esetek, amikor a megfelelő adattípus nem következtethető ki, mert az adatok hiánya és a nagyobb adattípusok használata nem lehetséges. Ez a teljesítmény terhelését eredményezi, és különösen fontos a karakteres oszlopok esetében, amelyek varchar (8000) értékre lesznek utalva. Az optimális teljesítmény érdekében [tekintse meg a késleltetett adattípusokat](best-practices-sql-on-demand.md#check-inferred-data-types) , és [használja a megfelelő adattípusokat](best-practices-sql-on-demand.md#use-appropriate-data-types).

### <a name="type-mapping-for-parquet"></a>Típus leképezése a parketta számára

A Parquet-fájlok minden oszlop típusának leírását tartalmazzák. A következő táblázat leírja, hogyan vannak leképezve a parketta típusai az SQL natív típusaira.

| Parketta típusa | Parketta logikai típusa (jegyzet) | SQL-adattípus |
| --- | --- | --- |
| LOGIKAI | | bit |
| BINÁRIS/BYTE_ARRAY | | varbinary |
| DUPLÁN | | float |
| FLOAT | | valós szám |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINÁRIS |UTF8 |varchar \* (UTF8-rendezés) |
| BINÁRIS |KARAKTERLÁNC |varchar \* (UTF8-rendezés) |
| BINÁRIS |ENUM|varchar \* (UTF8-rendezés) |
| FIXED_LEN_BYTE_ARRAY |UUID |uniqueidentifier |
| BINÁRIS |DECIMÁLIS |tizedes tört |
| BINÁRIS |JSON |varchar (8000) \* (UTF8-rendezés) |
| BINÁRIS |BSON | Nem támogatott |
| FIXED_LEN_BYTE_ARRAY |DECIMÁLIS |tizedes tört |
| BYTE_ARRAY |IDŐKÖZ | Nem támogatott |
| INT32 |INT (8, igaz) |smallint |
| INT32 |INT (16, igaz) |smallint |
| INT32 |INT (32, true) |int |
| INT32 |INT (8, hamis) |tinyint |
| INT32 |INT (16, hamis) |int |
| INT32 |INT (32, hamis) |bigint |
| INT32 |DATE |dátum |
| INT32 |DECIMÁLIS |tizedes tört |
| INT32 |IDŐ (MILLIS)|time |
| INT64 |INT (64, true) |bigint |
| INT64 |INT (64, hamis) |decimális (20, 0) |
| INT64 |DECIMÁLIS |tizedes tört |
| INT64 |IDŐ (MICROS) |az időpontok (NANOs) nem támogatottak |
|INT64 |IDŐBÉLYEG (MALOM/MICROS) |datetime2 – az IDŐBÉLYEG (NANOs) nem támogatott |
|[Összetett típus](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |LISTÁJÁT |varchar (8000), szerializálva JSON-ban |
|[Összetett típus](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|Térkép|varchar (8000), szerializálva JSON-ban |

## <a name="examples"></a>Példák

### <a name="read-csv-files-without-specifying-schema"></a>CSV-fájlok olvasása séma meghatározása nélkül

Az alábbi példa olyan CSV-fájlt olvas be, amely az oszlopnevek és az adattípusok meghatározása nélkül tartalmazza a fejlécsort: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0',
    HEADER_ROW = TRUE) as [r]
```

Az alábbi példa olyan CSV-fájlt olvas be, amely nem tartalmaz fejlécet az oszlopnevek és az adattípusok meghatározása nélkül: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0') as [r]
```

### <a name="read-parquet-files-without-specifying-schema"></a>A Parquet-fájlok beolvasása a séma meghatározása nélkül

A következő példa visszaadja az első sor összes oszlopát a számbavételi adatkészletből, a parketta formátuma, valamint az oszlopnevek és az adattípusok megadása nélkül: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

### <a name="read-specific-columns-from-csv-file"></a>Adott oszlopok olvasása CSV-fájlból

A következő példa csak két olyan oszlopot ad vissza, amelyekben az 1. és 4. sorszám szerepel a Population*. csv fájlokban. Mivel a fájlokban nem szerepel fejlécsor, a rendszer az első sor olvasását indítja el:

```sql
SELECT 
    * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```

### <a name="read-specific-columns-from-parquet-file"></a>Adott oszlopok olvasása a Parquet-fájlból

A következő példa a Népszámlálás adatkészletének első sorából csak két oszlopot ad vissza, a parketta formátuma: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    [stateName] VARCHAR (50),
    [population] bigint
) AS [r]
```

### <a name="specify-columns-using-json-paths"></a>Oszlopok megadása JSON-elérési utak használatával

Az alábbi példa bemutatja, hogyan használhatja a [JSON elérésiút-kifejezéseket](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) a with záradékban, és különbséget mutat a szigorú és a LAX elérésiút-módok között: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    --lax path mode samples
    [stateName] VARCHAR (50), -- this one works as column name casing is valid - it targets the same column as the next one
    [stateName_explicit_path] VARCHAR (50) '$.stateName', -- this one works as column name casing is valid
    [COUNTYNAME] VARCHAR (50), -- STATEname column will contain NULLs only because of wrong casing - it targets the same column as the next one
    [countyName_explicit_path] VARCHAR (50) '$.COUNTYNAME', -- STATEname column will contain NULLS only because of wrong casing and default path mode being lax

    --strict path mode samples
    [population] bigint 'strict $.population' -- this one works as column name casing is valid
    --,[population2] bigint 'strict $.POPULATION' -- this one fails because of wrong casing and strict path mode
)
AS [r]
```

## <a name="next-steps"></a>Következő lépések

További példákat a [lekérdezési adattárolási](query-data-storage.md) útmutatóban talál, amelyből megtudhatja, hogyan használható a `OPENROWSET` [CSV](query-single-csv-file.md)-, a [parketta](query-parquet-files.md)-és a [JSON](query-json-files.md) -fájlformátumok olvasásához. [Ajánlott eljárások](best-practices-sql-on-demand.md) az optimális teljesítmény eléréséhez. Azt is megtudhatja, hogyan mentheti a lekérdezés eredményeit az Azure Storage-ba a [CETAS](develop-tables-cetas.md)használatával.