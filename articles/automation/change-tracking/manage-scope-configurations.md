---
title: Azure Automation Change Tracking és a leltár központi telepítési hatókörének korlátozása
description: Ez a cikk azt ismerteti, hogyan használhatók a hatókör-konfigurációk a Change Tracking és a leltári telepítés hatókörének korlátozására.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 6eb9a20920f0a340491459f0875fc85b90dfa193
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92209680"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>A Change Tracking és a leltár központi telepítési hatókörének korlátozása

Ez a cikk azt ismerteti, hogyan használhatók a hatókör-konfigurációk a [change Tracking és leltár](overview.md) funkció használatakor a virtuális gépek változásainak üzembe helyezéséhez. További információ: [figyelési megoldások célzása Azure monitor (előzetes verzió)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>A hatókör-konfigurációk ismertetése

A hatókör-konfiguráció egy vagy több mentett keresés (lekérdezés) csoportja, amely a Change Tracking és a leltár hatókörének adott számítógépekre való korlátozására szolgál. A hatókör-konfiguráció a Log Analytics munkaterületen található, hogy megcélozza az engedélyezni kívánt számítógépeket. Ha hozzáad egy számítógépet a szolgáltatás változásaihoz, a rendszer a munkaterületen lévő mentett kereséshez is hozzáadja a számítógépet.

## <a name="set-the-scope-limit"></a>Hatókör korlátjának beállítása

A Change Tracking hatókörének és a leltár üzembe helyezésének korlátozása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A Azure Portal válassza a **minden szolgáltatás** lehetőséget. Az erőforrások listájába írja be az **Automation** kifejezést. Ahogy elkezd gépelni, a lista a bemenet alapján szűri a javaslatokat. Válassza az **Automation-fiókok** elemet.

3. Az Automation-fiókok listájában válassza ki azt a fiókot, amelyet a Change Tracking és a leltár engedélyezésekor választott ki.

4. Az Automation-fiókban a **kapcsolódó erőforrások** területen válassza a **csatolt munkaterület** lehetőséget.

5. Kattintson **a munkaterület ugrása** elemre.

6. Válassza ki a **hatókör-konfigurációk (előzetes verzió)** elemet a **munkaterület-adatforrások** területen.

7. Válassza ki a hatókör-konfigurációtól jobbra található három pontot  `MicrosoftDefaultScopeConfig-ChangeTracking` , majd kattintson a **Szerkesztés** gombra.

8. A szerkesztési ablaktáblán válassza a **számítógépcsoportok kiválasztása** lehetőséget. A számítógépcsoportok ablaktáblán láthatók a hatókör-konfiguráció létrehozásához használt mentett keresések. A Change Tracking és a leltár által használt mentett keresés:

    |Name     |Kategória  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  Változáskövetési       | ChangeTracking__MicrosoftDefaultComputerGroup        |

9. A mentett keresés elem kiválasztásával megtekintheti és szerkesztheti a csoport feltöltéséhez használt lekérdezést. Az alábbi ábrán a lekérdezés és annak eredményei láthatók:

    ![Mentett keresések](media/manage-scope-configurations/logsearch.png)

## <a name="next-steps"></a>Következő lépések

* A Change Tracking és a leltár kezelésével kapcsolatban lásd: a [change Tracking és a leltár kezelése](manage-change-tracking.md).
* Az általános szolgáltatással kapcsolatos problémák elhárításához lásd: [change Tracking és leltározási problémák elhárítása](../troubleshoot/change-tracking.md).
