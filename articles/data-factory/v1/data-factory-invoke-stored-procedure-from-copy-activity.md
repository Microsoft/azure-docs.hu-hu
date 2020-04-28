---
title: Tárolt eljárás meghívása Azure Data Factory másolási tevékenységből
description: Megtudhatja, hogyan hívhat meg egy tárolt eljárást Azure SQL Database vagy SQL Server egy Azure Data Factory másolási tevékenységből.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d05c2b03a0c498144f37c9b6205053120a596b09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74924096"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Tárolt eljárás meghívása másolási tevékenységből Azure Data Factory
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg az adatátalakítás a Data Factory tárolt eljárással tevékenységgel](../transform-data-using-stored-procedure.md)című témakört.


Az adatok [SQL Server](data-factory-sqlserver-connector.md) vagy [Azure SQL Databaseba](data-factory-azure-sql-connector.md)való másolása során beállíthatja, hogy a másolási tevékenység **SqlSink** egy tárolt eljárás meghívásához. A tárolt eljárással további feldolgozást (oszlopokat, értékeket keresve, több táblába történő beszúrást stb.) is el lehet végezni, mielőtt az adatok bekerülnek a céltábla táblájába. Ez a szolgáltatás a [tábla értékű paraméterek](https://msdn.microsoft.com/library/bb675163.aspx)előnyeit használja ki. 

Az alábbi példa bemutatja, hogyan hívhat meg egy tárolt eljárást egy SQL Server-adatbázisban egy Data Factory folyamatból (másolási tevékenység):  

## <a name="output-dataset-json"></a>Kimeneti adatkészlet JSON-je
A kimeneti adatkészlet JSON-fájljában állítsa be a **következőt** : **SqlServerTable**. Beállíthatja, hogy a **tulajdonsága azuresqltable** Azure SQL Database-adatbázissal használhassa. A **Táblanév** tulajdonság értékének meg kell egyeznie a tárolt eljárás első paraméterének nevével.  

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

## <a name="sqlsink-section-in-copy-activity-json"></a>SqlSink szakasz a másolási tevékenység JSON-fájljában
Adja meg az **SqlSink** szakaszt a másolási tevékenység JSON-fájljában az alábbiak szerint. Ha tárolt eljárást szeretne meghívni az adatok fogadó/céladatbázisbe való beszúrása közben, a **SqlWriterStoredProcedureName** és a **SqlWriterTableType** tulajdonság értékeit is meg kell adnia. A tulajdonságok leírását [a SQL Server-összekötő című cikk SqlSink szakasza](data-factory-sqlserver-connector.md#sqlsink)tartalmazza.

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

## <a name="stored-procedure-definition"></a>Tárolt eljárás definíciója 
Az adatbázisban adja meg a tárolt eljárást ugyanazzal a névvel, mint a **SqlWriterStoredProcedureName**. A tárolt eljárás kezeli a bemeneti adatokat a forrás adattárból, és beszúrja az adatokat a céladatbázis egyik táblájába. A tárolt eljárás első paraméterének meg kell egyeznie a JSON (marketing) adatkészletben definiált táblanév.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Tábla típusának definíciója
Az adatbázisában adja meg a tábla típusát a **SqlWriterTableType**megegyező névvel. A tábla típusának sémájának meg kell egyeznie a bemeneti adatkészlet sémájával.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>További lépések
Tekintse át a következő összekötő-cikkeket a teljes JSON-példákhoz: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
