---
title: Külső táblák használata Synapse SQL
description: Adatfájlok olvasása vagy írása Synapse SQL
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 0986a1d6a75f0d464eb405841af821c606c68200
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565321"
---
# <a name="use-external-tables-with-synapse-sql"></a>Külső táblák használata Synapse SQL

A külső tábla a Hadoopban, Azure Storage-blobban vagy -tárolóban található adatokra Azure Data Lake Storage. A külső táblákkal fájlokból olvashat be adatokat, vagy írhat adatokat az Azure Storage-ban lévő fájlokba. A Synapse SQL külső táblák használatával adatokat olvashat be és írhat dedikált SQL-készletbe vagy kiszolgáló nélküli SQL-készletbe.

## <a name="external-tables-in-dedicated-sql-pool-and-serverless-sql-pool"></a>Külső táblák a dedikált SQL-készletben és a kiszolgáló nélküli SQL-készletben

### <a name="dedicated-sql-pool"></a>[Dedikált SQL-készlet](#tab/sql-pool) 

Dedikált SQL-készletben a következőre használhat külső táblát:

- Lekérdezési Azure Blob Storage Azure Data Lake Gen2 és Transact-SQL-utasításokkal.
- Importálhatja és tárolhatja a Azure Blob Storage és Azure Data Lake Storage SQL-készletbe.

A CREATE TABLE AS [SELECT](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) utasítással együtt használva a külső táblából való kiválasztással adatokat importál az SQL-készlet egy táblába. A COPY utasítás [mellett a](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)külső táblák is hasznosak az adatok betöltéséhez. 

Betöltési oktatóanyagért lásd: Adatok betöltése a PolyBase használatával [a Azure Blob Storage.](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)

### <a name="serverless-sql-pool"></a>[Kiszolgáló nélküli SQL-készlet](#tab/sql-on-demand)

Kiszolgáló nélküli SQL-készlet esetén külső táblát fog használni a következőre:

- Adatok lekérdezése Azure Blob Storage vagy Azure Data Lake Storage Transact-SQL-utasításokkal
- Kiszolgáló nélküli SQL-készlet lekérdezési eredményeinek tárolása a CETAS Azure Blob Storage vagy Azure Data Lake Storage [fájlokban](develop-tables-cetas.md)

A külső táblák kiszolgáló nélküli SQL-készlet használatával a következő lépésekkel hozhatók létre:

1. KÜLSŐ ADATFORRÁS LÉTREHOZÁSA
2. CREATE EXTERNAL FILE FORMAT
3. CREATE EXTERNAL TABLE

---

### <a name="security"></a>Biztonság

A felhasználónak engedéllyel `SELECT` kell rendelkeznie a külső táblán az adatok beolvassához.
A külső tábla az adatforrásban meghatározott, adatbázisra vonatkozó hitelesítő adatokkal fér hozzá a mögöttes Azure Storage-hoz a következő szabályokkal:
- A hitelesítő adatok nélküli adatforrás lehetővé teszi, hogy a külső táblák hozzáférjenek az Azure Storage-ban nyilvánosan elérhető fájlokhoz.
- Az adatforrás olyan hitelesítő adatokkal is rendelkezik, amelyek lehetővé teszik, hogy a külső táblák csak az Azure Storage-ban lévő fájlokhoz férnek hozzá SAS-jogkivonat vagy munkaterületi felügyelt identitás használatával – Példákat a [Tárfájlok](develop-storage-files-storage-access-control.md#examples) tárelérés-vezérlésének fejlesztése cikkben talál.

> [!IMPORTANT]
> A dedikált SQL-készletben hitelesítő adatok nélkül létrehozott adatforrás lehetővé teszi az Azure AD-felhasználók számára, hogy az Azure AD-identitásuk használatával hozzáférjenek a tárolófájlokhoz. A kiszolgáló nélküli SQL-készletben létre kell hoznia egy adatforrást egy olyan adatbázisra vonatkozó hitelesítő adatokkal, amely rendelkezik tulajdonsággal – lásd `IDENTITY='User Identity'` [példákat itt.](develop-storage-files-storage-access-control.md#examples)

## <a name="create-external-data-source"></a>KÜLSŐ ADATFORRÁS LÉTREHOZÁSA

A külső adatforrások a tárfiókok csatlakoztatására használhatók. A teljes dokumentációt itt [ismertetjuk.](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="syntax-for-create-external-data-source"></a>A CREATE EXTERNAL DATA SOURCE szintaxisa

#### <a name="dedicated-sql-pool"></a>[Dedikált SQL-készlet](#tab/sql-pool)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
     , TYPE = HADOOP
)
[;]
```

#### <a name="serverless-sql-pool"></a>[Kiszolgáló nélküli SQL-készlet](#tab/sql-on-demand)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
)
[;]
```

---

### <a name="arguments-for-create-external-data-source"></a>A CREATE EXTERNAL DATA SOURCE argumentumai

data_source_name

Megadja az adatforrás felhasználó által megadott nevét. A névnek egyedinek kell lennie az adatbázisban.

#### <a name="location"></a>Hely
LOCATION = – A külső adatforrás kapcsolati protokollját és `'<prefix>://<path>'`   elérési útját biztosítja. A következő minták használhatók a helyszínen:

| Külső adatforrás        | Helyelőtag | Hely elérési útja                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Blob Storage          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Azure Blob Storage          | `http[s]`       | `<storage_account>.blob.core.windows.net/<container>/subfolders` |
| Azure Data Lake Store Gen 1 | `http[s]`       | `<storage_account>.azuredatalakestore.net/webhdfs/v1` |
| Azure Data Lake Store Gen2 | `http[s]`       | `<storage_account>.dfs.core.windows.net/<container>/subfolders`  |

`https:` A előtaggal almappát használhat az elérési úton.

#### <a name="credential"></a>Hitelesítő adat
CREDENTIAL = nem kötelező hitelesítő adat, amely `<database scoped credential>` az Azure Storage-ban való hitelesítéshez lesz használva. A külső adatforrások hitelesítő adatok nélkül férhetnek hozzá a nyilvános tárfiókhoz. 

A dedikált SQL-készletben hitelesítő adatok nélküli külső adatforrások a hívó Azure AD-identitásával férnek hozzá a tárolóban lévő fájlokhoz. A kiszolgáló nélküli SQL-készlet hitelesítő adatokkal való külső adatforrása a hívó Azure AD-identitásával fér hozzá  `IDENTITY='User Identity'` a fájlokhoz.
- A dedikált SQL-készletben az adatbázisra vonatkozó hitelesítő adatok megadhatnak egyéni alkalmazásidentitást, munkaterület által felügyelt identitást vagy SAK-kulcsot. 
- A kiszolgáló nélküli SQL-készletben az adatbázisra vonatkozó hitelesítő adatok megadhatja a hívó Azure AD-identitását, a munkaterület felügyelt identitását vagy SAS-kulcsát. 

#### <a name="type"></a>TÍPUS
A TYPE = kötelező beállítás a dedikált SQL-készletben, és megadja, hogy a rendszer Polybase-technológiát használ a `HADOOP` mögöttes fájlok eléréséhez. Ez a paraméter nem használható beépített natív olvasót használó kiszolgáló nélküli SQL-készletben.

### <a name="example-for-create-external-data-source"></a>Példa KÜLSŐ ADATFORRÁS LÉTREHOZÁSÁRA

#### <a name="dedicated-sql-pool"></a>[Dedikált SQL-készlet](#tab/sql-pool)

Az alábbi példa egy külső adatforrást hoz létre az Azure Data Lake Gen2-hez, amely a New York-adatkészletre mutat:

```sql
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://data@newyorktaxidataset.dfs.core.windows.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

#### <a name="serverless-sql-pool"></a>[Kiszolgáló nélküli SQL-készlet](#tab/sql-on-demand)

Az alábbi példa egy külső adatforrást hoz létre az Azure Data Lake Gen2-hez, amely SAS-hitelesítő adatokkal érhető el:

```sql
CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO

CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

Az alábbi példa egy külső adatforrást hoz létre az Azure Data Lake Gen2-hez, amely a nyilvánosan elérhető New York-adatkészletre mutat:

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
```
---

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Létrehoz egy külső fájlformátum-objektumot, amely meghatározza a Azure Blob Storage vagy Azure Data Lake Storage. Külső fájlformátum létrehozása a külső tábla létrehozásának előfeltétele. A teljes dokumentáció itt [található.](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)

Külső fájlformátum létrehozásával megadhatja a külső tábla által hivatkozott adatok tényleges elrendezését.

### <a name="syntax-for-create-external-file-format"></a>A CREATE EXTERNAL FILE FORMAT szintaxisa

#### <a name="sql-pool"></a>[SQL-készlet](#tab/sql-pool)

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' ]
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
}
```

#### <a name="serverless-sql-pool"></a>[Kiszolgáló nélküli SQL-készlet](#tab/sql-on-demand)

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' ]
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
    | PARSER_VERSION = {'parser_version'}
}
```

---


### <a name="arguments-for-create-external-file-format"></a>A CREATE EXTERNAL FILE FORMAT argumentumai

file_format_name – Megadja a külső fájlformátum nevét.

FORMAT_TYPE = [ PARQUET | DELIMITEDTEXT]– A külső adatok formátumát adja meg.

- PARQUET – Parquet formátumot ad meg.
- TAGOLT SZÖVEG – Egy oszlopelválasztókat tartalmazó szöveges formátumot, más néven mezőhatárolókat ad meg.

FIELD_TERMINATOR = *field_terminator* – Csak tagolt szövegfájlokra vonatkozik. A mező-lezáró egy vagy több olyan karaktert ad meg, amely a szöveggel tagolt fájl minden mezőjének (oszlopának) végét jelöli. Az alapértelmezett érték a pipe karakter (ꞌ|ꞌ).

Angol nyelvű Példák:

- FIELD_TERMINATOR = '|'
- FIELD_TERMINATOR = ' '
- FIELD_TERMINATOR = ꞌ\tꞌ

STRING_DELIMITER = *string_delimiter* – Megadja a szöveggel tagolt fájl sztring típusú adatainak mezőbeválasztóját. A sztringhatároló egy vagy több karakter hosszúságú, és egyszeres idézőjelek között van. Az alapértelmezett érték az üres sztring ("").

Angol nyelvű Példák:

- STRING_DELIMITER = """
- STRING_DELIMITER = '*'
- STRING_DELIMITER = ꞌ,ꞌ

FIRST_ROW = *First_row_int* – Meghatározza az elsőként beolvasott és az összes fájlra vonatkozó sorszámot. Ha az értéket két értékre áll be, a rendszer minden fájl (fejlécsor) első sorát kihagyja az adatok betöltésekor. A sorok kihagyása a sorterminátorok megléte (/r/n, /r, /n) alapján lesz kihagyva.

USE_TYPE_DEFAULT = { TRUE | **FALSE** } – Meghatározza, hogyan kezelje a tagolt szövegfájlok hiányzó értékeit, amikor adatokat ad vissza a szövegfájlból.

TRUE (IGAZ) – Ha a szövegfájlból ad le adatokat, tárolja az egyes hiányzó értékeket az alapértelmezett érték adattípusának használatával a külső tábla definíciójának megfelelő oszlopában. Például cserélje le a hiányzó értéket a következőre:

- 0, ha az oszlop numerikus oszlopként van definiálva. A decimális oszlopok nem támogatottak, és hibát okoznak.
- Üres sztring ("") ha az oszlop sztringoszlop.
- 1900-01-01, ha az oszlop dátumoszlop.

FALSE (HAMIS) – Az összes hiányzó értéket NULL értékként tárolja. A tagolt szövegfájlban a NULL szó használatával tárolt NULL értékeket a rendszer NULL sztringként importálja.

Encoding = {'UTF8' | UTF16} – A kiszolgáló nélküli SQL-készlet UTF8- és UTF16-kódolású, tagolt szövegfájlokat tud olvasni.

DATA_COMPRESSION = *data_compression_method* – Ez az argumentum határozza meg a külső adatok adattömörítési metódusát. 

A PARQUET fájlformátum a következő tömörítési módszereket támogatja:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

A PARQUET külső tábláiból történő beolvasáskor a rendszer figyelmen kívül hagyja ezt az argumentumot, de külső táblákba ír a [CETAS használatával.](develop-tables-cetas.md)

A DELIMITEDTEXT fájlformátum a következő tömörítési módszert támogatja:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'

PARSER_VERSION = "parser_version" A fájlok olvasása során használt elemzőverziót határozza meg. A PARSER_VERSION [az OPENROWSET argumentumok](develop-openrowset.md#arguments) között.

### <a name="example-for-create-external-file-format"></a>Példa a CREATE EXTERNAL FILE FORMAT (KÜLSŐ FÁJLFORMÁTUM LÉTREHOZÁSA) parancsra

Az alábbi példa egy külső fájlformátumot hoz létre a nép meg nem felelő fájlokhoz:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>CREATE EXTERNAL TABLE

A CREATE EXTERNAL TABLE parancs létrehoz egy külső táblát a Synapse SQL számára a Azure Blob Storage vagy Azure Data Lake Storage. 

### <a name="syntax-for-create-external-table"></a>Szintaxis a CREATE EXTERNAL TABLE-hez

```sql
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH (
        LOCATION = 'folder_or_filepath',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name
    )  
[;]  

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]
```

### <a name="arguments-create-external-table"></a>ARGUMENTUMOK CREATE EXTERNAL TABLE

*{ database_name.schema_name.table_name | schema_name.table_name | table_name }*

A létrehozni szükséges tábla egy-háromrészes neve. Külső tábla esetén a kiszolgáló nélküli SQL-készlet csak a tábla metaadatait tárolja. A rendszer nem mozgat vagy tárol tényleges adatokat kiszolgáló nélküli SQL-készletben.

<column_definition>, ... *n* ]

A CREATE EXTERNAL TABLE támogatja az oszlopnév, az adattípus és a rendezés konfigurálási képességét. Külső táblákon nem használhatja a DEFAULT CONSTRAINT értéket.

>[!IMPORTANT]
>Az oszlopdefinícióknak – az adattípusokat és az oszlopok számát is beleértve – meg kell felelniük a külső fájlok adatainak. Ha ezek között eltérés található, a tényleges adatok lekérdezésekor a fájlsorok vissza lesznek utasítva.

Parquet-fájlokból való olvasáskor csak az olvasni kívánt oszlopokat adhatja meg, a többit pedig kihagyhatja.

LOCATION = '*folder_or_filepath*'

Megadja a mappa vagy a fájl elérési útját és fájlnevét a Azure Blob Storage. A hely a gyökérmappában kezdődik. A gyökérmappa a külső adatforrásban megadott adathely.

Ha megad egy LOCATION mappát, egy kiszolgáló nélküli SQL-készletlekérdezés fog választani a külső táblából, és lekéri a fájlokat a mappából.

> [!NOTE]
> A Hadooptól és a PolyBase-től eltérően a kiszolgáló nélküli SQL-készlet csak akkor ad vissza almappákat, ha az elérési út végén a /** értéket adja meg. A Hadoophoz és a PolyBase-hez hasonlóan nem ad vissza olyan fájlokat, amelyek neve aláhúzással (_) vagy pontokkal (.) kezdődik.

Ebben a példában, ha LOCATION='/webdata/', egy kiszolgáló nélküli SQL-készletlekérdezés ad vissza sorokat a mydata.txt. Nem ad vissza mydata2.txt és mydata3.txt, mert egy almappában találhatók.

![Rekurzív adatok külső táblákhoz](./media/develop-tables-external-tables/folder-traversal.png)

DATA_SOURCE = *external_data_source_name* – Megadja a külső adatok helyét tartalmazó külső adatforrás nevét. Külső adatforrás létrehozásához használja a [CREATE EXTERNAL DATA SOURCE (KÜLSŐ ADATFORRÁS LÉTREHOZÁSA) adatokat.](#create-external-data-source)

FILE_FORMAT = *external_file_format_name* – Megadja a külső adatok fájltípusát és tömörítési módszerét tároló külső fájlformátum-objektum nevét. Külső fájlformátum létrehozásához használja a [CREATE EXTERNAL FILE FORMAT formátumot.](#create-external-file-format)

### <a name="permissions-create-external-table"></a>Engedélyek CREATE EXTERNAL TABLE

Külső táblából való kijelöléshez megfelelő hitelesítő adatokra van szükség listával és olvasási engedélyekkel.

### <a name="example-create-external-table"></a>Példa: CREATE EXTERNAL TABLE

Az alábbi példa egy külső táblát hoz létre. Az első sort adja vissza:

```sql
CREATE EXTERNAL TABLE census_external_table
(
    decennialTime varchar(20),
    stateName varchar(100),
    countyName varchar(100),
    population int,
    race varchar(50),
    sex    varchar(10),
    minAge int,
    maxAge int
)  
WITH (
    LOCATION = '/parquet/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)
GO

SELECT TOP 1 * FROM census_external_table
```

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Külső táblák létrehozása és lekérdezése azure data lake-beli fájlból

A Data Lake feltárási képességeinek használatával most már létrehozhat és lekérdezhet egy külső táblát dedikált SQL-készlet vagy kiszolgáló nélküli SQL-készlet használatával, és kattintson rá a jobb gombbal a fájlra. Az egykattintásos kézmozdulattal külső táblákat hozhat létre a ADLS Gen2 tárfiókból csak Parquet-fájlok esetén támogatott. 

### <a name="prerequisites"></a>Előfeltételek

- Legalább a Storage-blobadatok közreműködője ARM-hozzáférési szerepkörével kell hozzáférnie a munkaterülethez a ADLS Gen2 fiókhoz

- Legalább engedéllyel kell [rendelkeznie](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#permissions-2&preserve-view=true) külső táblák létrehozásához és lekérdezéséhez az SQL-készleten vagy az SQL OD-on

Az Adatok panelen válassza ki azt a fájlt, amelyből létre szeretné hozni a külső táblát:
> [!div class="mx-imgBorder"]
>![externaltable1](./media/develop-tables-external-tables/external-table-1.png)

Megnyílik egy párbeszédpanel. Válassza ki a dedikált SQL-készletet vagy a kiszolgáló nélküli SQL-készletet, adjon nevet a táblának, és válassza a szkript megnyitása lehetőséget:

> [!div class="mx-imgBorder"]
>![externaltable2](./media/develop-tables-external-tables/external-table-2.png)

Az SQL-szkript automatikusan generálja a sémát a fájlból:
> [!div class="mx-imgBorder"]
>![externaltable3](./media/develop-tables-external-tables/external-table-3.png)

Futtassa a szkriptet. A szkript automatikusan futtatja a Select Top 100 *.:
> [!div class="mx-imgBorder"]
>![externaltable4](./media/develop-tables-external-tables/external-table-4.png)

Ezzel létrejött a külső tábla, és a külső tábla tartalmának későbbi feltárására a felhasználó közvetlenül az Adatok panelről tudja lekérdezni:
> [!div class="mx-imgBorder"]
>![externaltable5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>Következő lépések

A [CETAS-cikkben](develop-tables-cetas.md) ellenőrizheti, hogyan mentheti a lekérdezési eredményeket egy külső táblába az Azure Storage-ban. Vagy elkezdheti lekérdezni Apache Spark [külső Azure Synapse lekérdezését.](develop-storage-files-spark-tables.md)
