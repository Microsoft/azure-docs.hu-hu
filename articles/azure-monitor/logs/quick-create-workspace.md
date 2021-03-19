---
title: Log Analytics munkaterület létrehozása a Azure Portalban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Log Analytics munkaterületet a Felhőbeli és a helyszíni környezetekben lévő felügyeleti megoldások és adatgyűjtés engedélyezéséhez a Azure Portal.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/18/2021
ms.openlocfilehash: 27eac9cefe645087cae43c34cb6503b562fb7c07
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656302"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Log Analytics-munkaterület létrehozása az Azure Portalon
Az **log Analytics munkaterületek** menüjéből hozzon létre egy log Analytics munkaterületet a Azure Portal használatával. A Log Analytics munkaterület egy egyedi környezet, amely Azure Monitor naplózza az adatnaplót. Az egyes munkaterületek saját adattárral és konfigurációval rendelkeznek, és az adatforrások és megoldások úgy vannak konfigurálva, hogy egy adott munkaterületen tárolják az adataikat. Ha a következő forrásokból szeretne adatgyűjtést végrehajtani, Log Analytics munkaterületre van szüksége:

* Azure-erőforrások az előfizetésében
* System Center Operations Manager által figyelt helyszíni számítógépek
* Configuration Managerból származó eszközök gyűjteményei 
* Diagnosztika vagy naplózási adatok az Azure Storage-ból

Más forrásokhoz, például az Azure-beli virtuális gépekhez és a környezetben található Windows-vagy Linux-alapú virtuális gépekhez a következő témakörökben talál további információt:

*  [Adatok gyűjtése az Azure Virtual Machines szolgáltatásból](../vm/quick-collect-azurevm.md) 
*  [Adatok gyűjtése hibrid Linux rendszerű számítógépről](../vm/quick-collect-linux-computer.md)
*  [Adatok gyűjtése hibrid Windows-számítógépről](../vm/quick-collect-windows-computer.md)

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 

## <a name="create-a-workspace"></a>Munkaterület létrehozása
Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **log Analytics munkaterületek** lehetőséget.

![Azure Portal](media/quick-create-workspace/azure-portal-01.png)
  
Kattintson a **Hozzáadás** gombra, és adja meg a következő beállítások értékeit:

   * A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
   * Az **erőforráscsoport** beállításnál válasszon egy már meglévő erőforráscsoportot, vagy hozzon létre egy újat.  
   * Adja meg az új **Log Analytics-munkaterület** nevét, például: *DefaultLAWorkspace*. Ennek a névnek globálisan egyedinek kell lennie az összes Azure Monitor-előfizetésen belül.
   * Válasszon ki egy elérhető **régiót**.  További információkért tekintse meg, hogy mely [régiók log Analytics érhetők el](https://azure.microsoft.com/regions/services/) , és keressen rá a Azure monitor kifejezésre a **termék keresése** területen.  


        ![Log Analytics-erőforrás létrehozása panel](media/quick-create-workspace/create-workspace.png)  


Kattintson a **felülvizsgálat + létrehozás** elemre, és tekintse át a beállításokat, majd **hozza** létre a munkaterületet. Ezzel kiválaszthatja az utólagos elszámolású csomag alapértelmezett díjszabását, amely nem eredményez semmilyen változást, amíg el nem éri a megfelelő mennyiségű adatok gyűjtését. További információ az egyéb díjszabási csomagokról: [log Analytics díjszabása](https://azure.microsoft.com/pricing/details/log-analytics/).



## <a name="troubleshooting"></a>Hibaelhárítás
Ha olyan munkaterületet hoz létre, amely az elmúlt 14 napban törölve lett, és nem [törölhető állapotban](../logs/delete-workspace.md#soft-delete-behavior)van, a művelet a munkaterület konfigurációjától függően eltérő eredménnyel járhat:
1. Ha ugyanazt a munkaterület-nevet, erőforráscsoportot, előfizetést és régiót adja meg, mint a törölt munkaterületen, a munkaterület helyreállítva lesz, beleértve az adatait, a konfigurációját és a csatlakoztatott ügynököket is.
2. Ha ugyanazt a munkaterület-nevet használja, de más erőforráscsoportot, előfizetést vagy régiót használ, hibaüzenet jelenik meg, hogy a *munkaterület neve már használatban van. Próbáljon meg egy másikat*. Az alábbi lépéseket követve felülbírálhatja a munkaterület helyreállítható törlését és végleges törlését, és létrehozhatja az új munkaterületet ugyanazzal a névvel, a munkaterület helyreállításához és az állandó törlés végrehajtásához hajtsa végre a következő lépéseket:
   - Munkaterület [helyreállítása](../logs/delete-workspace.md#recover-workspace)
   - Munkaterület [végleges törlése](../logs/delete-workspace.md#permanent-workspace-delete)
   - Új munkaterület létrehozása ugyanazzal a munkaterület-névvel

## <a name="next-steps"></a>Következő lépések
Most, hogy elérhetővé tett egy munkaterületet, beállíthatja a figyelési telemetria gyűjteményét, futtathatja a naplók kereséseit az adatelemzéshez, és hozzáadhat egy felügyeleti megoldást további információk és elemzési elemzések megadásához. 

* Lásd: [log Analytics munkaterület állapotának figyelése Azure monitor](../logs/monitor-workspace.md) riasztási szabályok létrehozása a munkaterület állapotának figyeléséhez. 
* Az Azure-erőforrások Azure Diagnostics vagy az Azure Storage használatával történő adatgyűjtésének engedélyezéséhez tekintse meg az [Azure-szolgáltatás naplófájljainak és metrikáinak gyűjtése a log Analytics való használatra](../essentials/resource-logs.md#send-to-log-analytics-workspace) című témakört.
