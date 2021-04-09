---
title: Előzetes értesítések (előzetes verzió) tervezett karbantartási eseményekhez
description: Értesítés kérése a Azure SQL Database tervezett karbantartása előtt.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/02/2021
ms.openlocfilehash: 895b9081ba7eb6d7e8b5d3304d37168e4064ed39
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560046"
---
# <a name="advance-notifications-for-planned-maintenance-events-preview"></a>Előzetes értesítések a tervezett karbantartási eseményekhez (előzetes verzió)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A [karbantartási](maintenance-window.md)időszakra (előzetes verzió) konfigurált adatbázisok számára elérhetők az előzetes értesítések (előnézet). Az előzetes értesítések lehetővé teszik az ügyfelek számára, hogy a tervezett események előtt akár 24 óráig is elküldjék az értesítéseket.

Az értesítések konfigurálhatók, így szövegeket, e-maileket, Azure leküldéses értesítéseket és hangpostát kaphat, ha a tervezett karbantartás a következő 24 órában kezdődik. A karbantartás megkezdése és a karbantartás befejezése után a rendszer további értesítéseket küld.

> [!Note]
> Míg az Azure SQL felügyelt példányai esetében lehetőség van karbantartási időszak kiválasztására, az előzetes értesítések jelenleg nem érhetők el az Azure SQL felügyelt példányain.

## <a name="create-an-advance-notification"></a>Előzetes értesítés létrehozása

Az előzetes értesítések olyan Azure SQL Database-adatbázisokhoz érhetők el, amelyeken a karbantartási időszak konfigurálva van. 

Az értesítések engedélyezéséhez hajtsa végre az alábbi lépéseket.  

1. Lépjen a [tervezett karbantartás](https://portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance) oldalra, válassza az **állapot riasztások** lehetőséget, majd **adja hozzá a szolgáltatás állapotára vonatkozó riasztást**.

    :::image type="content" source="media/advance-notifications/health-alerts.png" alt-text="új állapot-riasztási menüpont létrehozása":::

2. A **műveletek** szakaszban válassza a **műveleti csoportok hozzáadása** elemet. 

    :::image type="content" source="media/advance-notifications/add-action-group.png" alt-text="műveleti csoport menüpont hozzáadása":::

3. Fejezze be a **műveleti csoport létrehozása** űrlapot, majd válassza a **Tovább: értesítések** lehetőséget.  

    :::image type="content" source="media/advance-notifications/create-action-group.png" alt-text="műveleti csoport létrehozása űrlap":::

1. Az **értesítések** lapon válassza ki az **értesítés típusát**. Az **e-mail-/SMS-üzenet/leküldéses/** hangvételi lehetőség a legnagyobb rugalmasságot kínálja, és az ajánlott beállítás. Válassza ki a tollat az értesítés konfigurálásához.  

    :::image type="content" source="media/advance-notifications/notifications.png" alt-text="értesítések konfigurálása":::



   1. Fejezze be a megnyíló *értesítés hozzáadása vagy szerkesztése* űrlapot, és kattintson **az OK gombra**: 

   2. A műveletek és címkék megadása nem kötelező. Itt konfigurálhatja az aktiválható további műveleteket, vagy címkékkel kategorizálhatja és rendszerezheti az Azure-erőforrásokat. 

   4. Tekintse **át a felülvizsgálat + létrehozás** lapon található részleteket, és válassza a **Létrehozás** lehetőséget. 

7. A létrehozás kiválasztása után megnyílik a riasztási szabály konfigurációja képernyő, és a rendszer kiválasztja a műveleti csoportot. Adjon nevet az új riasztási szabálynak, majd válassza ki az erőforráscsoportot, majd válassza a **riasztási szabály létrehozása** lehetőséget. 

8. Kattintson ismét az **állapot riasztások** menüelemre, és a riasztások listája mostantól tartalmazza az új riasztást. 


Készen is van. Amikor legközelebb egy tervezett Azure SQL-karbantartási eseményt kap, értesítést fog kapni.

## <a name="receiving-notifications"></a>Értesítések fogadása

Az alábbi táblázat a lehetséges általános információkkal kapcsolatos értesítéseket tartalmazza: 

|Állapot|Leírás|
|:---|:---|
|**Tervezett üzembe helyezés**| A karbantartási esemény előtt 24 órával korábban érkezett. A karbantartás a DB XYZ-hez tartozó, 17:00 – 08:00 (helyi idő) közötti DÁTUMra van tervezve.|
|**Folyamatban** | A **   rendszer megkezdi az XYZ adatbázis karbantartását.| 
|**Kész** | Az *XYZ* adatbázis karbantartása befejeződött. |

A következő táblázat a karbantartás közben elküldhető további értesítéseket tartalmazza: 

|Állapot|Leírás|
|:---|:---|
|**Kiterjesztett** | A karbantartás folyamatban van, de az *XYZ* adatbázis nem fejeződött be. A karbantartás a következő karbantartási időszakban folytatódik.| 
|**Megszakítva**| Az *XYZ* adatbázis karbantartását megszakították, és később újra kell ütemezni. |
|**Blokkolva**|Probléma merült fel az *XYZ* adatbázis karbantartása során. A folytatáshoz értesítjük Önt.| 
|**Folytatódik**|A probléma megoldódott, és a karbantartás a következő karbantartási időszakon belül folytatódni fog.|


## <a name="next-steps"></a>Következő lépések

- [Karbantartási időszak](maintenance-window.md)
- [Karbantartási időszak – gyakori kérdések](maintenance-window-faq.yml)
- [A Microsoft Azure riasztásainak áttekintése](../../azure-monitor/alerts/alerts-overview.md)
- [Azure Resource Manager-szerepkör küldése e-mailben](../../azure-monitor/alerts/action-groups.md#email-azure-resource-manager-role)