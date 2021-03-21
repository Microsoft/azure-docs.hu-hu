---
title: A Linux Foundation Delta Lake használatának áttekintése az Azure szinapszis Analytics Apache Sparkban
description: Ismerje meg, hogyan használható a Delta Lake a Apache Spark az Azure szinapszis Analyticshez, hogyan hozhatók létre és használhatók a savas tulajdonságokkal rendelkező táblák.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: euang
ms.topic: overview
ms.subservice: spark
ms.date: 07/28/2020
ms.author: euang
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 1e0dfd597e7f445eeba6cef332d8ea12b27dc3a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676311"
---
# <a name="linux-foundation-delta-lake-overview"></a>A Linux Foundation Delta Lake áttekintése

Ez a cikk az eredeti [munkatársaival](https://docs.delta.io/latest/quick-start.html)kapcsolatos egyértelműség kedvéért lett kialakítva. Ez a cikk segítséget nyújt a [Delta Lake](https://delta.io)főbb funkcióinak gyors megismerésében. A cikk kódrészleteket tartalmaz, amelyek bemutatják, hogyan lehet olvasni és írni a Delta Lake-táblákat interaktív, batch és streaming lekérdezésekben. A kódrészletek a következő jegyzetfüzetek [PySpark](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Python.ipynb)is elérhetők itt: [Scala itt](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Scala/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Scala.ipynb)és [C#](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Spark.NET%20C%23/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20CSharp.ipynb)

A következőket fogjuk lefedni:

* Tábla létrehozása
* Adatok olvasása
* Tábla-adatbázis frissítése
* Táblázat-adatértékek felülírása
* Feltételes frissítés felülírás nélkül
* Az idő régebbi verzióinak olvasása a Time Travel használatával
* Adatfolyam írása egy táblába
* Egy táblázat változásait tartalmazó adatfolyam olvasása
* SQL-támogatás

## <a name="configuration"></a>Konfiguráció

Győződjön meg róla, hogy az alábbi módon módosítja a környezetét.

:::zone pivot = "programming-language-python"

```python
import random

session_id = random.randint(0,1000000)
delta_table_path = "/delta/delta-table-{0}".format(session_id)

delta_table_path
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var sessionId = (new Random()).Next(10000000);
var deltaTablePath = $"/delta/delta-table-{sessionId}";

deltaTablePath
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val sessionId = scala.util.Random.nextInt(1000000)
val deltaTablePath = s"/delta/delta-table-$sessionId";
```

::: zone-end

Eredmények:

'/delta/delta-table-335323'

## <a name="create-a-table"></a>Tábla létrehozása

A Delta Lake-tábla létrehozásához írjon egy DataFrame egy DataFrame a különbözeti formátumban. A formátum a következő módon módosítható: parketta, CSV, JSON stb.

Az alábbi kód bemutatja, hogyan hozhat létre új Delta Lake-táblázatot a DataFrame következtetett séma használatával.

:::zone pivot = "programming-language-python"

```python
data = spark.range(0,5)
data.show()
data.write.format("delta").save(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(0,5);
data.Show();
data.Write().Format("delta").Save(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(0, 5)
data.show
data.write.format("delta").save(deltaTablePath)
```

::: zone-end

Eredmények:

| ID (Azonosító)|
|---|
|  0|
|  1|
|  2|
|  3|
|  4|

## <a name="read-data"></a>Adatok olvasása

A Delta Lake-táblában lévő adatolvasáshoz a fájlok elérési útját és a különbözeti formátumot kell megadnia.

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Load(deltaTablePath);
df.Show()
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").load(deltaTablePath)
df.show()
```

::: zone-end

Eredmények:

| ID (Azonosító)|
|---|
|  1|
|  3|
|  4|
|  0|
|  2|

Az eredmények sorrendje eltér a fentitől, mert az eredmények kihelyezése előtt nem adta meg explicit módon az eredményeket.

## <a name="update-table-data"></a>Tábla-adatbázis frissítése

A Delta Lake a standard DataFrame API-k használatával több műveletet is támogat a táblázatok módosításához, ez az egyik a Delta Format által hozzáadott nagy fejlesztéseknek. A következő példa egy batch-feladatot futtat, amely felülírja a táblában lévő adatok felülírását.

:::zone pivot = "programming-language-python"

```python
data = spark.range(5,10)
data.write.format("delta").mode("overwrite").save(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(5,10);
data.Write().Format("delta").Mode("overwrite").Save(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(5, 10)
data.write.format("delta").mode("overwrite").save(deltaTablePath)
df.show()
```

::: zone-end

Eredmények:

| ID (Azonosító)|
|---|
|  7|
|  8|
|  5|
|  9|
|  6|

Itt láthatja, hogy mind az öt rekord frissítve lett az új értékek tárolására.

## <a name="save-as-catalog-tables"></a>Mentés a katalógus tábláiba

A Delta Lake képes a felügyelt vagy külső katalógus tábláiba írni.

:::zone pivot = "programming-language-python"

```python
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql("CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{0}'".format(delta_table_path))
spark.sql("SHOW TABLES").show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
data.Write().Format("delta").SaveAsTable("ManagedDeltaTable");
spark.Sql($"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{deltaTablePath}'");
spark.Sql("SHOW TABLES").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql(s"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '$deltaTablePath'")
spark.sql("SHOW TABLES").show
```

::: zone-end

Eredmények:

|adatbázis|         tableName|isTemporary|
|--------|------------------|-----------|
| alapértelmezett|externaldeltatable|      hamis|
| alapértelmezett| manageddeltatable|      hamis|

Ezzel a kóddal létrehozott egy új táblát a katalógusban egy meglévő dataframe, amelyet felügyelt táblázatnak nevezünk. Ezután definiált egy új külső táblát a katalógusban, amely egy meglévő helyet használ, amelyet külső táblázatnak nevezünk. A kimenetben mindkét táblát láthatja, a létrehozásuk módjától függetlenül, a katalógusban látható.

Most megtekintheti mindkét táblázat kiterjesztett tulajdonságait

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ManagedDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=false)
```

::: zone-end

Eredmények:

|col_name                    |data_type                                                                                                    |megjegyzés|
|----------------------------|-------------------------------------------------------------------------------------------------------------|-------|
|id                          |bigint                                                                                                       |null   |
|                            |                                                                                                             |       |
|Részletes táblázat adatai  |                                                                                                             |       |
|Adatbázis                    |alapértelmezett                                                                                                      |       |
|Tábla                       |manageddeltatable                                                                                            |       |
|Tulajdonos                       |megbízható szolgáltatás – felhasználó                                                                                         |       |
|Létrehozás ideje                |SAT Apr 25 00:35:34 UTC 2020                                                                                 |       |
|Utolsó hozzáférés                 |Thu Jan 01 00:00:00 UTC 1970                                                                                 |       |
|Created By (Létrehozó)                  |Spark 2.4.4.2.6.99.201 – 11401300                                                                              |       |
|Típus                        |FELÜGYELT                                                                                                      |       |
|Szolgáltató                    |delta                                                                                                        |       |
|Tábla tulajdonságai            |[transient_lastDdlTime = 1587774934]                                                                           |       |
|Statisztika                  |2407 bájt                                                                                                   |       |
|Hely                    |abfss://data @ <data lake> . DFS.Core.Windows.net/Synapse/workspaces/ <workspace name> /Warehouse/manageddeltatable|       |
|Serde-könyvtár               |org. Apache. Hadoop. kaptár. serde2. lusta. LazySimpleSerDe                                                           |       |
|InputFormat                 |org. Apache. Hadoop. mapred. SequenceFileInputFormat                                                             |       |
|OutputFormat                |org. Apache. Hadoop. kaptár. QL. IO. HiveSequenceFileOutputFormat                                                    |       |
|Tárolási tulajdonságok          |[szerializálás. Format = 1]                                                                                     |       |

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ExternalDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=false)
```

::: zone-end

Eredmények:

|col_name                    |data_type                                                             |megjegyzés|
|----------------------------|----------------------------------------------------------------------|-------|
|id                          |bigint                                                                |null   |
|                            |                                                                      |       |
|Részletes táblázat adatai  |                                                                      |       |
|Adatbázis                    |alapértelmezett                                                               |       |
|Tábla                       |externaldeltatable                                                    |       |
|Tulajdonos                       |megbízható szolgáltatás – felhasználó                                                  |       |
|Létrehozás ideje                |SAT Apr 25 00:35:38 UTC 2020                                          |       |
|Utolsó hozzáférés                 |Thu Jan 01 00:00:00 UTC 1970                                          |       |
|Created By (Létrehozó)                  |Spark 2.4.4.2.6.99.201 – 11401300                                       |       |
|Típus                        |KÜLSŐ                                                              |       |
|Szolgáltató                    |KÜLÖNBÖZETI                                                                 |       |
|Tábla tulajdonságai            |[transient_lastDdlTime = 1587774938]                                    |       |
|Hely                    |abfss://data @ <data lake> . DFS.Core.Windows.net/Delta/Delta-Table-587152|       |
|Serde-könyvtár               |org. Apache. Hadoop. kaptár. serde2. lusta. LazySimpleSerDe                    |       |
|InputFormat                 |org. Apache. Hadoop. mapred. SequenceFileInputFormat                      |       |
|OutputFormat                |org. Apache. Hadoop. kaptár. QL. IO. HiveSequenceFileOutputFormat             |       |
|Tárolási tulajdonságok          |[szerializálás. Format = 1]                                              |       |

## <a name="conditional-update-without-overwrite"></a>Feltételes frissítés felülírás nélkül

A Delta Lake programozási API-kat biztosít a feltételes frissítéshez, a törléshez és az egyesítéshez (ez általában upsert) az adattáblákba.

:::zone pivot = "programming-language-python"

```python
from delta.tables import *
from pyspark.sql.functions import *

delta_table = DeltaTable.forPath(spark, delta_table_path)

delta_table.update(
  condition = expr("id % 2 == 0"),
  set = { "id": expr("id + 100") })
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Delta;
using Microsoft.Spark.Extensions.Delta.Tables;
using Microsoft.Spark.Sql;
using static Microsoft.Spark.Sql.Functions;

var deltaTable = DeltaTable.ForPath(deltaTablePath);

deltaTable.Update(
  condition: Expr("id % 2 == 0"),
  set: new Dictionary<string, Column>(){{ "id", Expr("id + 100") }});
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
import io.delta.tables._
import org.apache.spark.sql.functions._

val deltaTable = DeltaTable.forPath(deltaTablePath)

// Update every even value by adding 100 to it
deltaTable.update(
  condition = expr("id % 2 == 0"),
  set = Map("id" -> expr("id + 100")))
deltaTable.toDF.show
```

::: zone-end

Eredmények:

| ID (Azonosító)|
|---|
|106|
|108|
|  5|
|  7|
|  9|

Itt csak a 100-et adta hozzá minden még AZONOSÍTÓhoz.

:::zone pivot = "programming-language-python"

```python
delta_table.delete("id % 2 == 0")
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.Delete(condition: Expr("id % 2 == 0"));
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.delete(condition = expr("id % 2 == 0"))
deltaTable.toDF.show
```

::: zone-end

Eredmények:

| ID (Azonosító)|
|---|
|  5|
|  7|
|  9|

Figyelje meg, hogy minden páros sor törölve lett.

:::zone pivot = "programming-language-python"

```python
new_data = spark.range(0,20).alias("newData")

delta_table.alias("oldData")\
    .merge(new_data.alias("newData"), "oldData.id = newData.id")\
    .whenMatchedUpdate(set = { "id": lit("-1")})\
    .whenNotMatchedInsert(values = { "id": col("newData.id") })\
    .execute()

delta_table.toDF().show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var newData = spark.Range(20).As("newData");

deltaTable
    .As("oldData")
    .Merge(newData, "oldData.id = newData.id")
    .WhenMatched()
        .Update(new Dictionary<string, Column>() {{"id", Lit("-1")}})
    .WhenNotMatched()
        .Insert(new Dictionary<string, Column>() {{"id", Col("newData.id")}})
    .Execute();

deltaTable.ToDF().Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val newData = spark.range(0, 20).toDF

deltaTable.as("oldData").
  merge(
    newData.as("newData"),
    "oldData.id = newData.id").
  whenMatched.
  update(Map("id" -> lit(-1))).
  whenNotMatched.
  insert(Map("id" -> col("newData.id"))).
  execute()

deltaTable.toDF.show()
```

::: zone-end

Eredmények:

| ID (Azonosító)|
|---|
| 18|
| 15|
| 19|
|  2|
|  1|
|  6|
|  8|
|  3|
| -1|
| 10|
| 13|
|  0|
| 16|
|  4|
| -1|
| 12|
| 11|
| 14|
| -1|
| 17|

Itt van a meglévő adatmennyiség kombinációja. A meglévő adat hozzá lett rendelve a-1 értékhez a frissítési (WhenMatched) kód elérési útjában. A kódrészlet tetején létrehozott új, a beszúrási kód elérési útján (WhenNotMatched) hozzáadott adatmennyiség is hozzá lett adva.

### <a name="history"></a>Előzmények

A Delta Lake 's lehetővé teszi egy tábla előzményeinek megtekintését. Az alapul szolgáló különbözeti táblán végrehajtott módosítások. Az alábbi cella azt mutatja be, hogy milyen egyszerű az előzmények vizsgálata.

:::zone pivot = "programming-language-python"

```python
delta_table.history().show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show(false)
```

::: zone-end

Eredmények:

|version|          időbélyeg|userId|userName (Felhasználónév)|művelet|                                                operationParameters| feladat|jegyzetfüzet|clusterId|visszacsapás|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|---------|-------------------------------------------------------------------|----|--------|---------|-----------|--------------|-------------|
|      4|2020-04-25 00:36:27|  null|    null|    KÖRLEVÉL|                       [predikátum-> (oldData.`ID` = newData. `ID` )]|null|    null|     null|          3|          null|        hamis|
|      3|2020-04-25 00:36:08|  null|    null|   DELETE|[predikátum-> ["(( `ID` % cast (2 as BIGINT)) = cast (0 as BIGINT))"]]|null|    null|     null|          2|          null|        hamis|
|      2|2020-04-25 00:35:51|  null|    null|   UPDATE| [predikátum-> ((azonosító # 744L% cast (2 as bigint)) = cast (0 as bigint))]|null|    null|     null|          1|          null|        hamis|
|      1|2020-04-25 00:35:05|  null|    null|    ÍRNI|                             [Mode-> felülírás, partitionBy-> []]|null|    null|     null|          0|          null|        hamis|
|      0|2020-04-25 00:34:34|  null|    null|    ÍRNI|                         [Mode-> ErrorIfExists, partitionBy-> []]|null|    null|     null|       null|          null|         true|

Itt láthatja a fenti kódrészletekben végrehajtott összes módosítást.

## <a name="read-older-versions-of-data-using-time-travel"></a>Az idő régebbi verzióinak olvasása a Time Travel használatával

A Delta Lake-táblázat korábbi pillanatképeit a Time Travel nevű funkció használatával lehet lekérdezni. Ha a felülírt adatokhoz szeretne hozzáférni, lekérdezheti a táblázat pillanatképét, mielőtt felülírja az első adathalmazt a versionAsOf beállítással.

Miután futtatta az alábbi cellát, a felülírása előtt látnia kell az első adatkészletet. Az időutazás rendkívül hatékony funkció, amely kihasználja a Delta Lake Transaction log előnyeit, hogy hozzáférjen a már nem szereplő adatmennyiségekhez. Ha eltávolítja a 0. verziót (vagy az 1. verzió megadását), az újabb adatértékeket is láthatja. További információ: egy [tábla régebbi pillanatképének lekérdezése](https://docs.delta.io/latest/delta-batch.html#deltatimetravel).

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Option("versionAsOf", 0).Load(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").option("versionAsOf", 0).load(deltaTablePath)
df.show()
```

::: zone-end

Eredmények:

| ID (Azonosító)|
|---|
|  0|
|  1|
|  4|
|  3|
|  2|

Itt láthatja, hogy a legkorábbi verzióra ment vissza.

## <a name="write-a-stream-of-data-to-a-table"></a>Adatfolyam írása egy táblába

Egy Delta Lake-táblába is írhat a Spark strukturált streaming használatával. A Delta Lake Transaction log pontosan egyszer garantálja a feldolgozást, még akkor is, ha más streamek vagy batch-lekérdezések egyidejűleg futnak a táblán. Alapértelmezés szerint a streamek Hozzáfűzés módban futnak, amely új rekordokat vesz fel a táblába.

A Delta Lake strukturált Streamingtel való integrálásával kapcsolatos további információkért lásd: [táblázatos adatfolyamok olvasása és írása](https://docs.delta.io/latest/delta-streaming.html).

Az alábbi cellákban a következőkre van szükségünk:

* 30. cella – az újonnan hozzáfűzött adathalmazok megjelenítése
* 31. cella vizsgálatának előzményei
* 32 cella a strukturált folyamatos átviteli feladatok leállítása
* 33-es cella-ellenőrzési előzmények < – észreveheti, hogy a Hozzáfűzés leállt

Először létre kell hoznia egy egyszerű Spark streaming-feladatot, amely egy sorozatot hoz létre, és a feladatot a különbözeti táblába írja.

:::zone pivot = "programming-language-python"

```python
streaming_df = spark.readStream.format("rate").load()
stream = streaming_df\
    .selectExpr("value as id")\
    .writeStream\
    .format("delta")\
    .option("checkpointLocation", "/tmp/checkpoint-{0}".format(session_id))\
    .start(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var streamingDf = spark.ReadStream().Format("rate").Load();
var stream = streamingDf.SelectExpr("value as id").WriteStream().Format("delta").Option("checkpointLocation", $"/tmp/checkpoint-{sessionId}").Start(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val streamingDf = spark.readStream.format("rate").load()
val stream = streamingDf.select($"value" as "id").writeStream.format("delta").option("checkpointLocation", s"/tmp/checkpoint-$sessionId").start(deltaTablePath)
```

::: zone-end

## <a name="read-a-stream-of-changes-from-a-table"></a>Egy táblázat változásait tartalmazó adatfolyam olvasása

Míg a stream a Delta Lake-táblába kerül, az adott táblából is beolvashatja a streaming forrásaként. Elindíthat például egy másik adatfolyam-lekérdezést, amely a Delta Lake-táblán végrehajtott összes módosítást kinyomtatja.

:::zone pivot = "programming-language-python"

```python
delta_table.toDF().sort(col("id").desc()).show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.ToDF().Sort(Col("id").Desc()).Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.toDF.sort($"id".desc).show
```

::: zone-end

Eredmények:

| ID (Azonosító)|
|---|
| 19|
| 18|
| 17|
| 16|
| 15|
| 14|
| 13|
| 12|
| 11|
| 10|
|  8|
|  6|
|  4|
|  3|
|  2|
|  1|
|  0|
| -1|
| -1|
| -1|

:::zone pivot = "programming-language-python"

```python
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show
```

::: zone-end

Eredmények:

|version|          időbélyeg|       művelet|                                                                  operationParameters|visszacsapás|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|FOLYAMATOS ÁTVITELI FRISSÍTÉS|[outputMode-> Hozzáfűzés, queryId-> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epochId-> 0]|          4|
|      4|2020-04-25 00:36:27|           KÖRLEVÉL|                                         [predikátum-> (oldData.`id` = newData. `id` )]|          3|
|      3|2020-04-25 00:36:08|          DELETE|                  [predikátum-> ["(( `id` % cast (2 as BIGINT)) = cast (0 as BIGINT))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predikátum-> ((azonosító # 744L% cast (2 as bigint)) = cast (0 as bigint))]|          1|
|      1|2020-04-25 00:35:05|           ÍRNI|                                               [Mode-> felülírás, partitionBy-> []]|          0|
|      0|2020-04-25 00:34:34|           ÍRNI|                                           [Mode-> ErrorIfExists, partitionBy-> []]|       null|

Íme néhány kevésbé érdekes oszlop, amely leegyszerűsíti az előzmények nézet megtekintési élményét.

:::zone pivot = "programming-language-python"

```python
stream.stop()
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(100, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
stream.Stop();
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(100, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
stream.stop
deltaTable.history.show
```

Eredmények:

|version|          időbélyeg|       művelet|                                                                  operationParameters|visszacsapás|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|FOLYAMATOS ÁTVITELI FRISSÍTÉS|[outputMode-> Hozzáfűzés, queryId-> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epochId-> 0]|          4|
|      4|2020-04-25 00:36:27|           KÖRLEVÉL|                                         [predikátum-> (oldData.`id` = newData. `id` )]|          3|
|      3|2020-04-25 00:36:08|          DELETE|                  [predikátum-> ["(( `id` % cast (2 as BIGINT)) = cast (0 as BIGINT))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predikátum-> ((azonosító # 744L% cast (2 as bigint)) = cast (0 as bigint))]|          1|
|      1|2020-04-25 00:35:05|           ÍRNI|                                               [Mode-> felülírás, partitionBy-> []]|          0|
|      0|2020-04-25 00:34:34|           ÍRNI|                                           [Mode-> ErrorIfExists, partitionBy-> []]|       null|

::: zone-end

## <a name="convert-parquet-to-delta"></a>A parketta átalakítása különbözetre

Helyi konverziót is végezhet a parketta formátumáról a Delta értékre.

Itt fogja tesztelni, hogy a meglévő tábla Delta formátumú-e, vagy sem.
:::zone pivot = "programming-language-python"

```python
parquet_path = "/parquet/parquet-table-{0}".format(session_id)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetPath = $"/parquet/parquet-table-{sessionId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetPath = s"/parquet/parquet-table-$sessionId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Eredmények:

Hamis

Most konvertálja az adatváltozásokat a Delta formátumba, és ellenőrizze, hogy a munka megtörtént-e.

:::zone pivot = "programming-language-python"

```python
DeltaTable.convertToDelta(spark, "parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
DeltaTable.ConvertToDelta(spark, $"parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
DeltaTable.convertToDelta(spark, s"parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Eredmények:

Igaz

## <a name="sql-support"></a>SQL-támogatás

A Delta az SQLon keresztül támogatja a Table Utility parancsait. Az SQL-t használhatja a következőhöz:

* DeltaTable előzményeinek beolvasása
* DeltaTable vákuum
* Parketta-fájl konvertálása a Delta értékre

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE HISTORY delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"DESCRIBE HISTORY delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"DESCRIBE HISTORY delta.`$deltaTablePath`").show()
```

::: zone-end

Eredmények:

|version|          időbélyeg|userId|userName (Felhasználónév)|       művelet| operationParameters| feladat|jegyzetfüzet|clusterId|visszacsapás|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|----------------|--------------------|----|--------|---------|-----------|--------------|-------------|
|      5|2020-04-25 00:37:09|  null|    null|FOLYAMATOS ÁTVITELI FRISSÍTÉS|[outputMode-> AP...|null|    null|     null|          4|          null|         true|
|      4|2020-04-25 00:36:27|  null|    null|           KÖRLEVÉL|[predikátum – > (ol...|null|    null|     null|          3|          null|        hamis|
|      3|2020-04-25 00:36:08|  null|    null|          DELETE|[predikátum-> ["(...|null|    null|     null|          2|          null|        hamis|
|      2|2020-04-25 00:35:51|  null|    null|          UPDATE|[predikátum-> ((i...|null|    null|     null|          1|          null|        hamis|
|      1|2020-04-25 00:35:05|  null|    null|           ÍRNI|[Mode-> Overwrit...|null|    null|     null|          0|          null|        hamis|
|      0|2020-04-25 00:34:34|  null|    null|           ÍRNI|[Mode-> ErrorIfE...|null|    null|     null|       null|          null|         true|

:::zone pivot = "programming-language-python"

```python
spark.sql("VACUUM delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"VACUUM delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"VACUUM delta.`$deltaTablePath`").show()
```

::: zone-end

Eredmények:

|                path|
|--------------------|
|abfss://data@arca...|

Most meg kell győződnie arról, hogy a tábla nem különbözeti formátumú tábla, majd a Spark SQL használatával átalakítja a Delta formátumra, és megerősíti, hogy megfelelően lett konvertálva.

:::zone pivot = "programming-language-python"

```python
parquet_id = random.randint(0,1000)
parquet_path = "/parquet/parquet-table-{0}-{1}".format(session_id, parquet_path)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
spark.sql("CONVERT TO DELTA parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetId =  (new Random()).Next(10000000);
var parquetPath = $"/parquet/parquet-table-{sessionId}-{parquetId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath);
spark.Sql($"CONVERT TO DELTA parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetId = scala.util.Random.nextInt(1000)
val parquetPath = s"/parquet/parquet-table-$sessionId-$parquetId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
spark.sql(s"CONVERT TO DELTA parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Eredmények:

Igaz

A teljes dokumentációért lásd a [Delta Lake dokumentációs oldalát](https://docs.delta.io/latest/delta-intro.html) .

További információ: [Delta Lake Project](https://github.com/delta-io/delta).

## <a name="next-steps"></a>Következő lépések

* [.NET Apache Spark dokumentációhoz](/dotnet/spark)
* [Azure Synapse Analytics](../index.yml)