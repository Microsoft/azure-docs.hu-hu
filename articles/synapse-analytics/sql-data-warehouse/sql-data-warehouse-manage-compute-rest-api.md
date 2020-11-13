---
title: Szüneteltetés, folytatás, méretezés REST API-kkal
description: A dedikált SQL-készlet (korábban SQL DW) számítási teljesítményének kezelése az Azure szinapszis Analytics REST API-kon keresztül.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/29/2019
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 49702051b5399d5079aacc97c00233a23ba8712d
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556012"
---
# <a name="rest-apis-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>REST API-k dedikált SQL-készlethez (korábban SQL DW) az Azure szinapszis Analyticsben

REST API-k a kiszámítások kezeléséhez a dedikált SQL-készlet (korábban SQL DW) számára az Azure szinapszis Analyticsben.

## <a name="scale-compute"></a>Számítások méretezése

Az adatraktár-egységek módosításához használja az [adatbázis létrehozása vagy frissítése](/rest/api/sql/databases/createorupdate?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) REST API. A következő példa beállítja az adatraktár-egységeket a DW1000 adatbázis-MySQLDW, amely a kiszolgáló MyServer található. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforráscsoport.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Számítás szüneteltetése

Egy adatbázis szüneteltetéséhez használja az [adatbázis szüneteltetése](/rest/api/sql/databases/pause?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) REST API. Az alábbi példa egy Database02 nevű adatbázist szüneteltet egy Kiszolgalo01 nevű kiszolgálón. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforráscsoport.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Számítás folytatása

Az adatbázis elindításához használja az [adatbázis folytatása](/rest/api/sql/databases/resume?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) REST API. A következő példa egy Database02 nevű adatbázist indít el egy Kiszolgalo01 nevű kiszolgálón. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforráscsoport.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Adatbázis állapotának keresése

> [!NOTE]
> A jelenleg ellenőrzött adatbázis állapota ONLINE állapotba kerülhet, miközben az adatbázis befejezi az online munkafolyamatot, és kapcsolódási hibákat eredményez. Ha ezt az API-hívást használja a kapcsolódási kísérletek elindításához, előfordulhat, hogy az alkalmazás kódjában egy 2-3 perces késleltetést kell hozzáadnia.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Karbantartási ütemterv beolvasása

A dedikált SQL-készlethez (korábban SQL DW) beállított karbantartási ütemtervet itt találja.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Karbantartási ütemterv beállítása

Karbantartási ütemterv beállítása és frissítése egy meglévő dedikált SQL-készleten (korábban SQL DW).

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": Saturday,
                                "startTime": 00:00,
                                "duration": 08:00,
                },
                {
                                "dayOfWeek": Wednesday
                                "startTime": 00:00,
                                "duration": 08:00,
                }
                ]
    }
}

```

## <a name="next-steps"></a>Következő lépések

További információ: a [számítások kezelése](sql-data-warehouse-manage-compute-overview.md).
