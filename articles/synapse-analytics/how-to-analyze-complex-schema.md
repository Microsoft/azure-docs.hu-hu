---
title: Séma elemzése tömbökkel és beágyazott struktúrákkal
description: Tömbök és beágyazott struktúrák elemzése Apache Spark és SQL használatával
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: spark
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: a35cdebe5ff4db562b19dc4ceed069a831af4305
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98219997"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Összetett adattípusok elemzése az Azure szinapszis Analyticsben

Ez a cikk az Azure-beli, Azure Cosmos DB-hez készült, az Azure-beli [szinapszis-hivatkozáshoz](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md)tartozó Parquet-fájlok A Spark vagy az SQL segítségével összetett sémákkal, például tömbökkel vagy beágyazott struktúrákkal olvashatja és alakíthatja át az adatátalakítást. A következő példa egyetlen dokumentummal fejeződött be, de a Spark vagy az SQL használatával könnyedén méretezhető több milliárd dokumentumra. A cikkben szereplő kód PySpark (Python) használ.

## <a name="use-case"></a>Használati eset

Az összetett adattípusok egyre gyakoribbak, és kihívást jelentenek az adatmérnökök számára. A beágyazott sémák és tömbök elemzése időigényes és összetett SQL-lekérdezéseket is tartalmazhat. Emellett nehéz lehet átnevezni vagy átadni a beágyazott oszlopok adattípusát. Emellett, ha mélyen beágyazott objektumokat használ, teljesítményproblémák merülhetnek fel.

Az adatmérnököknek ismerniük kell, hogyan kell hatékonyan feldolgozni az összetett adattípusokat, és könnyen elérhetővé tenni azokat mindenki számára. Az alábbi példában a Spark az Azure szinapszis Analytics szolgáltatásban az objektumok olvasására és átalakítására használható az adatkereteken keresztül. Az Azure szinapszis Analytics kiszolgáló nélküli SQL-modelljét használva közvetlenül lekérdezheti az ilyen objektumokat, és visszaküldheti ezeket az eredményeket normál táblázatként.

## <a name="what-are-arrays-and-nested-structures"></a>Mik azok a tömbök és beágyazott struktúrák?

A következő objektum a [Application Insightsból](../azure-monitor/app/app-insights-overview.md)származik. Ebben az objektumban beágyazott struktúrákat és tömböket tartalmaz, amelyek beágyazott struktúrákat tartalmaznak.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>A tömbök és beágyazott struktúrák sémájának példája
Ha az objektum adatkeretének ( **DF**) sémáját az paranccsal nyomtatja ki `df.printschema` , akkor a következő ábrázolás jelenik meg:

* A sárga beágyazott struktúrákat jelöl.
* A zöld a két elemet tartalmazó tömböt jelöl.

[![Kód sárga és zöld kiemeléssel, a séma eredetének megjelenítése](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

`_rid`, `_ts` és `_etag` hozzá lettek adva a rendszerhez, mivel a dokumentum betöltése bekerült a Azure Cosmos db tranzakciós tárolóba.

Az előző adatkeret 5 oszlopra és csak 1 sorra számít. Az átalakítás után a kurátori adatkeret 13 oszlopból és 2 sorból áll, táblázatos formában.

## <a name="flatten-nested-structures-and-explode-arrays"></a>Beágyazott struktúrák összeolvasztása és tömbök alábontása

Az Azure szinapszis Analyticsben a Spark használatával egyszerűen átalakíthatja a beágyazott struktúrákat oszlopokra és tömb elemekre több sorba. A megvalósításhoz kövesse az alábbi lépéseket.

[![A Spark-átalakítások lépéseit bemutató folyamatábra](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

### <a name="define-a-function-to-flatten-the-nested-schema"></a>A beágyazott séma összeolvasztására szolgáló függvény definiálása

Ezt a függvényt módosítás nélkül is használhatja. Hozzon létre egy cellát egy [PySpark jegyzetfüzetben](quickstart-apache-spark-notebook.md) a következő függvénnyel:

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

### <a name="use-the-function-to-flatten-the-nested-schema"></a>A beágyazott séma összeolvasztása a függvény használatával

Ebben a lépésben az adatkeret (**DF**) beágyazott sémáját egy új adatkeretbe () ágyazza be `df_flat` :

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

A megjelenítési függvénynek 10 oszlopot és 1 sort kell visszaadnia. A tömb és a beágyazott elemei még mindig vannak.

### <a name="transform-the-array"></a>A tömb átalakítása

Itt átalakítja a tömböt, az `context_custom_dimensions` adatkereten belül `df_flat` egy új adatkeretbe `df_flat_explode` . A következő kódban azt is megadhatja, hogy melyik oszlopot válassza ki:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

A megjelenítési függvénynek 10 oszlopot és 2 sort kell visszaadnia. A következő lépés a beágyazott sémák összeolvasztása az 1. lépésben meghatározott függvénnyel.

### <a name="use-the-function-to-flatten-the-nested-schema"></a>A beágyazott séma összeolvasztása a függvény használatával

Végül a függvénnyel lelapulhatja az adatkeret beágyazott sémáját `df_flat_explode` egy új adatkeretbe `df_flat_explode_flat` :
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

A megjelenítési függvénynek 13 oszlopot és 2 sort kell megjelenítenie.

Az `printSchema` adatkeret funkciója a `df_flat_explode_flat` következő eredményt adja vissza:

[![A végleges sémát megjelenítő kód](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly"></a>Tömbök és beágyazott szerkezetek olvasása közvetlenül

A kiszolgáló nélküli SQL-modell használatával megtekintheti és létrehozhatja az ilyen objektumokra vonatkozó nézeteket és táblákat.

Először is, az adattárolás módjától függően a felhasználóknak a következő besorolást kell használniuk. A nagybetűkben látható összes érték a használati esetre vonatkozik:

| Tömeges | Formátum |
| ------ | ------ |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |"Parquet" (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; Account = ACCOUNTNAME; adatbázis = databasename; gyűjtemény = COLLECTIONNAME; régió = REGIONTOQUERY ', titkos kulcs = ' YOURSECRET ' |"CosmosDB" (Azure szinapszis-hivatkozás)|


Cserélje le az egyes mezőket a következőképpen:
* A "saját TÖMEGES fenti" az adatforrás kapcsolati karakterlánca, amelyhez csatlakozik.
* A fenti típus a forráshoz való kapcsolódáshoz használt formátum.

```sql
select *
FROM
openrowset(
    BULK 'YOUR BULK ABOVE',
    FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

Két különböző típusú művelet létezik:

- Az első Művelettípus a következő sorában van megadva, amely meghatározza a nevű oszlopot `contextdataeventTime` , amely a beágyazott elemre hivatkozik `Context.Data.eventTime` . 
  ```sql
  contextdataeventTime varchar(50) '$.context.data.eventTime'
  ```

  Ez a sor az nevű oszlopot határozza meg `contextdataeventTime` , amely a beágyazott elemre hivatkozik `Context>Data>eventTime` .

- A második Művelettípus a `cross apply` tömbben lévő egyes elemek új sorainak létrehozásához használatos. Ezután definiálja az egyes beágyazott objektumokat. 
  ```sql
  cross apply openjson (contextcustomdimensions) 
  with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
  ```

  Ha a tömb 5 elemet tartalmaz 4 beágyazott struktúrával, a kiszolgáló nélküli SQL-modell 5 sort és 4 oszlopot ad vissza. A kiszolgáló nélküli SQL-modell képes a lekérdezésre, a tömböt 2 sorba leképezni, és az összes beágyazott struktúrát oszlopokba megjeleníteni.

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan kérdezheti le a Azure Cosmos DBhoz készült szinapszis-hivatkozást a Spark használatával](./synapse-link/how-to-query-analytical-store-spark.md)
* [A Parquet beágyazott típusainak lekérdezése](./sql/query-parquet-nested-types.md)