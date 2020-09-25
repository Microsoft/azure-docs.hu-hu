---
title: JSON-fájlok lekérdezése az SQL on-demand használatával (előzetes verzió)
description: Ez a szakasz azt ismerteti, hogyan olvashatók be a JSON-fájlok az SQL igény szerinti használatával az Azure szinapszis Analytics szolgáltatásban.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 0757c867d46144ac9fb9b9eca8b2a588aeeb15d6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91288324"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>JSON-fájlok lekérdezése az SQL on-demand (előzetes verzió) használatával az Azure szinapszis Analyticsben

Ebből a cikkből megtudhatja, hogyan írhat egy lekérdezést az SQL on-demand (előzetes verzió) használatával az Azure szinapszis Analytics szolgáltatásban. A lekérdezés célja, hogy beolvassa a JSON-fájlokat a [OpenRowset](develop-openrowset.md)használatával. 
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

Ez a lekérdezés minden JSON-dokumentumot az eredményhalmaz külön soraként ad vissza. Győződjön meg arról, hogy el tudja érni ezt a fájlt. Ha a fájlt SAS-kulccsal vagy egyéni identitással védi, be kell állítania a [kiszolgálói szintű hitelesítő adatokat az SQL-bejelentkezéshez](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential). 

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

Az alábbi lekérdezés azt mutatja be, hogyan használhatók a [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a skaláris értékek (cím, KÖZZÉTEVŐ) JSON-dokumentumokból való lekéréséhez:

```sql
select
    JSON_VALUE(doc, '$.date_rep') AS date_reported,
    JSON_VALUE(doc, '$.countries_and_territories') AS country,
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

### <a name="query-json-files-using-openjson"></a>JSON-fájlok lekérdezése a OPENJSON UTASÍTÁSSAL használatával

A következő lekérdezés a [openjson utasítással](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-t használja. A szolgáltatás lekéri a szerbiai COVID-statisztikát:

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

## <a name="next-steps"></a>Következő lépések

A sorozat következő cikkei a következőket szemléltetik:

- [Mappák és több fájl lekérdezése](query-folders-multiple-csv-files.md)
- [Nézetek létrehozása és használata](create-use-views.md)
