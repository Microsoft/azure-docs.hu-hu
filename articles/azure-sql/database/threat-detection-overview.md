---
title: Advanced Threat Protection
titleSuffix: Azure SQL Database, SQL Managed Instance, & Azure Synapse Analytics
description: A komplex veszélyforrások elleni védelem rendellenes adatbázis-tevékenységeket észlel a Azure SQL Database, az Azure SQL felügyelt példányában és az Azure szinapszis Analyticsben rejlő lehetséges biztonsági fenyegetéseket jelezve.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, sstein
ms.date: 12/01/2020
tags: azure-synapse
ms.openlocfilehash: 931e914cd3c184136395a9bb9a7e148a90e9fb91
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96461942"
---
# <a name="advanced-threat-protection-for-azure-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Komplex veszélyforrások elleni védelem a Azure SQL Database, az SQL felügyelt példánya és az Azure szinapszis Analytics számára
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

A [Azure SQL Database](sql-database-paas-overview.md)komplex veszélyforrások elleni védelme, az [Azure SQL felügyelt példánya](../managed-instance/sql-managed-instance-paas-overview.md) és az [Azure szinapszis Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) rendellenes tevékenységeket észlel, amelyekben szokatlan és potenciálisan ártalmas kísérleteket jelez az adatbázisok eléréséhez vagy kiaknázásához.

A komplex veszélyforrások elleni védelem az [Azure Defender for SQL](azure-defender-for-sql.md) -ajánlat része, amely a fejlett SQL biztonsági funkciók egységes csomagja. A komplex veszélyforrások elleni védelem a központi Azure Defender for SQL portálon keresztül érhető el és kezelhető.

## <a name="overview"></a>Áttekintés

A komplex veszélyforrások elleni védelem új biztonsági réteget biztosít, amely lehetővé teszi az ügyfelek számára, hogy a rendellenes tevékenységekre vonatkozó biztonsági riasztások révén észlelje és válaszolják meg a potenciális fenyegetéseket. A felhasználók riasztást kapnak a gyanús adatbázis-tevékenységekről, a potenciális sebezhetőségekről és az SQL-injektálási támadásokról, valamint a rendellenes adatbázis-hozzáférési és lekérdezési mintákról. A komplex veszélyforrások elleni védelem a riasztásokat [Azure Security Centerokkal](https://azure.microsoft.com/services/security-center/)integrálja, amelyek tartalmazzák a gyanús tevékenységek részleteit, és javaslatokat tesznek a fenyegetés kivizsgálására és enyhítésére. A komplex veszélyforrások elleni védelem révén egyszerűen kezelheti az adatbázis potenciális veszélyforrásait anélkül, hogy biztonsági szakértőnek kellene lennie, vagy speciális biztonsági monitorozási rendszereket kellene kezelnie.

A teljes körű vizsgálathoz ajánlott engedélyezni a naplózást, amely az adatbázis-eseményeket az Azure Storage-fiókjában lévő naplóba írja.  A naplózás engedélyezéséhez tekintse meg a [Azure SQL Database és az Azure szinapszis](../../azure-sql/database/auditing-overview.md) vagy [Az Azure SQL felügyelt példányának](../managed-instance/auditing-configure.md)naplózása című témakört.

## <a name="alerts"></a>Riasztások

A Azure SQL Database komplex veszélyforrások elleni védelme olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához. A Azure SQL Database riasztások listáját a [Azure Security Center SQL Database és az Azure szinapszis Analytics riasztásai](../../security-center/alerts-reference.md#alerts-sql-db-and-warehouse)című témakörben találhatja meg.

## <a name="explore-detection-of-a-suspicious-event"></a>Gyanús esemény észlelésének megismerése

A rendellenes adatbázis-tevékenységek észlelése után e-mailben értesítést kap. Az e-mail információt nyújt a gyanús biztonsági eseményekről, beleértve a rendellenes tevékenységek jellegét, az adatbázis nevét, a kiszolgáló nevét, az alkalmazás nevét és az esemény időpontját. Emellett az e-mail-cím a lehetséges okokkal és a javasolt műveletekkel kapcsolatos információkat nyújt az adatbázis lehetséges fenyegetésének kivizsgálásához és enyhítéséhez.

![Rendellenes tevékenység jelentés](./media/threat-detection-overview/anomalous_activity_report.png)

1. Kattintson a **legutóbbi SQL-riasztások megtekintése** hivatkozásra az e-mailben a Azure Portal elindításához és a Azure Security Center riasztások oldal megjelenítéséhez, amely áttekintést nyújt az adatbázisban észlelt aktív fenyegetésekről.

   ![Tevékenységgel kapcsolatos fenyegetések](./media/threat-detection-overview/active_threats.png)

1. Egy adott riasztásra kattintva további részleteket és műveleteket kaphat a fenyegetés kivizsgálásához és a jövőbeli fenyegetések szervizelését.

   Az SQL injection például az egyik leggyakoribb webalkalmazási biztonsági probléma az interneten, amely az adatvezérelt alkalmazások támadására szolgál. A támadók kihasználhatják az alkalmazások biztonsági réseit, hogy rosszindulatú SQL-utasításokat szúrnak be az Application Entry mezőibe, vagy megsértsék vagy módosíthassák az adatokat az adatbázisban. Az SQL-injektálási riasztások esetében a riasztás részletei közé tartozik a kihasznált sebezhető SQL-utasítás.

   ![Adott riasztás](./media/threat-detection-overview/specific_alert.png)

## <a name="explore-alerts-in-the-azure-portal"></a>A riasztások megismerése a Azure Portal

A komplex veszélyforrások elleni védelem a riasztásokat az [Azure Security Center](https://azure.microsoft.com/services/security-center/)szolgáltatással integrálja. Az adatbázison belül élő SQL Advanced Threat Protection csempéket, SQL Azure Defender-lapokat pedig az Azure Portal nyomon követheti az aktív fenyegetések állapotát.

Kattintson a komplex **veszélyforrások elleni védelem riasztása** lehetőségre a Azure Security Center riasztások oldal elindításához, és tekintse át az adatbázisban észlelt aktív SQL-fenyegetések áttekintését.

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection-alerts.png" alt-text="komplex veszélyforrások elleni védelem riasztásai az adatbázisban – áttekintés":::

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection.png" alt-text="komplex veszélyforrások elleni védelem a Security Centerben":::

## <a name="next-steps"></a>Következő lépések

- További információ a komplex [veszélyforrások elleni védelemről Azure SQL Database & Azure szinapszisban](threat-detection-configure.md).
- További információ [az összetett veszélyforrások elleni védelemről az Azure SQL felügyelt példányain](../managed-instance/threat-detection-configure.md).
- További információ [Az Azure Defender for SQL szolgáltatásról](azure-defender-for-sql.md).
- További információ a [Azure SQL Database naplózásról](../../azure-sql/database/auditing-overview.md)
- További információ az [Azure Security Centerről](../../security-center/security-center-introduction.md)
- A díjszabással kapcsolatos további információkért tekintse meg a [Azure SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/) .