---
title: Azure Advisor For MariaDB-hez
description: Ismerje meg a Azure Advisor MariaDB-re vonatkozó javaslatokat.
author: alau-ms
ms.author: alau
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: 60a0a5763085de38dbfd4dabcb65dc24b299b29a
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368616"
---
# <a name="azure-advisor-for-mariadb"></a>Azure Advisor For MariaDB-hez
Megtudhatja, Azure Advisor hogyan lesz alkalmazva a Azure Database for MariaDB és kaphat választ a gyakori kérdésekre.
## <a name="what-is-azure-advisor-for-mariadb"></a>Mi az Azure Advisor a MariaDB-hez?
A Azure Advisor rendszer telemetriai adatokat használ a MariaDB-adatbázis teljesítményére és megbízhatóságára vonatkozó javaslatok kiadásához. 

Néhány javaslat több termékajánlatnál gyakori, míg más javaslatok termékspecifikus optimalizáláson alapulnak.
## <a name="where-can-i-view-my-recommendations"></a>Hol tudom megtekinteni a javaslataimat?
A javaslatok az  áttekintési oldalsávon érhetők el a Azure Portal. Az előnézet szalagcím-értesítésként jelenik meg, a  részletek pedig az erőforrás-használati grafikonok alatt található Értesítések szakaszban jelennek meg.

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Képernyőkép a Azure Portal egy Azure Advisor javaslatról.":::

## <a name="recommendation-types"></a>Javaslattípusok
Azure Database for MariaDB következő javaslattípusok rangsorolása:
* **Teljesítmény:** A MariaDB-kiszolgáló sebességének növelése. Ez magában foglalja a processzorhasználatot, a memóriaterhelést, a lemezkihasználtságot és a termékspecifikus kiszolgálóparamétereket. További információkért lásd az [Advisor teljesítményével kapcsolatos javaslatokat.](../advisor/advisor-performance-recommendations.md)
* **Megbízhatóság:** Az üzleti fontosságú adatbázisok folytonosságának biztosítása és javítása. Ez magában foglalja a tárhelykorlátra és a kapcsolati korlátra vonatkozó javaslatokat. További információkért lásd az [Advisor megbízhatósági javaslatait.](../advisor/advisor-high-availability-recommendations.md)
* **Költség:** A teljes Azure-költségek optimalizálása és csökkentése érdekében. Ide tartoznak a kiszolgáló megfelelő méretezésére vonatkozó javaslatok is. További információ: [Advisor Cost recommendations (Az Advisor költséggel kapcsolatos javaslatai).](../advisor/advisor-cost-recommendations.md)

## <a name="understanding-your-recommendations"></a>A javaslatok ismertetése
* **Napi ütemezés:** Az Azure MariaDB-adatbázisokhoz napi rendszerességgel ellenőrizzük a kiszolgálói telemetriát, és javaslatokat adunk ki. Ha módosítja a kiszolgáló konfigurációját, a meglévő javaslatok addig láthatóak maradnak, amíg a következő napon újra meg nem vizsgáljuk a telemetriát. 
* **Teljesítményelőzmények:** Néhány javaslatunk a teljesítményelőzményen alapul. Ezek a javaslatok csak akkor jelennek meg, ha egy kiszolgáló már 7 napja ugyanazokkal a konfigurációval működik. Ez lehetővé teszi, hogy észleljük a tartós időszakban a nagy terhelésű használati mintákat (például a magas processzorhasználatot vagy a nagy kapcsolati mennyiségeket). Ha új kiszolgálót létesít vagy új virtuálismag-konfigurációt vált, akkor ezek a javaslatok ideiglenesen szünetelnek. Ez megakadályozza, hogy az örökölt telemetria javaslatokat indítson egy újonnan újrakonfigurált kiszolgálón. Ez azonban azt is jelenti, hogy a teljesítményelőzmény-alapú javaslatok nem mindig azonosíthatók azonnal.

## <a name="next-steps"></a>Következő lépések
További információkért lásd a [Azure Advisor áttekintését.](../advisor/advisor-overview.md)
