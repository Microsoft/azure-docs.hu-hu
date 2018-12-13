---
title: Tervezett karbantartás – Azure SQL Data Warehouse |} A Microsoft Docs
description: Ismerje meg, hogyan lehet felkészülni a tervezett karbantartási események az Azure SQL Data warehouse.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/19/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: c1e8f94a0131ace6354d070e932e414a1897260e
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53166302"
---
# <a name="planning-for-maintenance-on-your-azure-sql-data-warehouse"></a>Az Azure SQL data warehouse a karbantartási tervezése

Ismerje meg, hogyan készíti elő az Azure SQL data warehouse a tervezett karbantartási események.

## <a name="what-is-a-planned-maintenance-event"></a>Mi az a tervezett karbantartási esemény?
Egy tervezett karbantartási esemény a ablakban idő, amikor az adatraktár kell lennie a karbantartási műveletet offline állapotban. Ezek az események a lehetőségek a szolgáltatásfrissítés, új funkciók vagy javítások alkalmazására. 

## <a name="frequency"></a>Gyakoriság
Átlagosan legalább egy tervezett karbantartási esemény következik be, minden hónapban. 

## <a name="notifications-and-downtime"></a>Értesítések és állásidő
Minden tervezett karbantartási esemény előtt értesítést fog kapni. A karbantartási események futó lekérdezések és az adattárház offline állapotba lép. Minden data warehouse-hoz a várható állásidő körülbelül 30 percet. Ha befejeződött a karbantartás várható értesítést. 

## <a name="setting-up-alerts"></a>Riasztások beállítása

Azt javasoljuk, [Azure Monitor](../azure-monitor/platform/alerts-activity-log-service-notifications.md) tervezett karbantartási naplóriasztások beállítása. A riasztások segítségével tervezze meg a szükséges karbantartás az Ön számára a hatás minimalizálása érdekében. 

Értesítések beállításához használja, ezek [jelentkezik riasztás utasításokat](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 

## <a name="next-steps"></a>További lépések
Figyelésével kapcsolatos további információkért lásd: [a számítási feladat monitorozása](sql-data-warehouse-manage-monitor.md).
