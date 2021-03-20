---
title: Adatátalakítás a tárolt eljárási tevékenység használatával
description: A cikk azt ismerteti, hogyan használható SQL Server tárolt eljárás tevékenység egy Data Factory folyamat Azure SQL Database/adattárházában tárolt eljárás meghívásához.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 11/27/2018
ms.openlocfilehash: b9ba2f9de82522d4348fa341ad0b41d43c3eebcc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375646"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Az adatátalakítást az SQL Server tárolt eljárási tevékenység használatával Azure Data Factory
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-stored-proc-activity.md)
> * [Aktuális verzió](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az Adatátalakítási tevékenységek egy Data Factory folyamaton keresztül alakíthatók át és feldolgozhatók az előrejelzések [és az](concepts-pipelines-activities.md) elemzések során. A tárolt eljárási tevékenység az Data Factory által támogatott átalakítási tevékenységek egyike. Ez [a cikk az](transform-data.md) Adatátalakítási cikkre épül, amely általános áttekintést nyújt az adattranszformációról és a Data Factory támogatott átalakítási tevékenységeiről.

> [!NOTE]
> Ha még nem ismeri a Azure Data Factoryt, olvassa el a [Azure Data Factory bevezetését](introduction.md) , és végezze el az oktatóanyagot: [oktatóanyag: az adatátalakítás](tutorial-transform-data-spark-powershell.md) a cikk elolvasása előtt. 

A tárolt eljárási tevékenységgel egy tárolt eljárást hívhat meg a vállalat vagy egy Azure-beli virtuális gép (VM) alábbi adattárainak egyikében: 

- Azure SQL Database
- Azure Synapse Analytics
- SQL Server adatbázis.  Ha SQL Server használ, telepítse a saját üzemeltetésű integrációs modult ugyanarra a gépre, amely az adatbázist üzemelteti, vagy egy különálló gépen, amely hozzáfér az adatbázishoz. Self-Hosted Integration Runtime egy olyan összetevő, amely biztonságos és felügyelt módon csatlakoztatja a helyszíni/Azure-beli virtuális gépen található adatforrásokat a Cloud Services szolgáltatással. További részleteket a saját üzemeltetésű [integrációs](create-self-hosted-integration-runtime.md) modulról szóló cikkben talál.

> [!IMPORTANT]
> Az adatok Azure SQL Database vagy SQL Serverba való másolása során beállíthatja, hogy a **SqlSink** a másolási tevékenységben egy tárolt eljárást hívjanak a **sqlWriterStoredProcedureName** tulajdonság használatával. A tulajdonsággal kapcsolatos részletekért tekintse meg a következő összekötő-cikkeket: [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md). A tárolt eljárás meghívása az adatok Azure szinapszis-elemzésbe való másolása során a másolási tevékenység használata nem támogatott. Azonban a tárolt eljárási tevékenység használatával meghívhat egy tárolt eljárást az Azure szinapszis Analytics szolgáltatásban. 
>
> Azure SQL Database vagy SQL Server vagy az Azure szinapszis Analytics adatainak másolása esetén a másolási tevékenység **SqlSource** konfigurálhatja a tárolt eljárás meghívását a forrásadatbázis adatainak a **sqlReaderStoredProcedureName** tulajdonság használatával történő olvasásához. További információkért tekintse meg a következő összekötő cikkeit: [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md), [Azure szinapszis Analytics](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Szintaxis részletei
Itt látható a tárolt eljárási tevékenység definiálásának JSON-formátuma:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

A következő táblázat ismerteti ezeket a JSON-tulajdonságokat:

| Tulajdonság                  | Leírás                              | Kötelező |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | A tevékenység neve                     | Yes      |
| leírás               | A tevékenység által használt szöveg leírása | No       |
| típus                      | A tárolt eljárási tevékenységnél a tevékenység típusa **SqlServerStoredProcedure** . | Yes      |
| linkedServiceName         | Hivatkozás a **Azure SQL Databasera** vagy az **Azure szinapszis analyticsre** , vagy **SQL Server** társított szolgáltatásként van regisztrálva Data Factoryban. A társított szolgáltatással kapcsolatos további információkért lásd: [számítási társított szolgáltatások](compute-linked-services.md) cikk. | Yes      |
| storedProcedureName       | Adja meg a meghívott tárolt eljárás nevét. | Yes      |
| storedProcedureParameters | A tárolt eljárás paramétereinek értékeinek megadása. `"param1": { "value": "param1Value","type":"param1Type" }`A paraméter értékének és az adatforrás által támogatott típusnak a továbbítására használható. Ha egy paraméternél null értéket kell átadnia, használja a `"param1": { "value": null }` (minden kisbetű) lehetőséget. | No       |

## <a name="parameter-data-type-mapping"></a>Paraméter adattípusának leképezése
A paraméterhez megadott adattípus a Azure Data Factory típus, amely a használt adatforrásban lévő adattípushoz van leképezve. Az adattípushoz tartozó leképezések az adatforráshoz az összekötők területen találhatók. Néhány példa

| Adatforrás          | Adattípus-leképezés |
| ---------------------|-------------------|
| Azure Synapse Analytics | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Azure SQL Database   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |




## <a name="next-steps"></a>Következő lépések
A következő cikkekből megtudhatja, hogyan alakíthat át más módon az adatátalakítást: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Struktúra tevékenysége](transform-data-using-hadoop-hive.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-tevékenység](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Az Azure Machine Learning Studio (klasszikus) kötegelt végrehajtási tevékenysége](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
