---
title: 'Azure-partneri szolgáltatás: a kapcsolatok telemetria mérése '
description: Ebből az oktatóanyagból megtudhatja, hogyan méri a kapcsolatok telemetria.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service.
ms.openlocfilehash: abbe69ebbaed56ed416f85fafa7b77a1740fabe7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96022496"
---
# <a name="tutorial-measure-peering-service-connection-telemetry"></a>Oktatóanyag: egyenrangú szolgáltatási kapcsolatok telemetria mérése

 Ebből az oktatóanyagból megtudhatja, hogyan méri a telemetria a társ-szolgáltatási kapcsolatok esetében.
 
 A kapcsolati telemetria az ügyfél és a Microsoft hálózata közötti kapcsolathoz nyújt bepillantást. Ebből a cikkből megtudhatja, hogyan tekintheti meg a késési jelentést egy adott Azure-partneri szolgáltatási kapcsolatban. 

A társ-szolgáltatási kapcsolatok telemetria méréséhez regisztrálnia kell egy társ-szolgáltatási kapcsolatot a Azure Portalban. A kapcsolatok regisztrálásáról további információt a következő témakörben talál: [társ-szolgáltatási kapcsolatok regisztrálása – Azure Portal](azure-portal.md).


## <a name="view-a-latency-report"></a>Késési jelentés megtekintése

Ha egy adott társ-szolgáltatási kapcsolatok késési jelentését szeretné megtekinteni, kövesse az alábbi lépéseket.

1. Válassza ki az **összes erőforrás** elemet a bal oldali ablaktáblán, és válassza ki a társ-szolgáltatási kapcsolatot. Ezután válassza a **Megnyitás** az **előtagok** alatt lehetőséget. 

   ![Válassza ki a társ-szolgáltatási kapcsolatot](./media/peering-service-measure/peering-service-measure-menu.png)

2. Megjelenik egy késleltetési jelentés lapja az ehhez a társ-szolgáltatási kapcsolathoz társított összes előtaghoz. 

      ![Késési jelentés lapja](./media/peering-service-measure/peering-service-latency-report.png)

3. Alapértelmezés szerint a jelentés minden, a lapon megjelenő órában frissül. Ha meg szeretné tekinteni a jelentést a különböző időpontokhoz, válassza ki a megfelelő lehetőséget az **adatok megjelenítéséhez az utolsó** elemnél. 

4. Egy adott előtag eseményeinek megtekintéséhez válassza ki az előtag nevét, és válassza a bal oldali ablaktábla **előtag-események** elemét. Megjelennek a rögzített események.


   ![Előtag-események](./media/peering-service-measure/peering-service-prefix-event.png)

 Itt jelennek meg az **előtag-események** listájában rögzített lehetséges események.

| **Előtag-események** | **Esemény típusa**|**Indoklása**|
|-----------|---------|---------|
| PrefixAnnouncementEvent |Tájékoztatás|Előtag-hirdetmény érkezett|
| PrefixWithdrawalEvent|Figyelmeztetés| Az előtag visszavonása megérkezett |
| PrefixBackupRouteAnnouncementEvent |Tájékoztatás|Az előtag biztonsági mentési útvonalának bejelentése megérkezett |
| PrefixBackupRouteWithdrawalEvent|Figyelmeztetés|Az előtag biztonsági mentési útvonalának visszavonása megérkezett |
| PrefixActivePath |Tájékoztatás| Aktuális előtag aktív útvonala   |
| PrefixBackupPath | Tájékoztatás|Aktuális előtag biztonsági mentési útvonala   |
| PrefixOriginAsChangeEvent|Kritikus| Pontos előtag érkezett a különböző forrás autonóm rendszer számával (aktív útvonalhoz)| 
| PrefixBackupRouteOriginAsChangeEvent  | Hiba|A különböző forrásként kapott előtag (biztonsági mentési útvonal)  |

## <a name="next-steps"></a>Következő lépések

- A társ-szolgáltatási kapcsolatok megismeréséhez tekintse meg a társítási [szolgáltatás kapcsolódása](connection.md)című témakört.
- A társ-szolgáltatási kapcsolatok telemetria kapcsolatos további tudnivalókért lásd: társítási [szolgáltatás kapcsolódási telemetria](connection-telemetry.md).