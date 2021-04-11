---
title: Azure Advisor PostgreSQL-hez
description: Ismerje meg a PostgreSQL-hez Azure Advisor javaslatokat.
author: alau-ms
ms.author: alau
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 9d736fe77e41b80abaf76d13e918b7d51bde09c0
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251018"
---
# <a name="azure-advisor-for-postgresql"></a>Azure Advisor PostgreSQL-hez
Ismerje meg, hogyan alkalmazza a rendszer a Azure Advisort a Azure Database for PostgreSQLre, és választ kapjon a gyakori kérdésekre.
## <a name="what-is-azure-advisor-for-postgresql"></a>Mi a PostgreSQL-Azure Advisor?
A Azure Advisor rendszer telemetria használ a PostgreSQL-adatbázis teljesítmény-és megbízhatósági javaslatainak kibocsátására. Az Advisor-javaslatok a PostgreSQL-adatbázis ajánlatai között oszlanak meg:
* Azure Database for PostgreSQL – Önálló kiszolgáló
* Azure Database for PostgreSQL – rugalmas kiszolgáló
* Azure Database for PostgreSQL – rugalmas skálázás (Citus)

Bizonyos javaslatok több termék-ajánlatra is jellemzőek, míg más javaslatok termékspecifikus optimalizáción alapulnak.
## <a name="where-can-i-view-my-recommendations"></a>Hol tekinthetem meg a javaslataikat?
A javaslatok a Azure Portal **Áttekintés** navigációs oldalsávján érhetők el. Az előnézet szalagcím-értesítésként jelenik meg, a részleteket pedig az erőforrás-használati diagramok alatt található **értesítések** szakaszban tekintheti meg.

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Képernyőkép a Azure Portalról Azure Advisor javaslattal.":::

## <a name="recommendation-types"></a>Javaslatok típusai
Azure Database for PostgreSQL rangsorolja a következő típusú ajánlásokat:
* **Teljesítmény**: a PostgreSQL-kiszolgáló sebességének növelése érdekében. Ez magában foglalja a CPU-használatot, a memória terhelését, a kapcsolatok készletezését, a lemez kihasználtságát és a termékspecifikus kiszolgálói paramétereket. További információ: [Advisor Performance javaslatok](../advisor/advisor-performance-recommendations.md).
* **Megbízhatóság**: az üzleti szempontból kritikus fontosságú adatbázisok folytonosságának biztosítása és javítása. Ez magában foglalja a tárolási korlátokat, a kapcsolatok korlátait és a nagy kapacitású adatelosztási javaslatait. További információ: [Advisor megbízhatósági javaslatok](../advisor/advisor-high-availability-recommendations.md).
* **Költségek**: a teljes Azure-kiadások optimalizálása és csökkentése. Ez magában foglalja a kiszolgálók megfelelő méretezésére vonatkozó javaslatokat. További információ: [Advisor Cost javaslatok](../advisor/advisor-cost-recommendations.md).

## <a name="understanding-your-recommendations"></a>A javaslatok megismerése
* **Napi ütemterv**: az Azure PostgreSQL-adatbázisok esetében a kiszolgáló telemetria és a javaslatok napi rendszerességgel történő kiosztását tekintjük át. Ha módosítja a kiszolgáló konfigurációját, a meglévő javaslatok továbbra is láthatók maradnak, amíg újra meg nem vizsgáljuk a telemetria a következő napon. 
* A **teljesítmény előzményei**: néhány Javaslatunk a teljesítmény előzményein alapul. Ezek a javaslatok csak azt követően jelennek meg, hogy egy kiszolgáló 7 napig ugyanazzal a konfigurációval működik. Ez lehetővé teszi a nehéz használatú minták (például a magas CPU-tevékenységek vagy a magas kapcsolati kötetek) észlelését egy tartós időszakban. Ha új kiszolgálót hoz létre, vagy új virtuális mag-konfigurációra vált, a rendszer ideiglenesen szünetelteti az ajánlásokat. Ez megakadályozza, hogy az örökölt telemetria az újonnan újrakonfigurált kiszolgálókon indítsák el a javaslatokat. Ez azonban azt is jelenti, hogy a teljesítmény-előzmények alapján történő javaslatok nem azonosíthatók azonnal.

## <a name="next-steps"></a>Következő lépések
További információ: [Azure Advisor Overview (áttekintés](../advisor/advisor-overview.md)).
