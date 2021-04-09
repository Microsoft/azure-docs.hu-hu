---
title: JSON-fájlok lekérdezése kiszolgáló nélküli SQL-készlettel
description: Ez a szakasz azt ismerteti, hogyan lehet JSON-fájlokat beolvasni a kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analyticsben.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 5fcf688bbe8a5be2fc10b70950990b7b6ca71df8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225591"
---
# <a name="query-json-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>JSON-fájlok lekérdezése kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analyticsben

Ebből a cikkből megtudhatja, hogyan írhat lekérdezéseket kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analytics szolgáltatásban. A lekérdezés célja, hogy beolvassa a JSON-fájlokat a [OpenRowset](develop-openrowset.md)használatával. 
- Szabványos JSON-fájlok, amelyek több JSON-dokumentumot is tárolnak JSON-tömbként.
- Sor-tagolt JSON-fájlok, ahol a JSON-dokumentumok a New-line karakterrel vannak elválasztva. Az ilyen típusú fájlok általános bővítményei:, `jsonl` `ldjson` és `ndjson` .

## <a name="read-json-documents"></a>JSON-dokumentumok olvasása

A JSON-fájl tartalmának megtekintésének legegyszerűbb módja a fájl URL-címének megadása a függvény számára `OPENROWSET` , a CSV megadása `FORMAT` , valamint `0x0b` a és a értékének beállítása `fieldterminator` `fieldquote` . Ha el kell olvasnia a sorokra tagolt JSON-fájlokat, akkor ez elég. Ha klasszikus JSON-fájllal rendelkezik, meg kell adnia a értékeit `0x0b` `rowterminator` . `OPENROWSET` a függvény a JSON-t elemzi, és az összes dokumentumot a következő formátumban fogja visszaadni:

| doc |
| --- |
|{"date_rep": "2020-07-24", "nap": 24, "hónap": 7, "év": 2020, "Cases": 3, "halálesetek": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-25", "nap": 25, "hónap": 7, "év": 2020, "Cases": 7, "halálesetek": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-26", "nap": 26, "hónap": 7, "év": 2020, "Cases": 4, "halálesetek": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-27", "nap": 27, "hónap": 7, "év": 2020, "Cases": 8, "halálesetek": 0, "geo_id": "AF"}|

Ha a fájl nyilvánosan elérhető, vagy ha az Azure AD-identitása hozzáfér ehhez a fájlhoz, a fájl tartalmát az alábbi példákban látható módon kell megjelennie.

### <a name="read-json-files"></a>JSON-fájlok olvasása

A következő minta lekérdezés a JSON-és a sortöréses JSON-fájlokat olvassa be, és minden dokumentumot külön sorként ad vissza.

```sql
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

Az előző minta lekérdezésben szereplő JSON-dokumentum objektumok tömbjét tartalmazza. A lekérdezés az egyes objektumokat az eredményhalmaz külön soraként adja vissza. Győződjön meg arról, hogy el tudja érni ezt a fájlt. Ha a fájlt SAS-kulccsal vagy egyéni identitással védi, be kell állítania a [kiszolgálói szintű hitelesítő adatokat az SQL-bejelentkezéshez](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential). 

### <a name="data-source-usage"></a>Adatforrás használata

Az előző példa a fájl teljes elérési útját használja. Alternatív megoldásként létrehozhat egy külső adatforrást is, amely a tároló gyökérkönyvtárára mutat, és ezt az adatforrást és a függvényben lévő fájl relatív elérési útját használja `OPENROWSET` :

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.json',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

Ha egy adatforrás SAS-kulccsal vagy egyéni identitással védett, az [adatforrást adatbázis-hatókörű hitelesítő adatokkal](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)is konfigurálhatja.

A következő részekben láthatja, hogyan lehet lekérdezni a JSON-fájlok különböző típusait.

## <a name="parse-json-documents"></a>JSON-dokumentumok elemzése

Az előző példákban található lekérdezések minden JSON-dokumentumot egyetlen sztringként adnak vissza az eredményhalmaz külön sorában. A függvények segítségével `JSON_VALUE` `OPENJSON` elemezheti a JSON-dokumentumok értékeit, és a következő példában látható módon visszaküldheti őket viszonyítási értékként.

| dátum \_ rep | esetekben | földrajzi \_ azonosító |
| --- | --- | --- |
| 2020-07-24 | 3 | AF |
| 2020-07-25 | 7 | AF |
| 2020-07-26 | 4 | AF |
| 2020-07-27 | 8| AF |

### <a name="sample-json-document"></a>JSON-dokumentum mintája

A lekérdezés példákkal olvassa be a dokumentumokat tartalmazó *JSON* -fájlokat a következő szerkezettel:

```json
{
    "date_rep":"2020-07-24",
    "day":24,"month":7,"year":2020,
    "cases":13,"deaths":0,
    "countries_and_territories":"Afghanistan",
    "geo_id":"AF",
    "country_territory_code":"AFG",
    "continent_exp":"Asia",
    "load_date":"2020-07-25 00:05:14",
    "iso_country":"AF"
}
```

> [!NOTE]
> Ha ezeket a dokumentumokat sortöréssel elválasztott JSON-ként tárolja, be kell állítania és 0x0b kell lennie `FIELDTERMINATOR` `FIELDQUOTE` . Ha szabványos JSON-formátummal rendelkezik, a 0x0b értékre kell állítania `ROWTERMINATOR` .

### <a name="query-json-files-using-json_value"></a>JSON-fájlok lekérdezése JSON_VALUE használatával

Az alábbi lekérdezés azt mutatja be, hogyan használhatók a [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) a skaláris értékek ( `date_rep` , `countries_and_territories` , `cases` ) JSON-dokumentumokból való lekéréséhez:

```sql
select
    JSON_VALUE(doc, '$.date_rep') AS date_reported,
    JSON_VALUE(doc, '$.countries_and_territories') AS country,
    CAST(JSON_VALUE(doc, '$.deaths') AS INT) as fatal,
    JSON_VALUE(doc, '$.cases') as cases,
    doc
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
order by JSON_VALUE(doc, '$.geo_id') desc
```

Miután kicsomagolta a JSON-tulajdonságokat egy JSON-dokumentumból, megadhatja az oszlopok aliasait, és igény szerint elvégezheti a szöveges értéket valamilyen típusra.

### <a name="query-json-files-using-openjson"></a>JSON-fájlok lekérdezése a OPENJSON UTASÍTÁSSAL használatával

A következő lekérdezés a [openjson utasítással](/sql/t-sql/functions/openjson-transact-sql?view=azure-sqldw-latest&preserve-view=true)-t használja. A szolgáltatás lekéri a szerbiai COVID-statisztikát:

```sql
select
    *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
    cross apply openjson (doc)
        with (  date_rep datetime2,
                cases int,
                fatal int '$.deaths',
                country varchar(100) '$.countries_and_territories')
where country = 'Serbia'
order by country, date_rep desc;
```
Az eredmények ugyanúgy működnek, mint a függvény használatával visszaadott eredmények `JSON_VALUE` . Bizonyos esetekben a `OPENJSON` következőket teheti előnyben `JSON_VALUE` :
- A `WITH` záradékban explicit módon állíthatja be az oszlop aliasneveit és az összes tulajdonság típusát. A függvényt nem kell a `CAST` lista minden oszlopában elhelyeznie `SELECT` .
- `OPENJSON` gyorsabb lehet, ha nagy számú tulajdonságot ad vissza. Ha csak a 1-2-es tulajdonságokat adja vissza, lehet, hogy a `OPENJSON` függvény terhelést okoz.
- `OPENJSON`Ha az egyes dokumentumokból össze kell elemezni a tömböt, akkor a függvényt kell használnia, és a fölérendelt sorhoz kell csatlakoztatnia.

## <a name="next-steps"></a>Következő lépések

A sorozat következő cikkei a következőket szemléltetik:

- [Mappák és több fájl lekérdezése](query-folders-multiple-csv-files.md)
- [Nézetek létrehozása és használata](create-use-views.md)
