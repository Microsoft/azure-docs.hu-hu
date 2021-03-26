---
title: Adatcsatornák hozzáadása különböző forrásokból a metrikai TANÁCSADÓBA
titleSuffix: Azure Cognitive Services
description: különböző adatcsatornák hozzáadása a metrikai tanácsadóhoz
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: mbullwin
ms.openlocfilehash: 4fd01256d94fbcb18fe8437be00c84e49d98f7d0
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606147"
---
# <a name="add-data-feeds-from-different-data-sources-to-metrics-advisor"></a>Adatcsatornák hozzáadása különböző adatforrásokból a metrikai TANÁCSADÓBA

Ebből a cikkből megtudhatja, hogy milyen beállítások és követelmények vonatkoznak a különböző típusú adatforrások metrikai TANÁCSADÓBA való csatlakoztatására. Olvassa el az adatok előkészítését ismertető témakört [, amelyből](how-tos/onboard-your-data.md) megtudhatja, hogyan használhatók az adatok a metrikus tanácsadóval való használatával kapcsolatos főbb fogalmak. \

## <a name="supported-authentication-types"></a>Támogatott hitelesítési típusok

| Hitelesítési típusok | Description |
| ---------------------|-------------|
|**Basic** | Az adatforrásokhoz való hozzáféréshez alapszintű paramétereket kell biztosítania. Például egy kapcsolatok karakterlánca vagy kulcsa. Az adatcsatorna-rendszergazdák megtekinthetik ezeket a hitelesítő adatokat. |
| **AzureManagedIdentity** | Az Azure-erőforrások [felügyelt identitásai](../../active-directory/managed-identities-azure-resources/overview.md) a Azure Active Directory egyik funkciója. Azure-szolgáltatásokat biztosít automatikusan felügyelt identitással az Azure AD-ben. Az identitás használatával bármely olyan szolgáltatás hitelesíthető, amely támogatja az Azure AD-hitelesítést.|
| **AzureSQLConnectionString**| Tárolja a AzureSQL-alapú kapcsolódási karakterláncot **hitelesítő entitásként** a metrikai tanácsadóban, és használja közvetlenül a metrikák adatainak bevezetéséhez. Csak a hitelesítőadat-entitás rendszergazdái tudják megtekinteni ezeket a hitelesítő adatokat, de lehetővé teszik a jogosult megjelenítők számára, hogy adatcsatornákat hozzon létre anélkül, hogy ismerniük kellene a hitelesítő adatok részleteit. |
| **DataLakeGen2SharedKey**| A adat-Lake-fiók kulcsát **hitelesítő entitásként** tárolja a metrikai tanácsadóban, és közvetlenül a metrikák adatainak bevezetéséhez használja fel. Csak a hitelesítőadat-entitás rendszergazdái tudják megtekinteni ezeket a hitelesítő adatokat, de lehetővé teszik a jogosult megjelenítők számára az adatcsatorna létrehozását anélkül, hogy ismerniük kellene a hitelesítő adatok részleteit.|
| **Szolgáltatásnév**| Az egyszerű szolgáltatásnevet a metrikai tanácsadóban tárolhatja **hitelesítőadat-entitásként** , és a metrikák adatainak bevezetéséhez közvetlenül minden alkalommal használhatja azt. Csak a hitelesítő adatok rendszergazdái tudják megtekinteni a hitelesítő adatokat, de lehetővé teszik a jogosult megjelenítők számára az adatcsatorna létrehozását anélkül, hogy ismerniük kellene a hitelesítő adatokat.|
| **Egyszerű szolgáltatásnév a Key vaultból**|A szolgáltatásnevet a Key vaultban tárolja **hitelesítő egységként** a metrikai tanácsadóban, és közvetlenül a metrikák adatainak bevezetéséhez használja fel. Csak a hitelesítő adatokkal rendelkező **entitások** rendszergazdái tudják megtekinteni a hitelesítő adatokat, de a megjelenítők a részletes hitelesítő adatok ismerete nélkül is létrehozhatnak adatcsatornákat. |

## <a name="data-sources-supported-and-corresponding-authentication-types"></a>Támogatott adatforrások és az azokhoz tartozó hitelesítési típusok


| Adatforrások | Hitelesítési típusok |
|-------------| ---------------------|
|[**Azure-Application Insights**](#appinsights)|  Alapszintű |
|[**Azure Blob Storage (JSON)**](#blob) | Alapszintű<br>Felügyelt identitás|
|[**Azure Cosmos DB (SQL)**](#cosmosdb) | Alapszintű |
|[**Azure Data Explorer (Kusto)**](#kusto) | Alapszintű<br>Felügyelt identitás|
|[**2. generációs Azure Data Lake Storage**](#adl) | Alapszintű<br>DataLakeGen2SharedKey<br>Szolgáltatásnév<br>Egyszerű szolgáltatásnév a Key vaultból<br> |
|[**Azure SQL Database/SQL Server**](#sql) | Alapszintű<br>Felügyelt identitás<br>Szolgáltatásnév<br>Egyszerű szolgáltatásnév a Key vaultból<br>AzureSQLConnectionString
|[**Azure-Table Storage**](#table) | Alapszintű | 
|[**ElasticSearch**](#es) | Alapszintű |
|[**Http-kérelem**](#http) | Alapszintű | 
|[**InfluxDB (InfluxQL)**](#influxdb) | Alapszintű |
|[**MongoDB**](#mongodb) | Alapszintű |
|[**MySQL**](#mysql) | Alapszintű |
|[**PostgreSQL**](#pgsql)| Alapszintű|

Hozzon létre egy hitelesítőadat-entitást * *, és használja az adatforrásokhoz való hitelesítéshez. A következő részekben az *alapszintű* hitelesítéshez szükséges paramétereket kell megadni. 

## <a name="span-idappinsightsazure-application-insightsspan"></a><span id="appinsights">Azure-Application Insights</span>

* **Alkalmazás azonosítója**: ezt az alkalmazást a Application Insights API használatakor lehet azonosítani. Az alkalmazás AZONOSÍTÓjának lekéréséhez tegye a következőket:

    1. A Application Insights erőforrásban kattintson az API-hozzáférés lehetőségre.

    2. Másolja a mérőszámok Advisor **Application ID** mezőjébe generált alkalmazás-azonosítót. 
    
    További információért tekintse meg a [Azure bot Service dokumentációját](/azure/bot-service/bot-service-resources-app-insights-keys#application-id) .

* **API-kulcs**: az API-kulcsokat a böngészőn kívüli alkalmazások használják az erőforrás eléréséhez. Az API-kulcs beszerzéséhez tegye a következőket:

    1. Az Application Insights erőforrásban kattintson az API-hozzáférés lehetőségre.

    2. Kattintson az API-kulcs létrehozása lehetőségre.

    3. Írjon be egy rövid leírást, jelölje be a telemetria olvasása beállítást, majd kattintson a kulcs létrehozása gombra.

    4. Másolja az API-kulcsot a metrikák Advisor **API-kulcs** mezőjébe.

* **Lekérdezés**: az Azure Application Insights-naplók az Azure adatkezelő-ra épülnek, és a Azure monitor-lekérdezések ugyanazt a Kusto-lekérdezési nyelvet használják. A [Kusto lekérdezési nyelv dokumentációja](/azure/data-explorer/kusto/query/) tartalmazza a nyelv összes részletét, és az elsődleges erőforrásnak kell lennie ahhoz, hogy lekérdezést írjon a Application Insights. 


## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Azure Blob Storage (JSON)</span>

* **Kapcsolatok karakterlánca**: Tekintse meg az Azure Blob Storage a [kapcsolatok karakterláncát](../../storage/common/storage-configure-connection-string.md#configure-a-connection-string-for-an-azure-storage-account) ismertető cikket a karakterlánc beolvasásával kapcsolatos információkért.

* **Tároló**: a metrikai tanácsadó a blob-fájlként (egy blob/timestamp) tárolt idősoros adatokat vár egyetlen tárolóban. Ez a tároló neve mező.

* **Blob-sablon**: Ez a blob-fájlnevek sablonja. Példa: `/%Y/%m/X_%Y-%m-%d-%h-%M.json`. A következő paraméterek támogatottak:
  * `%Y` az év a következőképpen van formázva `yyyy`
  * `%m` a hónap a következőképpen van formázva `MM`
  * `%d` a nap a következőképpen van formázva `dd`
  * `%h` az óra a következőképpen van formázva `HH`
  * `%M` a perc a következőképpen van formázva `mm`

* **JSON-formátum verziója**: az adatsémát definiálja a JSON-fájlokban. Az aktuális metrikai tanácsadó két verziót támogat:
  
  * v1 (alapértelmezett érték)

      A rendszer csak a metrikák *nevét* és *értékét* fogadja el. Például:
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

  * v2

      A metrikák *dimenzióit* és az *időbélyeget* is elfogadjuk. Például:
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

JSON-fájlokban csak egy időbélyeg engedélyezett. 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **Kapcsolati karakterlánc**: a Azure Cosmos db eléréséhez használt kapcsolati karakterlánc. Ez a Cosmos DB erőforrásban található a **kulcsokban**. 
* **Adatbázis**: a lekérdezéshez használandó adatbázis. Ez a **Tallózás** lap **tárolók** területén található.
* **Gyűjtemény azonosítója**: a lekérdezéshez használandó Gyűjtemény azonosítója. Ez a **Tallózás** lap **tárolók** területén található.
* **SQL-lekérdezés**: SQL-lekérdezés, amely a többdimenziós idősorozatok adatsoraiba beolvassa és megfogalmazza az adatgyűjtést. Használhatja a és a `@StartTime` `@EndTime` változókat a lekérdezésben. A következőket kell formázni: `yyyy-MM-dd HH:mm:ss` .

    Példa lekérdezésre:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    Mintául szolgáló lekérdezés egy 2019/12/12-es adatszeletből:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure Data Explorer (Kusto)</span>

* **Kapcsolatok karakterlánca**: a metrikák tanácsadója támogatja az Azure adatkezelő (Kusto) Azure ad-alkalmazás-hitelesítéssel való elérését. Létre kell hoznia és regisztrálnia kell egy Azure AD-alkalmazást, majd engedélyeznie kell egy Azure Adatkezelő-adatbázis elérését. A kapcsolódási karakterlánc beszerzéséhez tekintse meg az [Azure adatkezelő](/azure/data-explorer/provision-azure-ad-app) dokumentációját.

* **Lekérdezés**: a [Kusto lekérdezési nyelve](/azure/data-explorer/kusto/query) a többdimenziós idősorozatok adatsoraiba való adatgyűjtéshez és-összeállításhoz. Használhatja a és a `@StartTime` `@EndTime` változókat a lekérdezésben. A következőket kell formázni: `yyyy-MM-dd HH:mm:ss` .

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **Fióknév**: a Azure Data Lake Storage Gen2 fiókjának neve. Ez az Azure Storage-fiók (Azure Data Lake Storage Gen2) erőforrásában található a **hozzáférési kulcsokban**.

* **Fiók kulcsa**: adja meg a fiók nevét a Azure Data Lake Storage Gen2 eléréséhez. Ez az Azure Storage-fiók (Azure Data Lake Storage Gen2) erőforrásában található a **hozzáférési kulcsok** beállításban.

* **Fájlrendszer neve (Container)**: a metrikai tanácsadó az idősorozat-adatokat blob-fájlként (egy blob/timestamp) tárolja, egyetlen tárolóban. Ez a tároló neve mező. Ez az Azure Storage-fiók (Azure Data Lake Storage Gen2) példányában található, és kattintson a "containers" elemre a "blob Service" szakaszban.

* **Directory-sablon**: Ez a blob-fájl címtár-sablonja. Például: */%Y/%m/%d*. A következő paraméterek támogatottak:
  * `%Y` az év a következőképpen van formázva `yyyy`
  * `%m` a hónap a következőképpen van formázva `MM`
  * `%d` a nap a következőképpen van formázva `dd`
  * `%h` az óra a következőképpen van formázva `HH`
  * `%M` a perc a következőképpen van formázva `mm`

* **Sablonfájl**: Ez a blob fájljának sablonja. Például: *X_% Y-% m-% d-% h-% M.json*. A következő paraméterek támogatottak:
  * `%Y` az év a következőképpen van formázva `yyyy`
  * `%m` a hónap a következőképpen van formázva `MM`
  * `%d` a nap a következőképpen van formázva `dd`
  * `%h` az óra a következőképpen van formázva `HH`
  * `%M` a perc a következőképpen van formázva `mm`

Az aktuális metrikai tanácsadó a következő módon támogatja a JSON-fájlokban lévő adatsémát. Például:

``` JSON
[
  {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
  {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
]
```
<!--
## <span id="eventhubs">Azure Event Hubs</span>

* **Connection String**: This can be found in 'Shared access policies' in your Event Hubs instance. Also for the 'EntityPath', it could be found by clicking into your Event Hubs instance and clicking at 'Event Hubs' in 'Entities' blade. Items that listed can be input as EntityPath. 

* **Consumer Group**: A [consumer group](../../event-hubs/event-hubs-features.md#consumer-groups) is a view (state, position, or offset) of an entire event hub.
Event Hubs use the latest offset of a consumer group to consume (subscribe from) the data from data source. Therefore a dedicated consumer group should be created for one data feed in your Metrics Advisor instance.

* **Timestamp**: Metrics Advisor uses the Event Hubs timestamp as the event timestamp if the user data source does not contain a timestamp field.
The timestamp field must match one of these two formats:

    * "YYYY-MM-DDTHH:MM:SSZ" format;

    * Number of seconds or milliseconds from the epoch of 1970-01-01T00:00:00Z.

    No matter which timestamp field it left aligns to granularity.For example, if timestamp is "2019-01-01T00:03:00Z", granularity is 5 minutes, then Metrics Advisor aligns the timestamp to "2019-01-01T00:00:00Z". If the event timestamp is "2019-01-01T00:10:00Z",  Metrics Advisor uses the timestamp directly without any alignment.
-->
## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL Database | SQL Server</span>

* **Kapcsolatok karakterlánca**: a metrikai tanácsadó elfogadja az SQL Server-adatforráshoz tartozó [ADO.net-stílusbeli kapcsolatok karakterláncát](/dotnet/framework/data/adonet/connection-string-syntax) .

    Példa a kapcsolatok karakterláncára:

    ```
    Data Source=db-server.database.windows.net:[port];initial catalog=[database];User ID=[username];Password=[password];Connection Timeout=10ms;
    ```

* **Lekérdezés**: SQL-lekérdezés, amely a többdimenziós idősorozatok adatsoraiba beolvassa és megfogalmazza az adatgyűjtést. A `@StartTime` lekérdezésben változót használhat a várt mérőszámok értékének beolvasásához.

  * `@StartTime`: a dátum és idő formátuma `yyyy-MM-dd HH:mm:ss`

    Példa lekérdezésre:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    A tényleges lekérdezés végrehajtása a 2019/12/12-es adatszeletnél:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idtableazure-table-storagespan"></a><span id="table">Azure-Table Storage</span>

* **Kapcsolati karakterlánc**: hozzon létre egy sas (közös hozzáférésű aláírás) URL-címet, és töltse ki itt. SAS URL-cím létrehozásának legegyszerűbb módja az Azure Portal használata. A Azure Portal használatával grafikusan is navigálhat. Ha a Azure Portal segítségével szeretne SAS URL-címet létrehozni, először keresse meg azt a Storage-fiókot, amelyet el szeretne érni a beállítások szakaszban, majd kattintson a megosztott elérési aláírás elemre. Jelölje be legalább a "Table" és az "Object" jelölőnégyzetet, majd kattintson a SAS létrehozása és a kapcsolatok sztringje gombra. Table service SAS URL-cím a metrikák Advisor munkaterületen a szövegmezőbe másolás és kitöltés.

* **Táblanév**: adjon meg egy táblát a lekérdezéshez. Ez az Azure Storage-fiók példányában található. A Table **szolgáltatás** szakaszban kattintson a **táblák** elemre.

* **Lekérdezés** Használhatja a t a `@StartTime` lekérdezésben. `@StartTime` lecserélve egy éééé-hh-NNTóó: PP: mm formátumú karakterláncot a szkriptben. Tipp: az Azure Storage Explorer használatával hozzon létre egy lekérdezést egy adott időtartományon belül, és győződjön meg arról, hogy az megfelelően fut, majd végezze el a cserét.

    ``` mssql
    date ge datetime'@StartTime' and date lt datetime'@EndTime'
    ```

## <a name="span-ideselasticsearchspan"></a><span id="es">Elasticsearch</span>

* **Gazdagép**: határozza meg a Elasticsearch-fürt fő gazdagépét.
* **Port**: határozza meg a Elasticsearch-fürt fő portját.
* **Engedélyezési fejléc**: határozza meg a Elasticsearch-fürt engedélyezési fejlécének értékét.
* **Lekérdezés**: a lekérdezés megadásával kérheti le az adatgyűjtést. @StartTimeA helyőrző támogatott. ( például a 2020-06-21T00:00:00Z betöltését, @StartTime = 2020-06-21T00:00:00)

## <a name="span-idhttphttp-requestspan"></a><span id="http">HTTP-kérelem</span>

* **Kérelem URL-** címe: egy olyan HTTP URL-cím, amely JSON-t adhat vissza. A (z)% Y,% m,% d,% h,% M helyőrzők támogatottak:% Y = év a (z) éééé,% m = hónap formátumban,% d = nap a (z) HH formátumban,% h = óra a (z) óó,% M = perc formátumban, mm-ben. Példa: `http://microsoft.com/ProjectA/%Y/%m/X_%Y-%m-%d-%h-%M`.
* **Http-metódus kérése**: a Get vagy a post használata.
* **Kérelem fejléce**: hozzáadhat egyszerű hitelesítést. 
* **Kérelem tartalma**: csak a JSON-adattartalom támogatott. A hasznos adatok helyőrzője @StartTime támogatott. A válasznak a következő JSON formátumúnak kell lennie: [{"Timestamp": "2018-01-01T00:00:00Z", "piac": "en-us", "Count": 11, "bevétel": 1.23}, {"Timestamp": "2018-01-01T00:00:00Z", "piac": "zh-CN", "Count": 22, "bevétel": 4,56}]. (például a 2020-06-21T00:00:00Z betöltését, @StartTime = 2020-06-21T00:00:00.0000000 + 00:00)

## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL)</span>

* **Kapcsolati karakterlánc**: a InfluxDB eléréséhez használt kapcsolati sztring.
* **Adatbázis**: a lekérdezéshez használandó adatbázis.
* **Lekérdezés**: egy lekérdezés, amely a többdimenziós idősoros adatsorokba gyűjti az adatgyűjtési és-kialakítási adatot.
* **Felhasználónév**: ez nem kötelező a hitelesítéshez. 
* **Password (jelszó**): ez nem kötelező a hitelesítéshez. 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **Kapcsolati karakterlánc**: a MongoDB eléréséhez használt kapcsolati sztring.
* **Adatbázis**: a lekérdezéshez használandó adatbázis.
* **Parancs**: egy parancs, amellyel beolvashatja és kialakíthatja az adatfeldolgozásra szolgáló többdimenziós idősorozat-adatsorokba.

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **Kapcsolati karakterlánc**: a MySQL-adatbázis eléréséhez használt kapcsolati sztring.
* **Lekérdezés**: egy lekérdezés, amely a többdimenziós idősoros adatsorokba gyűjti az adatgyűjtési és-kialakítási adatot.

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **Kapcsolati sztring**: a POSTGRESQL-adatbázis eléréséhez használt kapcsolati sztring.
* **Lekérdezés**: egy lekérdezés, amely a többdimenziós idősoros adatsorokba gyűjti az adatgyűjtési és-kialakítási adatot.

## <a name="next-steps"></a>Következő lépések

* A metrikus adatok rendszerbe való betöltésére való várakozás során olvassa el az [adatcsatorna-konfigurációk kezelésével](how-tos/manage-data-feeds.md)kapcsolatos témakört.
* A metrikai adatok betöltését követően beállíthatja a [metrikákat és a konfiguráció finomhangolását](how-tos/configure-metrics.md).
