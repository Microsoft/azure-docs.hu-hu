---
title: Az OPENROWSET használata kiszolgáló nélküli SQL-készletben
description: Ez a cikk az OPENROWSET kiszolgáló nélküli SQL-készletben való szintaxisát és az argumentumok használatát ismerteti.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 28c54865ab9c2876d998896f5f536a11088962f8
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566426"
---
# <a name="how-to-use-openrowset-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Az OPENROWSET használata kiszolgáló nélküli SQL-készlet használatával a Azure Synapse Analytics

A `OPENROWSET(BULK...)` függvény lehetővé teszi az Azure Storage-ban lévő fájlokhoz való hozzáférést. `OPENROWSET` A függvény beolvassa egy távoli adatforrás (például fájl) tartalmát, és sorok halmazaként adja vissza a tartalmat. A kiszolgáló nélküli SQL-készlet erőforrásán belül az OPENROWSET tömeges sorhalmaz-szolgáltató az OPENROWSET függvény hívása és a BULK beállítás megadásával érhető el.  

A függvényre a lekérdezés záradékában lehet hivatkozni, mintha `OPENROWSET` `FROM` táblanév `OPENROWSET` lenne. Támogatja a tömeges műveleteket egy beépített BULK szolgáltatón keresztül, amely lehetővé teszi egy fájl adatainak sorhalmazként való olvasását és visszaadása.

## <a name="data-source"></a>Adatforrás

A fájl Synapse SQL OPENROWSET függvénye beolvassa a fájl(ak) tartalmát egy adatforrásból. Az adatforrás egy Azure-tárfiók, és explicit módon hivatkozhat rá a függvényben, vagy dinamikusan kikövetkeztetheti az olvasni kívánt fájlok `OPENROWSET` URL-címével.
A függvény tartalmazhat egy paramétert is a fájlokat tartalmazó `OPENROWSET` `DATA_SOURCE` adatforrás megadásához.
- `OPENROWSET` A without használatával közvetlenül beolvasható a fájlok tartalma a beállításként megadott `DATA_SOURCE` `BULK` URL-helyről:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://<storage account>.dfs.core.windows.net/container/folder/*.parquet',
                    FORMAT = 'PARQUET') AS file
    ```

Ez egy gyors és egyszerű módszer a fájlok tartalmának olvasására előzetes konfiguráció nélkül. Ez a beállítás lehetővé teszi, hogy az alapszintű hitelesítési lehetőséggel hozzáférjen a tárolóhoz (Azure AD-alapú bejelentkezések esetén Azure AD-áthaladás és SAS-jogkivonat sql-bejelentkezések esetén). 

- `OPENROWSET` A `DATA_SOURCE` a megadott tárfiókban lévő fájlok elérésére használható:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    FORMAT = 'PARQUET') AS file
    ```


    Ezzel a beállítással konfigurálhatja a tárfiók helyét az adatforrásban, és megadhatja a tároló eléréséhez használni kívánt hitelesítési módszert. 
    
    > [!IMPORTANT]
    > `OPENROWSET` A nélkül gyors és egyszerű módszert kínál a tárolófájlok `DATA_SOURCE` eléréséhez, de korlátozott hitelesítési lehetőségeket kínál. Az Azure AD-rendszerbiztonsági tag például csak az [Azure AD-identitásuk](develop-storage-files-storage-access-control.md?tabs=user-identity) vagy nyilvánosan elérhető fájljaik használatával férhet hozzá a fájlokhoz. Ha hatékonyabb hitelesítési lehetőségekre van szüksége, használja a lehetőséget, és adja meg a tároló eléréséhez használni kívánt `DATA_SOURCE` hitelesítő adatokat.


## <a name="security"></a>Biztonság

Az adatbázis-felhasználónak engedéllyel `ADMINISTER BULK OPERATIONS` kell rendelkeznie a függvény `OPENROWSET` használatára.

A tár-rendszergazdának azt is engedélyeznie kell, hogy a felhasználó érvényes SAS-jogkivonatot biztosítson a fájlokhoz, vagy engedélyezze az Azure AD-rendszerbiztonsági tag számára a tárfájlokhoz való hozzáférést. A tárterület-hozzáférés-vezérlésről ebben [a cikkben talál további információt.](develop-storage-files-storage-access-control.md)

`OPENROWSET` a következő szabályok alapján határozza meg a tárolóban való hitelesítést:
- A `OPENROWSET` hitelesítési mechanizmus nélkül a hívó `DATA_SOURCE` típusától függ.
  - Bármely felhasználó használhatja `OPENROWSET` anélkül, hogy beolvasná a nyilvánosan elérhető `DATA_SOURCE` fájlokat az Azure Storage-ban.
  - Az Azure AD-bejelentkezések a saját [Azure AD-identitásuk](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) használatával férhetnek hozzá a védett fájlokhoz, ha az Azure Storage lehetővé teszi az Azure AD-felhasználó számára a mögöttes fájlokhoz való hozzáférést (például ha a hívó rendelkezik engedéllyel az `Storage Reader` Azure Storage-hoz).
  - Az SQL-bejelentkezések a nyilvánosan elérhető fájlokhoz, SAS-jogkivonattal védett fájlokhoz vagy a Synapse-munkaterület felügyelt identitásához való hozzáférés nélkül is `OPENROWSET` `DATA_SOURCE` használhatók. A tárolófájlokhoz való [hozzáféréshez kiszolgálói](develop-storage-files-storage-access-control.md#examples) hatókörű hitelesítő adatokat kell létrehoznia. 
- A hitelesítési mechanizmussal a hivatkozott adatforráshoz rendelt, adatbázisra vonatkozó hitelesítő adatok `OPENROWSET` `DATA_SOURCE` határozzák meg. Ez a beállítás lehetővé teszi a nyilvánosan elérhető tárolók vagy a tárolók elérését SAS-jogkivonat, a munkaterület felügyelt identitása vagy a hívó [Azure AD-identitása](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) használatával (ha a hívó Azure AD-rendszerbiztonsági tag). Ha nem nyilvános Azure Storage-tárolóra hivatkozik, létre kell hoznia az adatbázisra vonatkozó hitelesítő adatokat, és hivatkozni kell rá a tárolófájlokhoz `DATA_SOURCE` való [](develop-storage-files-storage-access-control.md#examples) `DATA SOURCE` hozzáféréshez.

A hívónak engedéllyel kell `REFERENCES` rendelkeznie a hitelesítő adatokhoz a tárolóban való hitelesítéshez.

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

A lekérdezés céladatát tartalmazó bemeneti fájlokhoz két lehetőség közül választhat. Az érvényes értékek a következők:

- CSV – Sor-/oszlopelválasztókat tartalmazó tagolt szövegfájlokat tartalmaz. Bármilyen karakter használható mezőelválasztóként, például TSV: FIELDTERMINATOR = tabulátor.

- PARQUET – Parquet formátumú bináris fájl 

**"unstructured_data_path"**

Az unstructured_data_path az adatok elérési útját lekért útvonal abszolút vagy relatív elérési út lehet:
- A ' :// ' formátumú abszolút elérési út \<prefix> \<storage_account_path> / \<storage_path> lehetővé teszi a felhasználó számára a fájlok közvetlen olvasását.
- Relatív elérési út "<storage_path>" formátumban, amely a paraméterrel együtt használható, és leírja a fájlmintát a `DATA_SOURCE` fájlban <storage_account_path> helyen `EXTERNAL DATA SOURCE` belül. 

Az alábbiakban találja a megfelelő értékeket, amelyek az adott külső <storage account path> adatforrásra hivatkoznak. 

| Külső adatforrás       | Előtag | Tárfiók elérési útja                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | http[s]  | \<storage_account>.blob.core.windows.net/path/file   |
| Azure Blob Storage         | wasb[s]  | \<container>@\<storage_account>.blob.core.windows.net/path/file |
| Azure Data Lake Storage Gen1 | http[s]  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | http[s]  | \<storage_account>.dfs.core.windows.net /path/file   |
| Azure Data Lake Storage Gen2 | abfs[s]  | [\<file_system>@\<account_name>.dfs.core.windows.net/path/file](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

Megadja a tárterületen belüli elérési utat, amely az olvasni kívánt mappára vagy fájlra mutat. Ha az elérési út egy tárolóra vagy mappára mutat, a rendszer az összes fájlt beolvassa az adott tárolóból vagy mappából. Az almappákban lévő fájlok nem lesznek benne. 

Helyettesítő karakterek használatával több fájlt vagy mappát célozhat meg. Több nem megfelelő helyettesítő karakter használata engedélyezett.
Az alábbi példa beolvassa az  összes csv-fájlt a */csv/population* fájltól kezdődően az összes mappából:   
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Ha megadja, hogy unstructured_data_path mappa legyen, a kiszolgáló nélküli SQL-készlet lekérdezése ebből a mappából fogja lekérni a fájlokat. 

Utasíthatja a kiszolgáló nélküli SQL-készletet, hogy az elérési út végén a /* megadásával járja be a mappákat, ahogy az az alábbi példában is használhatja: `https://sqlondemandstorage.blob.core.windows.net/csv/population/**`

> [!NOTE]
> A Hadooptól és a PolyBase-től eltérően a kiszolgáló nélküli SQL-készlet csak akkor ad vissza almappákat, ha az elérési út végén a /** értéket adja meg. A Hadoophoz és a PolyBase-hez hasonlóan nem ad vissza olyan fájlokat, amelyek fájlneve aláhúzással (_) vagy pontokkal (.) kezdődik.

Az alábbi példában, ha a unstructured_data_path= , egy kiszolgáló nélküli SQL-készlet lekérdezése fog sorokat visszaadni a `https://mystorageaccount.dfs.core.windows.net/webdata/` mydata.txt. Nem ad vissza mydata2.txt és mydata3.txt, mert egy almappában találhatók.

![Rekurzív adatok külső táblákhoz](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

A WITH záradék lehetővé teszi a fájlokból beolvasni kívánt oszlopok megadását.

- CSV-adatfájlok esetén az összes oszlop beolvassa az oszlopneveket és azok adattípusait. Ha oszlopok egy részkészletét szeretné használni, sorszámszámok használatával válassza ki az oszlopokat az eredeti adatfájlokból sorszám szerint. Az oszlopokat a sorszám jelölése köti. Ha HEADER_ROW = TRUE érték van használva, akkor az oszlopkötés az oszlop neve alapján történik a sorszám pozíciója helyett.
    > [!TIP]
    > A CSV-fájlokra vonatkozó WITH záradékot is kihagyhatja. Az adattípusok automatikusan kikövetkeztetve lesznek a fájl tartalmából. A argumentum HEADER_ROW megadhatja a fejlécsor meglétét, amely esetben az oszlopnevek a fejlécsorból lesznek beolvasva. Részletekért tekintse meg az [automatikus sémafelderítést.](#automatic-schema-discovery)
    
- Parquet-adatfájlok esetén olyan oszlopneveket adjon meg, amelyek megegyeznek az eredeti adatfájlokban szereplő oszlopnevekkel. Az oszlopok név alapján lesznek kötve, és megkülönböztetik a kis- és nagybetűket. Ha a WITH záradék nincs megadva, a Rendszer a Parquet-fájlok összes oszlopát visszaadja.
    > [!IMPORTANT]
    > A Parquet-fájlokban az oszlopnevek megkülönböztetik a kis- és nagybetűket. Ha a Parquet-fájlban a kis- és a nagy cas-értékeket adja meg, a rendszer NULL értékeket ad vissza az oszlophoz.


column_name = A kimeneti oszlop neve. Ha meg van téve, ez a név felülírja a forrásfájlban lévő oszlopnevet, és ha van ilyen, akkor a JSON-útvonalban megadott oszlopnevet. Ha json_path nincs megadva, a rendszer automatikusan hozzáadja "$.column_name" értékként. Ellenőrizze, json_path-e a viselkedést az argumentumban.

column_type = A kimeneti oszlop adattípusa. Az implicit adattípus-konverzióra itt kerül sor.

column_ordinal = a forrásfájl(k) oszlopának sorszáma. A Parquet-fájlok ezt az argumentumot figyelmen kívül hagyják, mivel a kötés név alapján történik. Az alábbi példa egy második oszlopot ad vissza csak egy CSV-fájlból:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

json_path = [JSON elérési út kifejezése](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) oszlopra vagy beágyazott tulajdonságra. Az [alapértelmezett elérési út](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true#PATHMODE) módja a lax.

> [!NOTE]
> Szigorú módban a lekérdezés hibát jelez, ha a megadott elérési út nem létezik. Lax módban a lekérdezés sikeres lesz, és a JSON-útvonal kifejezés kiértékelése NULL lesz.

**\<bulk_options>**

FIELDTERMINATOR ='field_terminator'

A használni használt mező-lezáró megadása. A mező alapértelmezett lezáró a vessző ("**,**").

ROWTERMINATOR ='row_terminator''

A használni használt sorterminátort adja meg. Ha nincs megadva sorterminátor, a rendszer az egyik alapértelmezett lezárót használja. Az alapértelmezett lezárók PARSER_VERSION = '1.0' : \r\n, \n és \r. Az alapértelmezett lezárók a PARSER_VERSION = '2.0' = \r\n és \n.

ESCAPE_CHAR = 'char'

Megadja a fájl azon karakterét, amely a saját maga és a fájl összes elválasztóértékének a escape-karaktereként használható. Ha a escape-karaktert nem magát, vagy az elválasztó értékek bármelyikét követi, a rendszer eldobja a escape-karaktert az érték beolvasásakor. 

A ESCAPE_CHAR paraméter attól függetlenül lesz alkalmazva, hogy a FIELDQUOTE vagy nincs engedélyezve. A hányados karakter kivédésében nem lesz használva. Az hányados karaktert egy másik hányados karakterrel kell escape-karakterrel megszűkülni. Az idéző karakter csak akkor jelenik meg az oszlopértékben, ha az érték idéző karaktereket tartalmaz.

FIRSTROW = 'first_row' 

Az első betöltés sorának számát adja meg. Az alapértelmezett érték 1, és a megadott adatfájl első sorát jelzi. A sorszámokat a sorterminátorok megszámlálása határozza meg. A FIRSTROW 1-alapú.

FIELDQUOTE = 'field_quote' 

Egy olyan karaktert ad meg, amely idézőjelként lesz használva a CSV-fájlban. Ha nincs megadva, a rendszer az idézőjel karaktert (") használja. 

DATA_COMPRESSION = 'data_compression_method'

A tömörítési metódust határozza meg. Csak a PARSER_VERSION='1.0' támogatja. A következő tömörítési módszer támogatott:

- Gzip

PARSER_VERSION = 'parser_version'

Megadja a fájlok olvas olvasása során használt elemzőverziót. A CSV-elemző jelenleg az 1.0-s és 2.0-s verziókat támogatja:

- PARSER_VERSION = '1.0'
- PARSER_VERSION = '2.0'

A CSV-elemző 1.0-s verziója alapértelmezett, és funkciókban gazdag. A 2.0-s verzió teljesítményre készült, és nem támogat minden lehetőséget és kódolást. 

A CSV-elemző 1.0-s verziójának sajátosságai:

- A következő beállítások nem támogatottak: HEADER_ROW.

A CSV-elemző 2.0-s verziójának sajátosságai:

- Nem minden adattípus támogatott.
- A karakteroszlopok maximális hossza 8000.
- A sorok maximális méretkorlátja 8 MB.
- A következő beállítások nem támogatottak: DATA_COMPRESSION.
- Az idézett üres sztring ("") üres sztringként van értelmezve.
- A DATE adattípus támogatott formátuma: YYYY-MM-DD
- A TIME adattípus támogatott formátuma: HH:MM:SS[.fractional seconds]
- A DATETIME2 adattípus támogatott formátuma: YYYY-MM-DD HH:MM:SS[.fractional seconds]

HEADER_ROW = { TRUE | FALSE }

Meghatározza, hogy a CSV-fájl tartalmaz-e fejlécsort. Az alapértelmezett érték FALSE (HAMIS). A következő PARSER_VERSION='2.0'. HA IGAZ, az oszlopnevek az első sorból lesznek beolvasva a FIRSTROW argumentumnak megfelelően. Ha a TRUE (IGAZ) és a schema (séma) a WITH használatával van megadva, az oszlopnevek kötése oszlopnév alapján történik, nem pedig sorszámok alapján.

DATAFILETYPE = { 'char' | 'widechar' }

Kódolást ad meg: a char utF8, widechar az UTF16-fájlokhoz használatos.

CODEPAGE = { 'ACP' | OEM-| "RAW" | "code_page" }

Megadja az adatfájlban található adatok kódlapját. Az alapértelmezett érték 65001 (UTF-8 kódolás). Erről a lehetőségről itt talál [további információt.](/sql/t-sql/functions/openrowset-transact-sql?view=sql-server-ver15&preserve-view=true#codepage)

## <a name="fast-delimited-text-parsing"></a>Gyors elválasztójeles szöveg-elemzési

Két tagolt szöveg elemzőverziót használhat. A CSV-elemző 1.0-s verziója alapértelmezett, és gazdag funkciókat biztosít, míg az elemző 2.0-s verziója teljesítményre lett felépítve. Az elemző 2.0 teljesítménybeli javulását a fejlett elemzési technikák és a többszálas módszer is lehetővé tért. A fájlméret növekedésével a sebességkülönbség nagyobb lesz.

## <a name="automatic-schema-discovery"></a>Automatikus sémafelderítés

A WITH záradék kihagyása nélkül is egyszerűen lekérdezhet CSV- és Parquet-fájlokat a séma ismerete vagy megadása nélkül. Az oszlopnevek és adattípusok a fájlokból lesznek kikövetkeztetve.

A Parquet-fájlok oszlopmetaadatokat tartalmaznak, amelyek be lesznek olvasva, a típusleképezések pedig a [Parquet](#type-mapping-for-parquet)típusleképezésében találhatók. Ellenőrizze [a Parquet-fájlok olvasását séma megadása nélkül](#read-parquet-files-without-specifying-schema) a mintákhoz.

CSV-fájloknál az oszlopnevek a fejlécsorból olvashatók. Az argumentum használatával megadhatja, hogy a fejlécsor létezik HEADER_ROW létezik. Ha HEADER_ROW = FALSE, általános oszlopneveket fog használni: C1, C2, ... Cn, ahol n a fájlban lévő oszlopok száma. Az adattípusok az első 100 adatsorból lesznek kikövetkeztetve. Ellenőrizze [a CSV-fájlok olvasását séma megadása nélkül](#read-csv-files-without-specifying-schema) a mintákhoz.

> [!IMPORTANT]
> Vannak olyan esetek, amikor az információ hiánya miatt nem lehet kikövetkeztetni a megfelelő adattípust, és inkább nagyobb adattípust fog használni. Ez teljesítménybeli többletterhelést eredményez, és különösen fontos a karakteroszlopok esetén, amelyek varchar(8000) ként lesznek kikövetkeztetve. Az optimális teljesítmény érdekében ellenőrizze a [kikövetkeztetett adattípusokat,](best-practices-sql-on-demand.md#check-inferred-data-types) [és használja a megfelelő adattípusokat.](best-practices-sql-on-demand.md#use-appropriate-data-types)

### <a name="type-mapping-for-parquet"></a>Típusleképezés a Parquethez

A Parquet-fájlok minden oszlop típusleírását tartalmazzák. Az alábbi táblázat bemutatja, hogyan vannak leképezve a Parquet-típusok natív SQL-típusokra.

| Parquet-típus | Parquet logikai típus (jegyzet) | SQL-adattípus |
| --- | --- | --- |
| Logikai | | bit |
| BINARY /BYTE_ARRAY | | varbinary (varbinary) |
| Dupla | | float |
| Úszó | | valós szám |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| Bináris |UTF8 |varchar \* (UTF8 rendezés) |
| Bináris |Karakterlánc |varchar \* (UTF8 rendezés) |
| Bináris |Enum|varchar \* (UTF8 rendezés) |
| FIXED_LEN_BYTE_ARRAY |Uuid |uniqueidentifier |
| Bináris |Decimális |tizedes tört |
| Bináris |JSON |varchar(8000) \* (UTF8 rendezés) |
| Bináris |BSON (BSON) | Nem támogatott |
| FIXED_LEN_BYTE_ARRAY |Decimális |tizedes tört |
| BYTE_ARRAY |Intervallum | Nem támogatott |
| INT32 |INT(8, true) |smallint |
| INT32 |INT(16, true) |smallint |
| INT32 |INT(32, true) |int |
| INT32 |INT(8, false) |tinyint |
| INT32 |INT(16, false) |int |
| INT32 |INT(32, false) |bigint |
| INT32 |DATE |dátum |
| INT32 |Decimális |tizedes tört |
| INT32 |TIME (MILLIS)|time |
| INT64 |INT(64, true) |bigint |
| INT64 |INT(64, false) |decimal(20,0) |
| INT64 |Decimális |tizedes tört |
| INT64 |TIME (MICROS) |time – A TIME(NANOS) nem támogatott |
|INT64 |TIMESTAMP (MILLIS /MICROS) |datetime2 – A TIMESTAMP(NANOS) nem támogatott |
|[Összetett típus](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |Lista |varchar(8000), JSON-ba szerializálva |
|[Összetett típus](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|Megjelenítése|varchar(8000), JSON-ba szerializálva |

## <a name="examples"></a>Példák

### <a name="read-csv-files-without-specifying-schema"></a>CSV-fájlok olvasása séma megadása nélkül

Az alábbi példa beolvassa a fejlécsort tartalmazó CSV-fájlt oszlopnevek és adattípusok megadása nélkül: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0',
    HEADER_ROW = TRUE) as [r]
```

Az alábbi példa beolvassa a fejlécsort nem tartalmazó CSV-fájlt oszlopnevek és adattípusok megadása nélkül: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0') as [r]
```

### <a name="read-parquet-files-without-specifying-schema"></a>Parquet-fájlok olvasása séma megadása nélkül

Az alábbi példa Parquet formátumban, az oszlopnevek és adattípusok megadása nélkül adja vissza az első sor összes oszlopát a nép néphalmazból: 

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

Az alábbi példa csak két oszlopot ad vissza 1 és 4 sorszámmal a population*.csv fájlokból. Mivel a fájlokban nincs fejlécsor, az első sorból kezd olvasni:

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

Az alábbi példa csak két oszlopot ad vissza a néphalmaz első sorában Parquet formátumban: 

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

### <a name="specify-columns-using-json-paths"></a>Oszlopok megadása JSON-útvonalak használatával

Az alábbi példa bemutatja, hogyan használhatók a [JSON-útvonalkifejezések](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) a WITH záradékban, és bemutatja a szigorú és a laxás elérésiút-módok közötti különbségeket: 

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

További példákért tekintse [](query-data-storage.md) meg a lekérdezési adatok tárolásának rövid útmutatóját, amelyből megtudhatja, hogyan olvashatja be a `OPENROWSET` [CSV-](query-single-csv-file.md), [PARQUET-](query-parquet-files.md)és [JSON-fájlformátumokat.](query-json-files.md) Tekintse meg [az optimális](best-practices-sql-on-demand.md) teljesítmény eléréséhez ajánlott eljárásokat. Azt is megtudhatja, hogyan mentheti a lekérdezés eredményeit az Azure Storage-ba a [CETAS használatával.](develop-tables-cetas.md)