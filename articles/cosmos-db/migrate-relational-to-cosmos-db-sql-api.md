---
title: Egy-a-többhöz kapcsolódó adat migrálása Azure Cosmos DB SQL API-ba
description: Ismerje meg, hogyan kezelheti az összetett adatáttelepítést egy-a-néhány kapcsolathoz az SQL API-ban
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 53a3317f38cc22ffa3745f5f0e58cc01a54b825c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93096750"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Egy-a-többhöz kapcsolódó adat migrálása Azure Cosmos DB SQL API-fiókba
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ha Azure Cosmos DB SQL API-ra szeretne áttérni egy kapcsolódó adatbázisból, az optimalizáláshoz szükség lehet az adatmodell módosítására.

Az egyik gyakori átalakítás az adatok denormalizálása egy JSON-dokumentumba tartozó kapcsolódó alelemek beágyazásával. Íme néhány lehetőség a Azure Data Factory vagy Azure Databricks használatával. A Cosmos DB adatmodellezésével kapcsolatos általános útmutatásért tekintse át a [Azure Cosmos db adatmodellezését](modeling-data.md)ismertető témakört.  

## <a name="example-scenario"></a>Példaforgatókönyv

Tegyük fel, hogy a következő két táblázat szerepel az SQL Database-ben, a megrendelések és a OrderDetails.


:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/orders.png" alt-text="Képernyőkép, amely megjeleníti az SQL Database Orders és OrderDetails tábláit." border="false" :::

Ezt az egy-a-többhöz kapcsolatot szeretnénk egyesíteni egy JSON-dokumentumba az áttelepítés során. Ehhez hozzon létre egy T-SQL-lekérdezést a "FOR JSON" használatával az alábbiak szerint:

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

A lekérdezés eredményei az alábbiak szerint jelennek meg: 

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png" alt-text="Rendelés részletei" lightbox="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png":::

Ideális esetben egyetlen Azure Data Factory (ADF) másolási tevékenységgel szeretné lekérdezni az SQL-adatokat forrásként, és közvetlenül a megfelelő JSON-objektumokként írja be a kimenetet Azure Cosmos DB fogadóba. Jelenleg nem lehet végrehajtani a szükséges JSON-transzformációt egy másolási tevékenységben. Ha a fenti lekérdezés eredményét egy Azure Cosmos DB SQL API-tárolóba próbálja átmásolni, a várt JSON-tömb helyett a OrderDetails mezőt fogjuk látni a dokumentum sztring tulajdonságában.

A jelenlegi korlátozást a következő módokon lehet megkerülni:

* **Azure Data Factory használata két másolási tevékenységgel**: 
  1. JSON formátumú adatok beolvasása az SQL-ből egy szövegfájlba egy közbenső blob Storage-helyen, valamint 
  2. Adatok betöltése a JSON-szövegfájlból egy Azure Cosmos DB tárolóba.

* Az **SQL-ből való olvasáshoz Azure Databricks és a Azure Cosmos DBba való íráshoz használja a következő** két lehetőséget.


Tekintsük át ezeket a módszereket részletesebben:

## <a name="azure-data-factory"></a>Azure Data Factory

Bár a OrderDetails nem ágyazható be JSON-tömbként a cél Cosmos DB dokumentumba, a probléma megoldásához két különálló másolási tevékenységet használhat.

### <a name="copy-activity-1-sqljsontoblobtext"></a>Másolási tevékenység #1: SqlJsonToBlobText

A forrásadatok esetében egy SQL-lekérdezést használunk az eredményhalmaz egyetlen oszlopként való beszerzésére egy JSON-objektummal (amely a sorrendet jelöli) a SQL Server OPENJSON UTASÍTÁSSAL és a JSON PATH képességekkel:

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

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf1.png" alt-text="ADF-másolat":::


A SqlJsonToBlobText másolási tevékenység fogadója esetében a "tagolt szöveg" lehetőséget választjuk, és az Azure Blob Storage egy adott mappájára mutatunk, amely egy dinamikusan generált egyedi fájlnevet (például " @concat (folyamat ()). RunId, ". JSON").
Mivel a szövegfájl nem igazán "tagolt", és nem akarjuk, hogy a vesszővel elválasztott oszlopok alapján elemezzük őket, és meg szeretné őrizni a kettős idézőjeleket ("), az oszlop elválasztóját" egy lapra ("\t"), vagy egy másik, az "idézőjel" karakterrel nem rendelkező karakterre.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf2.png" alt-text="Képernyőkép, amely kiemeli az oszlop elválasztóját és az idézőjelek karakterének beállításait.":::

### <a name="copy-activity-2-blobjsontocosmos"></a>Másolási tevékenység #2: BlobJsonToCosmos

Ezután módosítjuk az ADF-folyamatot úgy, hogy hozzáadja az Azure Blob Storageban megjelenő második másolási tevékenységet az első tevékenység által létrehozott szövegfájlhoz. Ezt "JSON"-ként dolgozza fel, hogy beillessze Cosmos DB fogadóba a szövegfájlban található JSON-sor egy dokumentumként.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf3.png" alt-text="Képernyőkép a JSON-forrásfájl és a fájl elérési útja mező kiemeléséhez.":::

Szükség esetén a "Törlés" tevékenységet is hozzáadhatja a folyamathoz, így az egyes futtatások előtt törli a/Orders/mappában maradó összes korábbi fájlt. Az ADF-folyamat most a következőhöz hasonlóan néz ki:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf4.png" alt-text="Képernyőkép, amely kiemeli a törlési tevékenységet.":::

Miután elindította a folyamatot a fenti folyamaton, megjelenik egy, a köztes Azure Blob Storageban létrehozott fájl, amely egy JSON-objektumot tartalmaz soronként:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf5.png" alt-text="Képernyőkép, amely megjeleníti a JSON-objektumokat tartalmazó létrehozott fájlt.":::

Azt is látjuk, hogy az Orders dokumentumok megfelelően beágyazott OrderDetails vannak beillesztve a Cosmos DB gyűjteménybe:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf6.png" alt-text="Képernyőkép, amely az Cosmos DB dokumentum részeként jeleníti meg a rendelés részleteit":::


## <a name="azure-databricks"></a>Azure Databricks

A Spark in [Azure Databricksban](https://azure.microsoft.com/services/databricks/) is használhatjuk az adatok SQL Database forrásról a Azure Cosmos db célhelyre való másolását anélkül, hogy az Azure Blob Storage létrehozza a közbenső szöveg/JSON-fájlokat. 

> [!NOTE]
> Az érthetőség és az egyszerűség érdekében az alábbi kódrészletek explicit módon beágyazottként tartalmazzák a dummy Database-jelszavakat, de mindig Azure Databricks titkot kell használniuk.
>

Először létrehozjuk és csatoljuk a szükséges [SQL-összekötőt](https://docs.databricks.com/data/data-sources/sql-databases-azure.html) és [Azure Cosmos db összekötő](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) kódtárakat a Azure Databricks-fürthöz. Indítsa újra a fürtöt, és győződjön meg arról, hogy a könyvtárak betöltődik.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks1.png" alt-text="Képernyőfelvétel: a szükséges SQL-összekötő és Azure Cosmos DB összekötő-kódtárak létrehozásának és csatolásának helye Azure Databricks-fürthöz.":::

Ezután két mintát mutatjuk be a Scala és a Python számára. 

### <a name="scala"></a>Scala
Itt megkapjuk az SQL-lekérdezés eredményét a "FOR JSON" kimenettel egy DataFrame:

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
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

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks2.png" alt-text="A DataFrame SQL-lekérdezési kimenetét bemutató képernyőkép.":::

Ezután kapcsolódunk Cosmos DB adatbázishoz és gyűjteményhez:

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

Végezetül definiáljuk a sémát, és a from_json használatával alkalmazzuk a DataFrame a CosmosDB-gyűjteménybe való mentés előtt.

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

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks3.png" alt-text="Képernyőkép, amely kiemeli a megfelelő tömböt a Cosmos DB gyűjteménybe való mentéshez.":::


### <a name="python"></a>Python

Alternatív megoldásként előfordulhat, hogy végre kell hajtania JSON-átalakításokat a Sparkban (ha a forrásadatbázis nem támogatja a "JSON-t" vagy hasonló műveletet), vagy ha egy nagyon nagy adatkészlethez szeretne párhuzamos műveleteket használni. Itt egy PySpark mintát mutatunk be. Először konfigurálja a forrás-és a célként megadott adatbázis-kapcsolatokat az első cellában:

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

Ezután lekérdezjük a forrás-adatbázist (ebben az esetben SQL Server) a megrendelés és a rendelés részletes rekordjaihoz, és az eredményeket a Spark Dataframes. Létre fogunk hozni egy listát is, amely tartalmazza az összes rendelési azonosítót és egy szál-készletet a párhuzamos műveletekhez:

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

Ezt követően hozzon létre egy függvényt a megrendelések megírásához a cél SQL API-gyűjteménybe. Ez a függvény a megadott rendelési azonosító összes megrendelési részletét szűri, átalakítja őket egy JSON-tömbbe, majd beszúrja a tömböt egy JSON-dokumentumba, amelyet a cél SQL API-gyűjteménybe írunk az adott sorrendben:

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

Végül meghívjuk a fentieket egy Térkép függvény használatával a szál-készleten, hogy párhuzamosan lehessen végrehajtani a műveletet a korábban létrehozott Order ID-azonosítók listájában:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
A végén mindkét megközelítés esetében megfelelően mentett beágyazott OrderDetails kell beolvasni a Cosmos DB gyűjtemény minden rendelési dokumentumán belül:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks4.png" alt-text="Databricks":::

## <a name="next-steps"></a>Következő lépések
* Tudnivalók az [adatmodellezésről Azure Cosmos db](./modeling-data.md)
* Megtudhatja [, hogyan modellezheti és particionálhatja az Azure Cosmos db](./how-to-model-partition-example.md)