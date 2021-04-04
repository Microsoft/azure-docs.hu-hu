---
title: T-SQL streaming-feladatok létrehozása az Azure SQL Edge-ben
description: Ismerkedjen meg Stream Analytics feladatok létrehozásával az Azure SQL Edge-ben.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 97189fd7a232c2467981b23dc20da51ebef08252
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656342"
---
# <a name="create-a-data-streaming-job-in-azure-sql-edge"></a>Adatátviteli feladatok létrehozása az Azure SQL Edge szolgáltatásban 

Ez a cikk azt ismerteti, hogyan lehet T-SQL streaming-feladatot létrehozni az Azure SQL Edge-ben. Létre kell hoznia a külső adatfolyam bemeneti és kimeneti objektumait, majd a folyamatos átviteli feladatok létrehozásának részeként meg kell határoznia a folyamatos átviteli feladatok lekérdezését.

## <a name="configure-the-external-stream-input-and-output-objects"></a>A külső adatfolyam bemeneti és kimeneti objektumainak konfigurálása

A T-SQL streaming a SQL Server külső adatforrásának funkcióját használja a külső adatfolyam-bemenetekhez és a folyamatos átviteli feladatok kimenetéhez társított adatforrások definiálásához. A következő T-SQL-parancsok használatával hozzon létre egy külső stream bemeneti vagy kimeneti objektumát:

- [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql)

- [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](/sql/t-sql/statements/create-external-data-source-transact-sql)

- [KÜLSŐ STREAM létrehozása (Transact-SQL)](#example-create-an-external-stream-object-to-azure-sql-database)

Emellett, ha az Azure SQL Edge, SQL Server vagy Azure SQL Database kimeneti adatfolyamként van használatban, szüksége lesz az [adatbázis-hatókörön belüli hitelesítő adatok létrehozása (Transact-SQL)](/sql/t-sql/statements/create-database-scoped-credential-transact-sql). Ez a T-SQL-parancs határozza meg az adatbázis eléréséhez szükséges hitelesítő adatokat.

### <a name="supported-input-and-output-stream-data-sources"></a>Támogatott bemeneti és kimeneti adatfolyam-adatforrások

Az Azure SQL Edge jelenleg csak a következő adatforrásokat támogatja stream-bemenetként és-kimenetként.

| Adatforrás típusa | Input (Bemenet) | Kimenet | Leírás |
|------------------|-------|--------|------------------|
| Azure IoT Edge hub | Y | Y | Az adatforrást az adatfolyamok Azure IoT Edge központba való olvasására és írására. További információ: [IoT Edge hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub).|
| SQL Database | N | Y | Adatforrás-kapcsolódás az adatfolyam-adatSQL Databaseba való íráshoz. Az adatbázis lehet egy helyi adatbázis az Azure SQL Edge-ben vagy egy távoli adatbázis SQL Server vagy Azure SQL Database.|
| Kafka | Y | N | Adatforrást egy Kafka-témakörben lévő adatfolyam-adatok olvasásához. Ez az adapter jelenleg csak az Azure SQL Edge Intel vagy AMD verzióihoz érhető el. Az Azure SQL Edge ARM64 verziója nem érhető el.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Példa: külső stream bemeneti/kimeneti objektum létrehozása Azure IoT Edge hubhoz

A következő példa egy külső stream-objektumot hoz létre Azure IoT Edge hub számára. Ha Azure IoT Edge hub külső stream bemeneti/kimeneti adatforrását szeretné létrehozni, először létre kell hoznia egy külső fájlformátumot az adatok elrendezéséhez.

1. Hozzon létre egy külső fájlformátum JSON típusú formátumot.

    ```sql
    Create External file format InputFileFormat
    WITH 
    (  
       format_type = JSON,
    )
    go
    ```

2. Hozzon létre egy külső adatforrást Azure IoT Edge hub számára. A következő T-SQL-szkript létrehoz egy adatforrás-kapcsolódást egy IoT Edge hubhoz, amely ugyanazon a Docker-gazdagépen fut, mint az Azure SQL Edge.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput 
    WITH 
    (
        LOCATION = 'edgehub://'
    )
    go
    ```

3. Hozza létre a külső stream objektumot Azure IoT Edge hub számára. A következő T-SQL-szkript létrehoz egy stream objektumot az IoT Edge hub számára. IoT Edge hub Stream objektum esetén a LOCATION paraméter a IoT Edge hub-témakör neve, vagy a csatorna olvasása vagy írása.

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors 
    WITH 
    (
        DATA_SOURCE = EdgeHubInput,
        FILE_FORMAT = InputFileFormat,
        LOCATION = N'TemperatureSensors',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    go
    ```

### <a name="example-create-an-external-stream-object-to-azure-sql-database"></a>Példa: külső Stream-objektum létrehozása Azure SQL Database

Az alábbi példa egy külső stream objektumot hoz létre a helyi adatbázishoz az Azure SQL Edge-ben. 

1. Hozzon létre egy főkulcsot az adatbázisban. Ez a hitelesítő adatok titkos kulcsának titkosításához szükséges.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Hozzon létre egy adatbázis-hatókörű hitelesítő adatot a SQL Server forrás eléréséhez. Az alábbi példa egy hitelesítő adatot hoz létre a külső adatforráshoz, az IDENTITY = username és a SECRET = Password értékkel.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Hozzon létre egy külső adatforrást külső ADATFORRÁS LÉTREHOZÁSával. A következő példa:

    * Létrehoz egy *LocalSQLOutput* nevű külső adatforrást.
    * A külső adatforrás (LOCATION = ' <vendor> :// <server> [: <port> ] ') azonosítása. A példában az Azure SQL Edge helyi példányára mutat.
    * A korábban létrehozott hitelesítő adatokat használja.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput 
    WITH 
    (
        LOCATION = 'sqlserver://tcp:.,1433',
        CREDENTIAL = SQLCredential
    )
    go
    ```

4. Hozza létre a külső stream objektumot. A következő példa egy külső stream objektumot hoz létre, amely egy tábla *dbo mutat. TemperatureMeasurements* az adatbázis *MySQLDatabase*.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements 
    WITH 
    (
        DATA_SOURCE = LocalSQLOutput,
        LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    ```

### <a name="example-create-an-external-stream-object-for-kafka"></a>Példa: külső Stream-objektum létrehozása a Kafka számára

Az alábbi példa egy külső stream objektumot hoz létre a helyi adatbázishoz az Azure SQL Edge-ben. Ez a példa feltételezi, hogy a Kafka-kiszolgáló névtelen hozzáférésre van konfigurálva. 

1. Hozzon létre egy külső adatforrást külső ADATFORRÁS LÉTREHOZÁSával. A következő példa:

    ```sql
    Create EXTERNAL DATA SOURCE [KafkaInput] 
    With
    (
        LOCATION = N'kafka://<kafka_bootstrap_server_name_ip>:<port_number>'
    )
    GO
    ```
2. Hozzon létre egy külső fájlformátumot a Kafka-bemenethez. A következő példa egy JSON-fájlformátumot hozott létre tömörített tömörítéssel. 

   ```sql
   CREATE EXTERNAL FILE FORMAT JsonGzipped  
    WITH 
    (  
        FORMAT_TYPE = JSON , 
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' 
    )
   ```

3. Hozza létre a külső stream objektumot. A következő példa egy külső stream objektumot hoz létre, amely a Kafka-témakörre mutat `*TemperatureMeasurement*` .

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurement 
    WITH 
    (  
        DATA_SOURCE = KafkaInput, 
        FILE_FORMAT = JsonGzipped,
        LOCATION = 'TemperatureMeasurement',
        INPUT_OPTIONS = 'PARTITIONS: 10' 
    ); 
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>A folyamatos átviteli feladatok és a folyamatos átviteli lekérdezések létrehozása

Az `sys.sp_create_streaming_job` adatfolyam-lekérdezések definiálásához és a folyamatos átviteli feladat létrehozásához használja a rendszer tárolt eljárását. A `sp_create_streaming_job` tárolt eljárás a következő paramétereket veszi figyelembe:

- `job_name`: A folyamatos átviteli feladatok neve. A folyamatos átviteli feladatok nevei egyediek a példányok között.
- `statement`: [Stream Analytics lekérdezési nyelvre](/stream-analytics-query/stream-analytics-query-language-reference)épülő streaming Query-utasítások.

Az alábbi példa egy egyszerű folyamatos átviteli feladatot hoz létre egy adatfolyam-lekérdezéssel. Ez a lekérdezés beolvassa az IoT Edge hub bemeneteit, és az adatbázisba írja az adatokat `dbo.TemperatureMeasurements` .

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

Az alábbi példa egy összetettebb folyamatos átviteli feladatot hoz létre több különböző lekérdezéssel. Ezek a lekérdezések tartalmazzák az egyiket, amely a beépített `AnomalyDetection_ChangePoint` függvényt használja a hőmérsékleti adatban észlelt rendellenességek azonosítására.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Folyamatos átviteli feladatok elindítása, leállítása, eldobása és figyelése

Ha egy folyamatos átviteli feladatot szeretne elindítani az Azure SQL Edge-ben, futtassa a `sys.sp_start_streaming_job` tárolt eljárást. A tárolt eljárás a folyamatos átviteli feladat nevét írja be bemenetként.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

A folyamatos átviteli feladatok leállításához futtassa a `sys.sp_stop_streaming_job` tárolt eljárást. A tárolt eljárás a leállításhoz szükséges folyamatos átviteli feladat nevét igényli bemenetként.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Adatfolyam-feladat eldobásához (vagy törléséhez) futtassa a `sys.sp_drop_streaming_job` tárolt eljárást. A tárolt eljárásnak a bemeneti adatátviteli feladat nevét kell eldobnia.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Egy folyamatos átviteli feladat aktuális állapotának lekéréséhez futtassa a `sys.sp_get_streaming_job` tárolt eljárást. A tárolt eljárásnak a bemeneti adatátviteli feladat nevét kell eldobnia. A kimenet a folyamatos átviteli feladatok nevét és aktuális állapotát adja meg.

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256),
       error nvarchar(256)
       )
)
```

A folyamatos átviteli feladatokhoz a következő állapotok tartozhatnak:

| Állapot | Leírás |
|--------| ------------|
| Létrehozva | A folyamatos átviteli feladatot létrehozták, de még nem indult el. |
| Indítás | A folyamatos átviteli feladatok a kezdeti fázisban vannak. |
| Tétlen | A folyamatos átviteli feladatok futtatása folyamatban van, de nincs feldolgozható bemenet. |
| Feldolgozás | A folyamatos átviteli feladatot futtatja, és feldolgozza a bemeneteket. Ez az állapot a folyamatos átviteli feladatokhoz tartozó kifogástalan állapotot jelzi. |
| Csökkentett teljesítményű | A folyamatos átviteli feladatok futnak, de a bemeneti feldolgozás során néhány nem végzetes hiba történt. A bemeneti feladatot a rendszer továbbra is futtatja, de a hibákkal kapcsolatos bemenetek eldobása is megtörténik. |
| Leállítva | A folyamatos átviteli feladatot leállították. |
| Sikertelen | A folyamatos átviteli feladatot nem sikerült végrehajtani. Ez általában egy végzetes hibát jelez a feldolgozás során. |

## <a name="next-steps"></a>Következő lépések

- [A folyamatos átviteli feladatokhoz tartozó metaadatok megtekintése az Azure SQL Edge-ben](streaming-catalog-views.md) 
- [Külső stream létrehozása](create-external-stream-transact-sql.md)