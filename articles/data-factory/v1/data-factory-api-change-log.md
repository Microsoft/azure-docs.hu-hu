---
title: Data Factory – .NET API-változási napló
description: A cikk a Azure Data Factory a .NET API adott verziójában ismerteti az adatváltozások, a szolgáltatások kiegészítései és a hibajavítások leírását.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: 3f7c80f5818c1353f438a164bf52c777056ad6d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377482"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory – .NET API-változási napló
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. 

Ez a cikk a Azure Data Factory SDK adott verzióban történt módosításaival kapcsolatos információkat tartalmazza. [Itt](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) találja a Azure Data Factory legújabb NuGet-csomagját

## <a name="version-4110"></a>4.11.0 verziója
Szolgáltatások kiegészítései:

* A következő társított szolgáltatások típusai lettek hozzáadva:
  * [OnPremisesMongoDbLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesmongodblinkedservice)
  * [AmazonRedshiftLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.amazonredshiftlinkedservice)
  * [AwsAccessKeyLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.awsaccesskeylinkedservice)
* A következő adatkészlet-típusok lettek hozzáadva:
  * [MongoDbCollectionDataset](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbcollectiondataset)
  * [AmazonS3Dataset](/dotnet/api/microsoft.azure.management.datafactories.models.amazons3dataset)
* A következő típusú másolási források lettek hozzáadva:
  * [MongoDbSource](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbsource)

## <a name="version-4100"></a>4.10.0 verziója
* A következő opcionális tulajdonságok lettek hozzáadva a Szövegformátum-hez:
  * [SkipLineCount](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [FirstRowAsHeader](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [TreatEmptyAsNull](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
* A következő társított szolgáltatások típusai lettek hozzáadva:
  * [OnPremisesCassandraLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice)
  * [SalesforceLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.salesforcelinkedservice)
* A következő adatkészlet-típusok lettek hozzáadva:
  * [OnPremisesCassandraTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset)
* A következő típusú másolási források lettek hozzáadva:
  * [CassandraSource](/dotnet/api/microsoft.azure.management.datafactories.models.cassandrasource)
* [WebServiceInputs](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity) tulajdonság hozzáadása a AzureMLBatchExecutionActivity
  * Több webszolgáltatás-bemenet átadásának engedélyezése egy Azure Machine Learning kísérletre

## <a name="version-491"></a>4.9.1 verziója
### <a name="bug-fix"></a>Hibajavítás
* A [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)WebApi-alapú hitelesítésének elavultnak kell lennie.

## <a name="version-490"></a>4.9.0 verziója
### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* Adja hozzá a [EnableStaging](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity) és a [StagingSettings](/dotnet/api/microsoft.azure.management.datafactories.models.stagingsettings) tulajdonságot a CopyActivity. A funkció részleteit a [szakaszos másolás](data-factory-copy-activity-performance.md#staged-copy) című részben tekintheti meg.

### <a name="bug-fix"></a>Hibajavítás
* A [ActivityWindowOperationExtensions. list](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions) metódus túlterhelésének bevezetése, amely egy [ActivityWindowsByActivityListParameters](/dotnet/api/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters) -példányt vesz igénybe.
* A [WriteBatchSize](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) és a [WriteBatchTimeout](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) megjelölése opcionálisként a CopySink-ben.

## <a name="version-480"></a>4.8.0 verziója
### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* A következő opcionális tulajdonságok lettek hozzáadva a másolási tevékenység típusához a másolási teljesítmény finomhangolásának engedélyezéséhez:
  * [ParallelCopies](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)
  * [CloudDataMovementUnits](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)

## <a name="version-470"></a>4.7.0 verziója
### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* Új StorageFormat típusú [OrcFormat](/dotnet/api/microsoft.azure.management.datafactories.models.orcformat) -típus hozzáadása a fájlok optimalizált, oszlopos (ORK) formátumban való másolásához.
* Adja hozzá a [AllowPolyBase](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) és a PolyBaseSettings tulajdonságot a SqlDWSink.
  * Lehetővé teszi, hogy a Base használatával az adatok az Azure szinapszis Analyticsbe másolhatók.

## <a name="version-461"></a>4.6.1-es verzió
### <a name="bug-fixes"></a>Hibajavítások
* Javítások HTTP-kérelem a listázási tevékenységhez Windows.
  * Eltávolítja az erőforráscsoport nevét és a adat-előállító nevét a kérelem hasznos adataiból.

## <a name="version-460"></a>4.6.0 verziója
### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* A következő tulajdonságok lettek hozzáadva a [PipelineProperties](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)-hez:
  * [PipelineMode](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [ExpirationTime](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [Adatkészletek](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
* A következő tulajdonságok lettek hozzáadva a [PipelineRuntimeInfo](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)-hez:
  * [PipelineState](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)
* Új [StorageFormat](/dotnet/api/microsoft.azure.management.datafactories.models.storageformat) típusú [JsonFormat](/dotnet/api/microsoft.azure.management.datafactories.models.jsonformat) hozzáadásával meghatározhatja azokat az adatkészleteket, amelyekben az adat JSON formátumú.

## <a name="version-450"></a>4.5.0 verziója
### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* [A List művelet hozzáadva a tevékenység ablakhoz](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions).
  * Metódusok hozzáadásával lekérheti a tevékenységek ablakait az entitások típusai (azaz az adat-előállítók, adatkészletek, folyamatok és tevékenységek) alapján.
* A következő társított szolgáltatások típusai lettek hozzáadva:
  * [ODataLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.odatalinkedservice), [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)
* A következő adatkészlet-típusok lettek hozzáadva:
  * [ODataResourceDataset](/dotnet/api/microsoft.azure.management.datafactories.models.odataresourcedataset), [WebTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.webtabledataset)
* A következő típusú másolási források lettek hozzáadva:     
  * [Webforrások](/dotnet/api/microsoft.azure.management.datafactories.models.websource)

## <a name="version-440"></a>4.4.0 verziója
### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* A következő társított szolgáltatástípus adatforrásként lett hozzáadva a másolási tevékenységekhez:
  * [AzureStorageSasLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice). Az [Azure Storage sas társított szolgáltatásával](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) kapcsolatos fogalmakat és példákat itt tekintheti meg.

## <a name="version-430"></a>4.3.0 verziója
### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* A másolási tevékenységek adatforrásaként a következő társított szolgáltatások lettek hozzáadva:
  * [HdfsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.hdfslinkedservice). Lásd: [adatok áthelyezése a HDFS a Data Factory használatával](data-factory-hdfs-connector.md) elméleti információk és példák.
  * [OnPremisesOdbcLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice). Tekintse meg az [adatok áthelyezése az ODBC-adattárakból Azure Data Factory használatával](data-factory-odbc-connector.md) fogalmi információkat és példákat.

## <a name="version-420"></a>4.2.0 verziója
### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* A következő új tevékenységtípus lett hozzáadva: [AzureMLUpdateResourceActivity](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity). A tevékenység részletes ismertetését lásd: [Az Azure ml-modellek frissítése az erőforrás frissítése tevékenység használatával](data-factory-azure-ml-batch-execution-activity.md).
* A [AzureMLLinkedService osztályhoz](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice)új, nem kötelező tulajdonság [updateResourceEndpoint](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice) lett hozzáadva.
* A [LongRunningOperationInitialTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) és a [LongRunningOperationRetryTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) tulajdonság hozzá lett adva a [datapipelinemanagementclient](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) osztályhoz.
* Az Data Factory szolgáltatásra irányuló ügyfélkapcsolatok időtúllépési beállításainak engedélyezése.

## <a name="version-410"></a>4.1.0 verziója
### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* A következő társított szolgáltatások típusai lettek hozzáadva:
  * [AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
  * [AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* A következő tevékenységtípusok lettek hozzáadva:
  * [DataLakeAnalyticsUSQLActivity](/dotnet/api/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity)
* A következő adatkészlet-típusok lettek hozzáadva:
  * [AzureDataLakeStoreDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoredataset)
* A másolási tevékenységhez a következő forrás-és fogadó típusok lettek hozzáadva:
  * [AzureDataLakeStoreSource](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresource)
  * [AzureDataLakeStoreSink](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresink)

## <a name="version-401"></a>4.0.1 verzió
### <a name="breaking-changes"></a>Kompatibilitástörő változások
A következő osztályok lettek átnevezve. Az új nevek az osztályok eredeti nevei voltak, mielőtt 4.0.0 a kiadást.

| Név a 4.0.0 | Név a 4.0.1-ban |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset) |
| AzureSqlDataset |[AzureSqlTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqltabledataset) |
| AzureDataset |[AzureTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuretabledataset) |
| OracleDataset |[OracleTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.oracletabledataset) |
| RelationalDataset |[RelationalTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.relationaltabledataset) |
| SqlServerDataset |[SqlServerTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.sqlservertabledataset) |

## <a name="version-400"></a>4.0.0-s verzió
### <a name="breaking-changes"></a>Kompatibilitástörő változások
* A következő osztályok/felületek lettek átnevezve.

| Régi név | Új név |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](/dotnet/api/microsoft.azure.management.datafactories.idatasetoperations) |
| Tábla |[Adathalmaz](/dotnet/api/microsoft.azure.management.datafactories.models.dataset) |
| TableProperties |[DatasetProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasetproperties) |
| TableTypeProprerties |[DatasetTypeProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasettypeproperties) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse) |
| TableGetResponse |[DatasetGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetgetresponse) |
| TableListResponse |[DatasetListResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetlistresponse) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters) |

* A **lista** metódusai mostantól a lapozható eredményeket adják vissza. Ha a válasz nem üres **NextLink** tulajdonságot tartalmaz, az ügyfélalkalmazásnak továbbra is le kell kérnie a következő lapot, amíg az összes oldalt vissza nem adja.  Alább bemutatunk egy példát:

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* A **List** folyamat API a teljes részletek helyett csak egy folyamat összegzését adja vissza. Például egy folyamat összefoglaló tevékenységei csak a nevet és a típust tartalmazzák.

### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* A [SqlDWSink](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) osztály két új tulajdonságot ( **SliceIdentifierColumnName** és **SqlWriterCleanupScript**) támogat az Azure Azure szinapszis Analytics idempotens-másolásának támogatásához. Ezen tulajdonságokkal kapcsolatos részletekért tekintse meg az [Azure szinapszis Analytics](data-factory-azure-sql-data-warehouse-connector.md) -cikket.
* A másolási tevékenység részeként mostantól támogatjuk a Azure SQL Database és az Azure szinapszis Analytics-források futtatására szolgáló tárolt eljárást. A [SqlSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqlsource) és a [SqlDWSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsource) osztály a következő tulajdonságokkal rendelkezik: **SqlReaderStoredProcedureName** és **StoredProcedureParameters**. Ezekről a tulajdonságokról a Azure.com [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) és az [Azure szinapszis Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) cikkeiben talál további információkat.