---
title: Áttelepítési Azure Monitor naplók frissítése a Azure Portal
description: Ebből a cikkből megtudhatja, hogyan telepítheti át Azure Monitor naplók frissítését a Azure Portalra.
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 2e94191e80d39e28d7ff0ffc9aa22b522fda68c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576026"
---
# <a name="migrate-azure-monitor-logs-update-deployments-to-azure-portal"></a>Áttelepítési Azure Monitor naplók frissítése a Azure Portal

Az Operations Management Suite-(OMS-) portál [elavult](../azure-monitor/logs/oms-portal-transition.md). A OMS-portálon Update Management összes funkciója elérhető a Azure Portalban Azure Monitor naplókon keresztül. Ez a cikk azokat az információkat tartalmazza, amelyeket át kell telepíteni a Azure Portalra.

## <a name="key-information"></a>Legfontosabb információk

* A meglévő telepítések továbbra is működni fognak. Ha újra létrehozta az üzembe helyezést az Azure-ban, törölheti a régi központi telepítést.
* A OMS összes meglévő funkciója elérhető az Azure-ban. További információ a Update Managementről: [Update Management Overview (áttekintés](./update-management/overview.md)).

## <a name="access-the-azure-portal"></a>Az Azure Portal elérése

1. A munkaterületen kattintson a **Megnyitás az Azure-ban** lehetőségre. 

    ![Megnyitás az Azure-ban – Log Analytics](media/migrate-oms-update-deployments/link-to-azure-portal.png)

2. A Azure Portal kattintson az **Automation-fiók** elemre.

    ![Azure Monitor-naplók](media/migrate-oms-update-deployments/log-analytics.png)

3. Az Automation-fiókban kattintson a **Update Management** elemre.

    :::image type="content" source="media/migrate-oms-update-deployments/azure-automation.png" alt-text="Képernyőkép az Update Management oldalról.":::

4. A Azure Portal az **összes szolgáltatás** területen válassza az **Automation-fiókok** lehetőséget. 

5. A **felügyeleti eszközök** területen válassza ki a megfelelő Automation-fiókot, majd kattintson a **Update Management** elemre.

## <a name="recreate-existing-deployments"></a>Meglévő központi telepítések újbóli létrehozása

A OMS-portálon létrehozott összes frissítési központi telepítésnek más néven számítógépcsoport [néven is](../azure-monitor/logs/computer-groups.md) ismertnek kell lennie, amelynek a neve megegyezik a létező frissítési központi telepítéssel. A mentett keresés a frissítés központi telepítésében ütemezett számítógépek listáját tartalmazza.

:::image type="content" source="media/migrate-oms-update-deployments/oms-deployment.png" alt-text="Képernyőfelvétel a frissítések központi telepítése oldalról a Kiemelt név és kiszolgálók mezőkkel.":::

A meglévő mentett keresés használatához kövesse az alábbi lépéseket:

1. Új frissítés központi telepítésének létrehozásához nyissa meg a Azure Portal, válassza ki a használt Automation-fiókot, majd kattintson az **Update Management** elemre. Kattintson a **frissítés központi telepítésének ütemezett frissítése** elemre.

    ![Frissítés központi telepítésének ütemezett időpontja](media/migrate-oms-update-deployments/schedule-update-deployment.png)

2. Megnyílik az új frissítés központi telepítése panel. Adja meg az alábbi táblázatban leírt tulajdonságok értékeit, majd kattintson a **Létrehozás** gombra:

3. A **frissíteni kívánt gépek** esetében válassza ki a OMS-telepítés által használt mentett keresést.

    | Tulajdonság | Leírás |
    | --- | --- |
    |Név |A frissítéstelepítést beazonosító egyedi név. |
    |Operációs rendszer| Válassza a **Linux** vagy a **Windows** lehetőséget.|
    |Frissítendő gépek |Válasszon ki egy mentett keresést, importált csoportot, vagy válasszon ki egy gépet a legördülő listából, és válassza az egyes gépek lehetőséget. Ha a **Gépek** lehetőséget választotta, a gép állapota az **ÜGYNÖK KÉSZÜLTSÉGÉNEK FRISSÍTÉSE** oszlopban látható.</br> A számítógépcsoportok Azure Monitor-naplókban való létrehozásának különböző módszereiről a következő témakörben talál további információt: [számítógépcsoportok Azure monitor-naplókban](../azure-monitor/logs/computer-groups.md) |
    |Frissítési besorolások|Válassza ki az összes szükséges frissítési besorolást. A CentOS nem támogatja ezt a jelölőnégyzetet.|
    |Kizárási frissítések|Adja meg a kizárni kívánt frissítéseket. Windows esetén **a KB-os előtag nélkül** adja meg a tudásbázist. Linux esetén adja meg a csomag nevét, vagy használjon helyettesítő karaktert.  |
    |Ütemezési beállítások|Válassza ki a kezdéshez szükséges időt, majd válassza ki az ismétlődést **egyszer** vagy **ismétlődően** . | 
    | Karbantartási időszak |A frissítések számára beállított percek száma. Az érték nem lehet kevesebb, mint 30 perc vagy több mint 6 óra. |
    | Vezérlő újraindítása| Meghatározza az újraindítások kezelését.</br>Az elérhető lehetőségek:</br>Újraindítás szükség esetén (alapértelmezett beállítás)</br>Mindig induljon újra</br>Soha ne induljon újra</br>Csak újraindítás – frissítések nem lesznek telepítve|

4. Az újonnan létrehozott frissítés központi telepítésének állapotának megtekintéséhez kattintson az **ütemezett frissítések központi telepítések** elemre.

    ![új frissítés telepítése](media/migrate-oms-update-deployments/new-update-deployment.png)

5. Ahogy korábban említettük, az új központi telepítések a Azure Portalon keresztül történő konfigurálása után eltávolíthatja a meglévő központi telepítéseket a Azure Portalról.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Automation Update Managementáról: [Update Management Overview](./update-management/overview.md).