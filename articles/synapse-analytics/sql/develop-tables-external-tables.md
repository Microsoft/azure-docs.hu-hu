---
title: Külső táblák használata a szinapszis SQL használatával
description: Adatfájlok olvasása vagy írása a szinapszis SQL-sel
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 78e4b35feb4e830a9f4335614a55d49ca90cd791
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667634"
---
# <a name="use-external-tables-with-synapse-sql"></a>Külső táblák használata a szinapszis SQL használatával

A külső tábla a Hadoop, az Azure Storage blob vagy a Azure Data Lake Storageban található adatforrásra mutat. A külső táblákból adatok olvashatók be a fájlokból, vagy adatok írhatók az Azure Storage-ban lévő fájlokba. A szinapszis SQL használatával külső táblákat is használhat a dedikált SQL-készletbe vagy kiszolgáló nélküli SQL-készletbe történő adatolvasásra és-írásra.

## <a name="external-tables-in-dedicated-sql-pool-and-serverless-sql-pool"></a>Külső táblák a dedikált SQL-készletben és a kiszolgáló nélküli SQL-készletben

### <a name="dedicated-sql-pool"></a>[Dedikált SQL-készlet](#tab/sql-pool) 

A dedikált SQL-készletben a következőhöz használhat külső táblát:

- Azure Blob Storage és Azure Data Lake Gen2 Transact-SQL-utasításokkal való lekérdezése.
- Adatok importálása és tárolása az Azure Blob Storage és Azure Data Lake Storage a dedikált SQL-készletbe.

Ha a [CREATE TABLE as Select](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) utasítással együtt használja, a külső táblából való kijelöléssel az adatok importálása az SQL-készletben található táblába történik. A [copy utasításon](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)kívül a külső táblák is hasznosak az adatok betöltéséhez. 

A betöltési oktatóanyagért lásd: az [Azure Blob Storageból történő adatok betöltésének alapja](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)

### <a name="serverless-sql-pool"></a>[Kiszolgáló nélküli SQL-készlet](#tab/sql-on-demand)

Kiszolgáló nélküli SQL-készlet esetén a következő külső táblát fogja használni:

- Adatlekérdezés az Azure Blob Storage vagy Azure Data Lake Storage Transact-SQL-utasításokkal
- A kiszolgáló nélküli SQL-készlet lekérdezési eredményeinek tárolása az Azure Blob Storage vagy Azure Data Lake Storage [CETAS](develop-tables-cetas.md) használatával

A következő lépésekkel hozhat létre külső táblákat a kiszolgáló nélküli SQL-készlet használatával:

1. KÜLSŐ ADATFORRÁS LÉTREHOZÁSA
2. CREATE EXTERNAL FILE FORMAT
3. KÜLSŐ TÁBLA LÉTREHOZÁSA

---

### <a name="security"></a>Biztonság

A felhasználónak engedéllyel kell rendelkeznie `SELECT` a külső táblán az adatolvasáshoz.
Külső tábla, amely az Azure Storage mögöttes hozzáférését az adatforrásban definiált adatbázis-hatókörű hitelesítő adatok használatával az alábbi szabályok alapján éri el:
- A hitelesítő adatok nélküli adatforrás lehetővé teszi, hogy a külső táblák hozzáférhessenek az Azure Storage-ban nyilvánosan elérhető fájlokhoz.
- Az adatforrás rendelkezhet olyan hitelesítő adatokkal, amelyek lehetővé teszik, hogy a külső táblák csak az Azure Storage-ban található fájlokat férhessenek hozzá az SAS-jogkivonat vagy a munkaterület által felügyelt identitás használatával – példák [a Storage Files Storage hozzáférés-vezérlésének fejlesztése](develop-storage-files-storage-access-control.md#examples) című cikkben

> [!IMPORTANT]
> A dedikált SQL-készletben a hitelesítő adatok nélkül létrehozott adatforrás lehetővé teszi, hogy az Azure AD-felhasználók hozzáférjenek a Storage-fájlokhoz az Azure AD-identitásuk használatával. A kiszolgáló nélküli SQL-készletben létre kell hoznia egy adatforrást egy olyan adatbázis-hatókörű hitelesítő adatokkal, amely rendelkezik `IDENTITY='User Identity'` tulajdonsággal – [példák itt](develop-storage-files-storage-access-control.md#examples)láthatók.

## <a name="create-external-data-source"></a>KÜLSŐ ADATFORRÁS LÉTREHOZÁSA

A külső adatforrások a Storage-fiókokhoz való kapcsolódáshoz használatosak. A teljes dokumentációt [itt](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)találja.

### <a name="syntax-for-create-external-data-source"></a>KÜLSŐ ADATFORRÁS létrehozásának szintaxisa

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

### <a name="arguments-for-create-external-data-source"></a>A külső ADATFORRÁS LÉTREHOZÁSához szükséges argumentumok

data_source_name

Megadja az adatforrás felhasználó által definiált nevét. A névnek egyedinek kell lennie az adatbázison belül.

#### <a name="location"></a>Hely
LOCATION = `'<prefix>://<path>'`   – Megadja a kapcsolati protokollt és a külső adatforrás elérési útját. A következő mintákat lehet használni a helyen:

| Külső adatforrás        | Hely előtagja | Hely elérési útja                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Blob Storage          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Azure Blob Storage          | `http[s]`       | `<storage_account>.blob.core.windows.net/<container>/subfolders` |
| 1. generációs Azure Data Lake Store | `http[s]`       | `<storage_account>.azuredatalakestore.net/webhdfs/v1` |
| 2. generációs Azure Data Lake Store | `http[s]`       | `<storage_account>.dfs.core.windows.net/<container>/subfolders`  |

`https:` az előtag lehetővé teszi az elérési út almappájának használatát.

#### <a name="credential"></a>Hitelesítő adat
HITELESÍTŐADAT = `<database scoped credential>` opcionális hitelesítő adat, amely az Azure Storage-ban való hitelesítéshez használatos. A hitelesítő adatok nélküli külső adatforrás hozzáférhet a nyilvános Storage-fiókhoz. 

A dedikált SQL-készletbe tartozó hitelesítő adatok nélküli külső adatforrások a hívó Azure AD-identitásával férnek hozzá a fájlokhoz a tárolóban. A hitelesítő adatokkal rendelkező kiszolgáló nélküli SQL-készlet külső adatforrása a  `IDENTITY='User Identity'` hívó Azure ad-identitását használja a fájlok eléréséhez.
- A dedikált SQL-készletben az adatbázis-hatókörű hitelesítő adatok egyéni alkalmazás-identitást, munkaterület által felügyelt identitást vagy SAK-kulcsot adhatnak meg. 
- A kiszolgáló nélküli SQL-készletben az adatbázis-hatókörrel rendelkező hitelesítő adatok megadhatják a hívó Azure AD-identitását, a munkaterület felügyelt identitását vagy az SAS-kulcsot. 

#### <a name="type"></a>TÍPUS
TYPE = `HADOOP` a kötelező beállítás a DEDIKÁLT SQL-készletben, és megadja, hogy a rendszer az alapul szolgáló fájlok eléréséhez használja a viszonyítási technológiát. Ez a paraméter nem használható olyan kiszolgáló nélküli SQL-készletben, amely beépített natív olvasót használ.

### <a name="example-for-create-external-data-source"></a>Példa külső ADATFORRÁS létrehozására

#### <a name="dedicated-sql-pool"></a>[Dedikált SQL-készlet](#tab/sql-pool)

Az alábbi példa egy külső adatforrást hoz létre Azure Data Lake Gen2 a New York-i adatkészletre mutat:

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

Az alábbi példa egy külső adatforrást hoz létre Azure Data Lake Gen2, amely SAS-hitelesítő adatokkal érhető el:

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

Az alábbi példa egy külső adatforrást hoz létre Azure Data Lake Gen2, amely a nyilvánosan elérhető New York-beli adatkészletre mutat:

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
```
---

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Létrehoz egy külső fájlformátum-objektumot, amely meghatározza az Azure Blob Storage vagy Azure Data Lake Storage tárolt külső adatforrásokat. Külső fájlformátum létrehozása előfeltétel egy külső tábla létrehozásához. A teljes dokumentáció [itt](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)található.

Külső fájlformátum létrehozásával megadhatja a külső tábla által hivatkozott adatok tényleges elrendezését.

### <a name="syntax-for-create-external-file-format"></a>KÜLSŐ FÁJLFORMÁTUM létrehozásának szintaxisa

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


### <a name="arguments-for-create-external-file-format"></a>Argumentumok külső FÁJLFORMÁTUM LÉTREHOZÁSához

file_format_name – a külső fájlformátum nevét adja meg.

FORMAT_TYPE = [PARKETTA | DELIMITEDTEXT] – a külső adatforrások formátumát adja meg.

- PARKETTA – a parketta formátumát adja meg.
- DELIMITEDTEXT – olyan szöveges formátumot ad meg, amelyben oszlop elválasztó karakterek, más néven mező-lezárók.

FIELD_TERMINATOR = *field_terminator* – csak a tagolt szövegfájlokra vonatkozik. A lezáró mező egy vagy több olyan karaktert határoz meg, amely az egyes mezők (oszlopok) végét jelöli a szöveg tagolt fájljában. Az alapértelmezett érték a cső karaktere (ꞌ | ꞌ).

Angol nyelvű Példák:

- FIELD_TERMINATOR = "|"
- FIELD_TERMINATOR = ""
- FIELD_TERMINATOR = ꞌ \ t ꞌ

STRING_DELIMITER = *string_delimiter* – megadja a mező lezáró értékét karakterlánc típusú adatként a szöveges tagolt fájlban. A karakterlánc elválasztója egy vagy több karakter hosszúságú, és aposztrófok között van. Az alapértelmezett érték az üres karakterlánc ("").

Angol nyelvű Példák:

- STRING_DELIMITER = ""
- STRING_DELIMITER = ' * '
- STRING_DELIMITER = ꞌ, ꞌ

FIRST_ROW = *First_row_int* – megadja az elsőként olvasott sor számát, és az összes fájlra vonatkozik. Ha az értéket két értékre állítja, a rendszer az összes fájl (fejlécsor) első sorát kihagyja a betöltéskor. A sorok kihagyása a sor lezárói (/r/n,/r,/n) létezése alapján történik.

USE_TYPE_DEFAULT = {TRUE | **Hamis** } – Megadja, hogy a rendszer hogyan kezelje az adatok szövegfájlból való beolvasása során a hiányzó értékeket a tagolt szövegfájlokban.

TRUE (igaz) – Ha az adatok szövegfájlból való beolvasását végzi, az összes hiányzó értéket az alapértelmezett érték adattípusával tárolja a külső tábla definíciójának megfelelő oszlopához. Például cserélje le a hiányzó értéket a következőre:

- 0, ha az oszlop numerikus oszlopként van meghatározva. A decimális oszlopok nem támogatottak, és hibát okoznak.
- Üres karakterlánc (""), ha az oszlop egy karakterlánc típusú oszlop.
- 1900-01-01, ha az oszlop egy Date oszlop.

FALSE – az összes hiányzó értéket tárolja NULL értékként. A tagolt szövegfájl NULL értékének használatával tárolt NULL értékek a "NULL" sztringként lesznek importálva.

Encoding = {' UTF8 ' | "UTF16"} – a kiszolgáló nélküli SQL-készlet képes olvasni az UTF8-t és a UTF16-kódolású tagolt szöveges fájlokat.

DATA_COMPRESSION = *data_compression_method* – ez az argumentum határozza meg a külső adattömörítési módszert. 

A PARQUEt fájlformátum típusa a következő tömörítési módszereket támogatja:

- DATA_COMPRESSION = ' org. Apache. Hadoop. IO. compress. GzipCodec '
- DATA_COMPRESSION = ' org. Apache. Hadoop. IO. compress. SnappyCodec '

A parketta külső tábláiból való olvasáskor a rendszer figyelmen kívül hagyja ezt az argumentumot, de külső táblákba való íráskor használja a [CETAS](develop-tables-cetas.md)használatával.

A DELIMITEDTEXT fájlformátum típusa a következő tömörítési módszert támogatja:

- DATA_COMPRESSION = ' org. Apache. Hadoop. IO. compress. GzipCodec '

A PARSER_VERSION = "parser_version" a fájlok olvasásakor használandó elemző verziót határozza meg. A részletekért olvassa el PARSER_VERSION argumentumot a [OpenRowset argumentumokban](develop-openrowset.md#arguments) .

### <a name="example-for-create-external-file-format"></a>Példa külső FÁJLFORMÁTUM létrehozására

Az alábbi példa külső fájlformátumot hoz létre a népszámlálási fájlokhoz:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>KÜLSŐ TÁBLA LÉTREHOZÁSA

A CREATE EXTERNAL TABLE parancs létrehoz egy külső táblázatot a szinapszis SQL számára az Azure-Blob Storage vagy Azure Data Lake Storage tárolt adateléréshez. 

### <a name="syntax-for-create-external-table"></a>KÜLSŐ tábla LÉTREHOZÁSi szintaxisa

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

### <a name="arguments-create-external-table"></a>Argumentumok külső tábla létrehozása

*{adatbázisnév. schema_name. table_name | schema_name. table_name | table_name}*

A létrehozandó tábla egy vagy három részének neve. Külső tábla esetén a kiszolgáló nélküli SQL-készlet csak a tábla metaadatait tárolja. Nem történik tényleges adatok áthelyezése vagy tárolása a kiszolgáló nélküli SQL-készletben.

<column_definition>,... *n* ]

KÜLSŐ tábla létrehozása lehetővé teszi az oszlopnév, az adattípus és a rendezés konfigurálását. Az alapértelmezett MEGKÖTÉS nem használható külső táblákon.

>[!IMPORTANT]
>Az oszlopdefinícióknak – az adattípusokat és az oszlopok számát is beleértve – meg kell felelniük a külső fájlok adatainak. Ha ezek között eltérés található, a tényleges adatok lekérdezésekor a fájlsorok vissza lesznek utasítva.

A Parquet-fájlokból való olvasáskor csak azokat az oszlopokat adhatja meg, amelyeket el szeretne olvasni, és kihagyhatja a többiet.

LOCATION = '*folder_or_filepath*'

Megadja a tényleges adatok mappáját vagy fájljának elérési útját és fájlnevét az Azure Blob Storageban. A hely a gyökérmappa alapján kezdődik. A gyökérmappa a külső adatforrásban megadott adathely.

Ha a mappa helyét adja meg, a kiszolgáló nélküli SQL-készlet lekérdezése a külső táblából lesz kiválasztva, és fájlokat kér le a mappából.

> [!NOTE]
> A Hadoop és a bázistól eltérően a kiszolgáló nélküli SQL-készlet nem ad vissza almappákat. Azokat a fájlokat adja vissza, amelyekhez a fájlnév aláhúzással (_) vagy ponttal (.) kezdődik.

Ebben a példában, ha a LOCATION = '/WebData/', egy kiszolgáló nélküli SQL Pool-lekérdezés, a mydata.txt és _hidden.txt sorait fogja visszaadni. Nem ad vissza mydata2.txt és mydata3.txt, mert egy almappában találhatók.

![Rekurzív adatértékek külső táblákhoz](./media/develop-tables-external-tables/folder-traversal.png)

DATA_SOURCE = *external_data_source_name* – megadja a külső adatforrás nevét, amely a külső adatforrások helyét tartalmazza. Külső adatforrás létrehozásához használja a [külső adatforrás létrehozása](#create-external-data-source)lehetőséget.

FILE_FORMAT = *external_file_format_name* – annak a külső fájlformátum-objektumnak a nevét adja meg, amely a külső adattípust és a tömörítési módszert tárolja. Külső fájlformátum létrehozásához használja a [create External File Format (külső fájlformátum létrehozása) formátumot](#create-external-file-format).

### <a name="permissions-create-external-table"></a>Engedélyek külső tábla létrehozása

Egy külső táblából való kiválasztáshoz megfelelő hitelesítő adatokra van szükség a listához és az olvasási engedélyekhez.

### <a name="example-create-external-table"></a>Példa külső tábla létrehozására

A következő példa egy külső táblát hoz létre. Az első sort adja vissza:

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

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Külső táblák létrehozása és lekérdezése egy fájlból Azure Data Lake

A Data Lake feltárási képességek használatával létrehozhat és lekérdezheti a külső táblákat a dedikált SQL Pool vagy a kiszolgáló nélküli SQL-készlet használatával, és egyszerűen rákattinthat a fájlra. Az egykattintásos kézmozdulat, amellyel külső táblákat hozhat létre a ADLS Gen2 Storage-fiókból, csak a Parquet-fájlok esetében támogatott. 

### <a name="prerequisites"></a>Előfeltételek

- A munkaterülethez hozzáféréssel kell rendelkeznie legalább a Storage blob adatközreműködői ARM hozzáférési szerepkörrel a ADLS Gen2 fiókhoz

- A külső táblák létrehozásához és lekérdezéséhez legalább [engedélyre](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#permissions-2&preserve-view=true) van szükség az SQL-készletben vagy az SQL od-ban.

Az adatok panelen válassza ki azt a fájlt, amelyből létre szeretné hozni a külső táblát:
> [!div class="mx-imgBorder"]
>![externaltable1](./media/develop-tables-external-tables/external-table-1.png)

Ekkor megnyílik egy párbeszédpanel. Válassza a dedikált SQL Pool vagy a kiszolgáló nélküli SQL-készlet lehetőséget, adjon meg egy nevet a táblának, és válassza a parancsfájl megnyitása:

> [!div class="mx-imgBorder"]
>![externaltable2](./media/develop-tables-external-tables/external-table-2.png)

Az SQL-parancsfájl automatikusan létrejön a sémából a fájlból:
> [!div class="mx-imgBorder"]
>![externaltable3](./media/develop-tables-external-tables/external-table-3.png)

Futtassa a szkriptet. A szkript automatikusan futtat egy Select Top 100 * elemet.:
> [!div class="mx-imgBorder"]
>![externaltable4](./media/develop-tables-external-tables/external-table-4.png)

A külső tábla most létrejön, a külső tábla tartalmának későbbi feltárásához a felhasználó közvetlenül az adatok ablaktábláról kérdezheti le:
> [!div class="mx-imgBorder"]
>![externaltable5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>Következő lépések

A lekérdezés eredményeinek az Azure Storage külső táblájába való mentéséhez olvassa el a [CETAS](develop-tables-cetas.md) című cikket. Vagy megkezdheti [a Apache Spark lekérdezését az Azure szinapszis külső tábláihoz](develop-storage-files-spark-tables.md).
