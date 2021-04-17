---
title: Egy-a-néhányhoz relációs adatok áttelepítése Azure Cosmos DB SQL API-ba
description: Megtudhatja, hogyan kezelhetők az összetett adatáttelepítések az SQL API-ba irányuló egy-a-néhányhoz kapcsolatokhoz
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 7dbb162749e0a2f84140b8e9394934198d096eac
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589618"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Egy-a-néhányhoz relációs adatok áttelepítése Azure Cosmos DB SQL API-fiókba
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ha egy relációs adatbázisból az SQL API-ba Azure Cosmos DB át, optimalizálás érdekében szükség lehet az adatmodell módosításaira.

Az egyik gyakori átalakítás az adatok denormalizálása kapcsolódó altemek egyetlen JSON-dokumentumba való beágyazása által. Az itt elérhető lehetőségek közül néhányat a Azure Data Factory vagy Azure Databricks. Az adatmodellezés általános útmutatását a Cosmos DB az adatmodellezést ismertető [Azure Cosmos DB.](modeling-data.md)  

## <a name="example-scenario"></a>Példaforgatókönyv

Tegyük fel, hogy az ALÁBBI két tábla található az SQL-adatbázisban: Orders és OrderDetails.


:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/orders.png" alt-text="Képernyőkép az SQL-adatbázis Orders és OrderDetails tábláiról." border="false" :::

Ezt az egy-a-néhányhoz kapcsolatot egy JSON-dokumentumban szeretnénk kombinálni a migrálás során. Ehhez létrehozhatunk egy T-SQL-lekérdezést a "FOR JSON" használatával az alábbiak szerint:

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

A lekérdezés eredményei az alábbihoz hasonlítanának: 

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png" alt-text="Megrendelés részletei" lightbox="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png":::

Ideális esetben egyetlen Azure Data Factory (ADF) másolási tevékenységet szeretne használni az Azure Cosmos DB SQL-adatok forrásként való lekérdezéséhez, és a kimenetet közvetlenül a fogadóba szeretné írni megfelelő JSON-objektumokként. Jelenleg nem lehetséges egyetlen másolási tevékenységben végrehajtani a szükséges JSON-átalakítást. Ha a fenti lekérdezés eredményeit egy Azure Cosmos DB SQL API-tárolóba próbáljuk másolni, az OrderDetails mező sztringtulajdonságként fog látni a dokumentumban a várt JSON-tömb helyett.

Ezt a jelenlegi korlátozást az alábbi módokon lehet kihozni:

* **Használja Azure Data Factory két másolási tevékenységhez:** 
  1. JSON-formátumú adatok lekérte az SQL-ről egy szövegfájlba egy köztes blobtárolóban, és 
  2. Töltse be az adatokat a JSON-szövegfájlból egy tárolóba a Azure Cosmos DB.

* **A Azure Databricks SQL-ről** való olvasásra és a Azure Cosmos DB írásra – itt két lehetőséget kínálunk fel.


Nézzük meg részletesebben ezeket a megközelítéseket:

## <a name="azure-data-factory"></a>Azure Data Factory

Bár az OrderDetails nem ágyazható be JSON-tömbként a Cosmos DB-dokumentum célhelyén, a probléma megoldásához két külön másolási tevékenységet is tudunk használni.

### <a name="copy-activity-1-sqljsontoblobtext"></a>Másolási tevékenység #1: SqlJsonToBlobText

A forrásadatokhoz egy SQL-lekérdezést használunk, hogy az eredményhalmazt egyetlen oszlopként kérjük le soronként egy JSON-objektummal (amely az Order objektumot képviseli) az SQL Server OPENJSON és a FOR JSON PATH képességek használatával:

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf1.png" alt-text="ADF másolása":::


Az SqlJsonToBlobText másolási tevékenység fogadója esetében a "Tagolt szöveg" lehetőséget választjuk, és a Azure Blob Storage egy meghatározott mappájába mutatjuk dinamikusan létrehozott egyedi fájlnévvel (például : " @concat (pipeline(). RunId,'.json').
Mivel a szövegfájl nem igazán "tagolt", és nem szeretnénk, hogy vesszők használatával külön oszlopokba legyen elemezve, és meg szeretné őrizni az idézőjeleket (), ezért az "Oszlophatároló" tabulátorra ("\t") van állítva – vagy egy másik karakterre, amely nem fordul elő az adatokban – és az "Idézőjel karakter" helyett "No quote character" (Nem idézőjel karakter).

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf2.png" alt-text="Képernyőkép az Oszlophatároló és az Idézőjel karakter beállításainak kiemeléséről.":::

### <a name="copy-activity-2-blobjsontocosmos"></a>Másolási tevékenység #2: BlobJsonToCosmos

Ezután módosítjuk az ADF-folyamatot úgy, hogy hozzáadjuk a második másolási tevékenységet, amely Azure Blob Storage az első tevékenység által létrehozott szövegfájlt. JSON-forrásként dolgozza fel, hogy beszúrja Cosmos DB fogadóba a szövegfájlban található JSON-soronként egy dokumentumként.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf3.png" alt-text="Képernyőkép a JSON-forrásfájlról és a Fájl elérési útja mezőről.":::

Ha szeretné, hozzáadhat egy "Törlés" tevékenységet is a folyamathoz, hogy minden futtatás előtt törölje az /Orders/ mappában maradó összes korábbi fájlt. Az ADF-folyamat most a következőre hasonlít:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf4.png" alt-text="Képernyőkép a Törlés tevékenységről.":::

A fenti folyamat aktiválása után egy fájlt látunk a közvetítőben létrehozott Azure Blob Storage helyen, amely soronként egy JSON-objektumot tartalmaz:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf5.png" alt-text="Képernyőkép a JSON-objektumokat tartalmazó létrehozott fájlról.":::

Azt is látjuk, hogy az Orders (Megrendelések) dokumentum megfelelően beágyazott OrderDetails van beszúrva a Cosmos DB gyűjteménybe:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf6.png" alt-text="Képernyőkép a rendelés részleteiről a Cosmos DB részeként":::


## <a name="azure-databricks"></a>Azure Databricks

A Spark in [Azure Databricks](https://azure.microsoft.com/services/databricks/) a SQL Database-forrásból a Azure Cosmos DB-célhelyre másolhatja az adatokat anélkül, hogy létre Azure Cosmos DB köztes szöveges/JSON-Azure Blob Storage. 

> [!NOTE]
> Az egyértelműség és az egyszerűség kedvéért az alábbi kódrészletek kifejezetten beágyazott adatbázis-jelszavakat tartalmaznak, de mindig használjon titkos Azure Databricks titkos kódokat.
>

Először létre kell hozni és csatolni kell a szükséges [SQL-összekötőt,](/connectors/sql/) [Azure Cosmos DB összekötőkódtárakat](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) a Azure Databricks fürthöz. Indítsa újra a fürtöt, és győződjön meg arról, hogy a kódtárak be vannak töltve.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks1.png" alt-text="A szükséges SQL-összekötők létrehozási és csatolási helyének, valamint a Azure Cosmos DB összekötőkódtárainak a fürthöz Azure Databricks képernyőképe.":::

Ezután két mintát is bemutatunk a Scala és a Python esetében. 

### <a name="scala"></a>Scala
Itt a "FOR JSON" kimenettel az SQL-lekérdezés eredményeit kérjük le egy DataFrame-be:

```scala
// Connect to Azure SQL /connectors/sql/
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks2.png" alt-text="Képernyőkép az SQL-lekérdezés kimenetét egy DataFrame-ről.":::

Ezután csatlakozunk a Cosmos DB adatbázishoz és gyűjteményhez:

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

Végül definiáljuk a sémát, és from_json a DataFrame-et a CosmosDB-gyűjteménybe való mentés előtt.

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks3.png" alt-text="Képernyőkép, amely kiemeli a megfelelő tömböt a Cosmos DB mentéséhez.":::


### <a name="python"></a>Python

Alternatív megoldásként előfordulhat, hogy JSON-átalakításokat kell végrehajtania a Sparkban (ha a forrásadatbázis nem támogatja a "FOR JSON" vagy hasonló műveletet), vagy párhuzamos műveleteket szeretne használni egy nagyon nagy adatkészlethez. Itt bemutatunk egy PySpark-mintát. Első lépésként konfigurálja a forrás- és céladatbázis-kapcsolatokat az első cellában:

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

Ezután lekérdezjük a forrásadatbázist (ebben az esetben SQL Server) a rendelési és a rendelési adatok rekordjait, és az eredményeket Spark Dataframe-ekbe jük. Létrehozunk egy listát is, amely tartalmazza az összes rendelési adatokat, valamint egy szálkészletet a párhuzamos műveletekhez:

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

Ezután hozzon létre egy függvényt az Orders (Rendelések) a cél SQL API-gyűjteménybe való írásához. Ez a függvény szűri az adott rendelésazonosító összes rendelési részletét, JSON-tömbbe konvertálja őket, és beszúrja a tömböt egy JSON-dokumentumba, amely az adott rendeléshez a cél SQL API-gyűjteménybe lesz beírva:

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

Végül a fenti függvényt egy leképező függvénnyel hívjuk meg a szálkészleten a párhuzamos végrehajtáshoz, a korábban létrehozott rendelési adatok listájának átadásával:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
Mindkét módszer végén megfelelően mentenünk kell az OrderDetails beágyazott OrderDetails adatokat a gyűjtemény minden order Cosmos DB belül:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks4.png" alt-text="Databricks":::

## <a name="next-steps"></a>Következő lépések
* További információ [az adatmodellezésről a Azure Cosmos DB](./modeling-data.md)
* [Megtudhatja, hogyan modellel és particionálhatóak az adatok a Azure Cosmos DB](./how-to-model-partition-example.md)
