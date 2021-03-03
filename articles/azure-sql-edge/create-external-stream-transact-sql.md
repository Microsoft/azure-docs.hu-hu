---
title: KÜLSŐ STREAM létrehozása (Transact-SQL) – Azure SQL Edge
description: További tudnivalók a külső STREAM-utasítás létrehozása az Azure SQL Edge-ben című témakörben
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 203abe2b6def478dc1747dd4ce638b5b62707612
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659222"
---
# <a name="create-external-stream-transact-sql"></a>KÜLSŐ STREAM létrehozása (Transact-SQL)

A külső STREAM objektumnak kettős célja van a bemeneti és a kimeneti adatfolyam is. Bemenetként használható az esemény-betöltési szolgáltatásokból (például az Azure Event hub, az Azure IoT Hub (vagy az Edge hub) vagy a Kafka-ből származó adatok lekérdezéséhez, illetve kimenetként is használható, amely meghatározza, hogy hol és hogyan kell tárolni az eredményeket egy adatfolyam-lekérdezésből.

Egy külső STREAM is megadható és létrehozható kimenetként és bemenetként olyan szolgáltatásokhoz, mint az Event hub vagy a blob Storage. Ez megkönnyíti az olyan láncolási forgatókönyveket, amelyekben az adatfolyam-lekérdezések kimenetként és egy másik adatfolyamként, a bemenetként megadott külső streamből való olvasáskor is megőrzik az eredményeket.

Az Azure SQL Edge jelenleg csak a következő adatforrásokat támogatja stream-bemenetként és-kimenetként.

| Adatforrás típusa | Input (Bemenet) | Kimenet | Leírás |
|------------------|-------|--------|------------------|
| Azure IoT Edge hub | Y | Y | Az adatforrást az adatfolyamok Azure IoT Edge központba való olvasására és írására. További információ: [IoT Edge hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub).|
| SQL Database | N | Y | Adatforrás-kapcsolódás az adatfolyam-adatSQL Databaseba való íráshoz. Az adatbázis lehet egy helyi adatbázis az Azure SQL Edge-ben vagy egy távoli adatbázis SQL Server vagy Azure SQL Database.|
| Kafka | Y | N | Adatforrást egy Kafka-témakörben lévő adatfolyam-adatok olvasásához. A Kafka-támogatás nem érhető el az Azure SQL Edge ARM64 verziójához.|



## <a name="syntax"></a>Szintaxis

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
( <column_definition> [, <column_definition> ] * ) -- Used for Inputs - optional 
WITH  ( <with_options> )

<column_definition> ::=
  column_name <column_data_type>

<data_type> ::=
[ type_schema_name . ] type_name
    [ ( precision [ , scale ] | max ) ]

<with_options> ::=
  DATA_SOURCE = data_source_name, 
  LOCATION = location_name, 
  [FILE_FORMAT = external_file_format_name], --Used for Inputs - optional 
  [<optional_input_options>],
  [<optional_output_options>], 
  TAGS = <tag_column_value>

<optional_input_options> ::= 
  INPUT_OPTIONS = '[<Input_options_data>]'

<Input_option_data> ::= 
      <input_option_values> [ , <input_option_values> ]

<input_option_values> ::=
  PARTITIONS: [number_of_partitions]
  | CONSUMER_GROUP: [ consumer_group_name ] 
  | TIME_POLICY: [ time_policy ] 
  | LATE_EVENT_TOLERANCE: [ late_event_tolerance_value ] 
  | OUT_OF_ORDER_EVENT_TOLERANCE: [ out_of_order_tolerance_value ]
  
<optional_output_options> ::= 
  OUTPUT_OPTIONS = '[<output_option_data>]'

<output_option_data> ::= 
      <output_option_values> [ , <output_option_values> ]

<output_option_values> ::=
   REJECT_POLICY: [ reject_policy ] 
   | MINIMUM_ROWS: [ row_value ] 
   | MAXIMUM_TIME: [ time_value_minutes] 
   | PARTITION_KEY_COLUMN: [ partition_key_column_name ] 
   | PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | SYSTEM_PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | PARTITION_KEY: [ partition_key_name ] 
   | ROW_KEY: [ row_key_name ] 
   | BATCH_SIZE: [ batch_size_value ] 
   | MAXIMUM_BATCH_COUNT: [ batch_value ] 
   | STAGING_AREA: [ blob_data_source ]
 
<tag_column_value> ::= -- Reserved for Future Usage
); 
```

## <a name="arguments"></a>Argumentumok

- [DATA_SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [FILE_FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **Hely**: az adatforrásban szereplő tényleges adatok vagy helyek neve. 
   - Az Edge hub vagy a Kafka stream objektumok esetében a Location (hely) megadja az Edge-központ vagy a Kafka-témakör nevét, amelyből olvasni vagy írni kíván.
   - Az SQL stream-objektumok (SQL Server, Azure SQL Database vagy az Azure SQL Edge) helye a tábla nevét adja meg. Ha az adatfolyam ugyanabban az adatbázisban és sémában jön létre, mint a céltábla, akkor csak a tábla neve elegendő. Ellenkező esetben teljes jogosultsággal kell rendelkeznie (<adatbázisnév. schema_name. table_name) a tábla nevével.
   - Az Azure Blob Storage Stream objektum helye a blob-tárolón belül használandó elérésiút-mintát jelenti. A szolgáltatással kapcsolatos további információkért lásd: (/articles/stream-Analytics/stream-Analytics-define-outputs.MD # blob-Storage-and-Azure-adat-Lake-Gen2)

- **INPUT_OPTIONS**: beállítások megadása kulcs-érték párokként olyan szolgáltatásokhoz, mint például a Kafka, a IoT Edge hub, amely az adatfolyam-lekérdezések bemenetei
    - PARTÍCIÓk: egy témakörhöz definiált partíciók száma. A használható partíciók maximális száma 32-re van korlátozva.
      - A Kafka bemeneti Streamekre vonatkozik
    - CONSUMER_GROUP: az Event és a IoT hubok az egyik fogyasztói csoporton belül korlátozzák az olvasók számát (5). Ha ezt a mezőt üresen hagyja, a "$Default" fogyasztói csoportot fogja használni.
      - Későbbi használatra fenntartva. Nem vonatkozik az Azure SQL Edge-re.  
    - TIME_POLICY: azt ismerteti, hogy el kell-e dobni az eseményeket, vagy módosítania kell az esemény időpontját, amikor a késői események, illetve az események nem felelnek meg
      - Későbbi használatra fenntartva. Nem vonatkozik az Azure SQL Edge-re.
    - LATE_EVENT_TOLERANCE: a maximális elfogadható késleltetési idő. A késleltetés az esemény időbélyegzője és a rendszeróra közötti különbséget jelöli.
      - Későbbi használatra fenntartva. Nem vonatkozik az Azure SQL Edge-re.
    - OUT_OF_ORDER_EVENT_TOLERANCE: az események addig nem érkeznek meg, amíg a bemenetről az adatfolyam-lekérdezésbe elvégezték az utat. Ezek az események elfogadhatók a-ben, vagy dönthet úgy is, hogy egy meghatározott időszakra szünetelteti az átrendezést.
      - Későbbi használatra fenntartva. Nem vonatkozik az Azure SQL Edge-re.
        
- **OUTPUT_OPTIONS**: adja meg a beállításokat kulcs-érték párokként a streaming lekérdezésekhez kimenetként támogatott szolgáltatások esetében 
  - REJECT_POLICY: DROP | Próbálkozzon újra a fajokkal az Adatátalakítási hibák esetén. 
    - Az összes támogatott kimenetre vonatkozik 
  - MINIMUM_ROWS:  
    Egy kimenetre írt kötegekben szükséges minimális sorok száma. A parketta esetében minden köteg létrehoz egy új fájlt. 
    - Az összes támogatott kimenetre vonatkozik 
  - MAXIMUM_TIME:  
    Maximális várakozási idő percben megadva a kötegekben. Ezt követően a köteg a kimenetbe kerül, még akkor is, ha a minimális sorokra vonatkozó követelmény nem teljesül. 
    - Az összes támogatott kimenetre vonatkozik 
  - PARTITION_KEY_COLUMN:  
    A partíciós kulcshoz használt oszlop. 
    - Későbbi használatra fenntartva. Nem vonatkozik az Azure SQL Edge-re.
  - PROPERTY_COLUMNS:  
    Az üzenetekhez egyéni tulajdonságokként csatolt kimeneti oszlopok neveinek vesszővel tagolt listája, ha van ilyen.  
    - Későbbi használatra fenntartva. Nem vonatkozik az Azure SQL Edge-re. 
  - SYSTEM_PROPERTY_COLUMNS:  
    A Rendszertulajdonságok és a kimeneti oszlopok neve/érték párokból álló, Service Bus üzenetekbe való feltöltésére szolgáló, JSON formátumú gyűjtemény. például {"MessageId": "Column1", "PartitionKey": "Oszlop2"} 
    - Későbbi használatra fenntartva. Nem vonatkozik az Azure SQL Edge-re. 
  - PARTITION_KEY:  
    A partíciós kulcsot tartalmazó kimeneti oszlop neve. A partíciós kulcs a partíció egyedi azonosítója egy adott táblán belül, amely az entitás elsődleges kulcsának első részét képezi. Ez egy olyan karakterlánc-érték, amely legfeljebb 1 KB méretű lehet. 
    - Későbbi használatra fenntartva. Nem vonatkozik az Azure SQL Edge-re.
  - ROW_KEY:  
    A sor kulcsot tartalmazó kimeneti oszlop neve. A sor kulcsa egy adott partíción belüli entitás egyedi azonosítója. Az entitás elsődleges kulcsának második részét képezi. A sor kulcsa egy olyan karakterlánc-érték, amely legfeljebb 1 KB méretű lehet. 
    - Későbbi használatra fenntartva. Nem vonatkozik az Azure SQL Edge-re.
  - BATCH_SIZE:  
    Ez a tábla tárterületének azon tranzakcióinak számát jelöli, amelyeknél a maximális érték 100 rekord lehet. A Azure Functions esetében ez a függvény hívási alapértelmezett értéke bájtban, a 256 kB értéket jelenti. 
    - Későbbi használatra fenntartva. Nem vonatkozik az Azure SQL Edge-re. 
  - MAXIMUM_BATCH_COUNT:  
    Az Azure Function függvénynek másodpercenként eljuttatott események maximális száma – az alapértelmezett érték a 100. SQL Database esetében ez az összes tömeges beszúrási tranzakcióval ellátott rekordok maximális számát jelöli – az alapértelmezett érték a 10 000. 
    - Az összes SQL-alapú kimenetre vonatkozik 
  - STAGING_AREA: külső ADATFORRÁS-objektum, amely a nagy átviteli sebességű adatfeldolgozást az Azure szinapszis Analytics szolgáltatásba Blob Storage az átmeneti területét. 
    - Későbbi használatra fenntartva. Nem vonatkozik az Azure SQL Edge-re.


## <a name="examples"></a>Példák

### <a name="example-1---edgehub"></a>1. példa – EdgeHub

Típus: bemenet vagy kimenet<br>

Szintaxis:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = JSON, 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   FILE_FORMAT = myFileFormat, 
   LOCATION = '<mytopicname>', 
   OUTPUT_OPTIONS =   
     'REJECT_TYPE: Drop'
);
```

### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>2. példa – Azure SQL Database, Azure SQL Edge, SQL Server

Típus: kimenet<br>

Szintaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>', 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = '<my_server_name>.database.windows.net', 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>', 
  CREDENTIAL = SQLCredName 
); 

CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = '<DatabaseName>.<SchemaName>.<TableName>' ,
   --Note: If table is contained in the database, <TableName> should be sufficient 
    OUTPUT_OPTIONS =  
      'REJECT_TYPE: Drop'
); 
```

### <a name="example-3---kafka"></a>3. példa – Kafka

Típus: bemenet<br>

Szintaxis:
```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = 'kafka://<kafkaserver>:<ipaddress>', 
  CREDENTIAL = kafkaCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = JSON, 
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
); 

CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = '<KafkaTopicName>', 
    FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      'PARTITIONS: 5'
); 
```

## <a name="see-also"></a>Lásd még

- [KÜLSŐ STREAM eldobása (Transact-SQL)](drop-external-stream-transact-sql.md)
