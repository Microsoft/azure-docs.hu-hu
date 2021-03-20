---
title: Hibatűrés hozzáadása Azure Data Factory másolási tevékenységben inkompatibilis sorok kihagyása
description: Megtudhatja, hogyan adhat hozzá hibatűrést Azure Data Factory másolási tevékenységhez, ha kihagyja a nem kompatibilis sorokat a másolás során
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 10e4bedae5b7c429152a3503fff2cb2769d66eb5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377176"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Hibatűrés hozzáadása a másolási tevékenységhez inkompatibilis sorok kihagyása

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-copy-activity-fault-tolerance.md)
> * [2-es verzió (aktuális verzió)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [Data Factory másolási tevékenységének hibatűrése](../copy-activity-fault-tolerance.md)című témakört.

Azure Data Factory [másolási tevékenység](data-factory-data-movement-activities.md) két módszert kínál a nem kompatibilis sorok kezelésére, amikor Adatmásolást végez a forrás-és fogadó adattárolók között:

- A másolási tevékenység megszakítható, ha nem kompatibilis adat észlelhető (alapértelmezett viselkedés).
- Az összes adattal folytathatja a hibatűrés hozzáadásával és a nem kompatibilis adatsorok kihagyásával. Emellett naplózhatja a nem kompatibilis sorokat az Azure Blob Storage-ban. Ezután megvizsgálhatja a naplót, hogy megismerje a hiba okát, javítsa ki az adatforráson lévő információkat, majd próbálja megismételni a másolási tevékenységet.

## <a name="supported-scenarios"></a>Támogatott esetek
A másolási tevékenység három olyan forgatókönyvet támogat, amelyek nem kompatibilis adatok észlelésére, kihagyására és naplózására alkalmasak:

- **Inkompatibilitás a forrás adattípusa és a fogadó natív típusa között.**

    Példa: adatok másolása CSV-fájlból a blob Storage-ból egy SQL Database-be egy olyan séma-definícióval, amely három **int** típusú oszlopot tartalmaz. A numerikus értékeket tartalmazó CSV-fájlok sorai, például a `123,456,789` másolásuk sikeresen megtörtént a fogadó tárolóba. Azonban a nem numerikus értékeket tartalmazó sorok, például a nem `123,456,abc` kompatibilisként észlelt és kimaradnak.

- **Eltérés az oszlopok számában a forrás és a fogadó között.**

    Például: adatok másolása egy CSV-fájlból a blob Storage-ban egy SQL Database-be olyan séma-definícióval, amely hat oszlopot tartalmaz. A hat oszlopot tartalmazó CSV-fájl sorainak másolása sikeresen megtörtént a fogadó tárolóba. A több vagy kevesebb, mint hat oszlopot tartalmazó CSV-fájl sorai nem kompatibilisként észlelhetők, és a rendszer kihagyja őket.

- **Elsődleges kulcs megsértése az SQL Serverre/Azure SQL Database-be/Azure Cosmos DB-be történő íráskor**

    Például: adatok másolása egy SQL-kiszolgálóról egy SQL-adatbázisba. Elsődleges kulcs van definiálva a fogadó SQL-adatbázisban, de nincs ilyen elsődleges kulcs definiálva a forrás SQL-kiszolgálón. A forrásban található duplikált sorok nem másolhatók a fogadóba. A másolási tevékenység csak a forrásadatok első sorát másolja a fogadóba. A duplikált elsődleges kulcs értékét tartalmazó következő forrásfájlokat inkompatibilisként észleli a rendszer, és kihagyja.

>[!NOTE]
>Ez a funkció nem érvényes, ha a másolási tevékenység úgy van konfigurálva, hogy külső betöltési mechanizmust indítson el, beleértve az [Azure szinapszis Analytics-alapú](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) , illetve az [Amazon vöröseltolódás](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift) Az adatoknak az Azure szinapszis Analyticsbe való betöltéséhez használja a "[polyBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" kifejezést a másolási tevékenységben a natív hibatűrés támogatásával.

## <a name="configuration"></a>Konfiguráció
A következő példa egy JSON-definíciót biztosít a nem kompatibilis sorok másolási tevékenységbe való kihagyásának konfigurálásához:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | A nem kompatibilis sorok kihagyásának engedélyezése a másolás során vagy nem. | Igaz<br/>False (alapértelmezett) | No |
| **redirectIncompatibleRowSettings** | A nem kompatibilis sorok naplózásához megadható tulajdonságok csoportja. | &nbsp; | No |
| **linkedServiceName** | Az Azure Storage társított szolgáltatása, amely a kihagyott sorokat tartalmazó naplót tárolja. | Egy [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) vagy [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) társított szolgáltatás neve, amely arra a tárolási példányra hivatkozik, amelyet a naplófájl tárolására kíván használni. | No |
| **elérési útja** | A kihagyott sorokat tartalmazó naplófájl elérési útja. | Itt adhatja meg azt a blob Storage-elérési útvonalat, amelyet a nem kompatibilis adatértékek naplózásához használni kíván. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót. | No |

## <a name="monitoring"></a>Figyelés
A másolási tevékenység futtatása után a figyelés szakaszban látható a kihagyott sorok száma:

![A nem kompatibilis sorok figyelése](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Ha úgy konfigurálja, hogy naplózza a nem kompatibilis sorokat, a naplófájl ezen az elérési úton található: a `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` naplófájlban láthatja a kihagyott sorokat, valamint a kompatibilitási kiváltó okot.

Az eredeti és a hozzá tartozó hiba is be van jelentkezve a fájlba. A naplófájl tartalmának példája a következő:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni Azure Data Factory másolási tevékenységről, olvassa el az [adatáthelyezés a másolási tevékenységgel](data-factory-data-movement-activities.md)című témakört.
