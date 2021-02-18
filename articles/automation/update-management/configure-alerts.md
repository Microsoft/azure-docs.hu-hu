---
title: Riasztások létrehozása Azure Automation Update Management
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja az Azure-riasztásokat, hogy tájékoztassanak a frissítési felmérések vagy üzembe helyezések állapotáról.
services: automation
ms.subservice: update-management
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 74207fe088034ff8d102fb2254d8ab78a6d57671
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579702"
---
# <a name="how-to-create-alerts-for-update-management"></a>Riasztások létrehozása Update Managementhoz

Az Azure-beli riasztások proaktív módon értesítik Önt a runbook-feladatok, a szolgáltatás állapotával kapcsolatos problémák vagy az Automation-fiókkal kapcsolatos egyéb forgatókönyvek eredményeiről. A Azure Automation nem tartalmaz előre konfigurált riasztási szabályokat, de saját maga is létrehozhatja a saját által létrehozott adatai alapján. Ez a cikk útmutatást nyújt a riasztási szabályok létrehozásához a Update Managementban található mérőszámok használatával.

## <a name="available-metrics"></a>Rendelkezésre álló metrikák

A Azure Automation két különböző platform-metrikát hoz létre, amelyek a begyűjtött és a Azure Monitor továbbított Update Managementhoz kapcsolódnak. Ezek a metrikák a [Metrikaböngésző](../../azure-monitor/essentials/metrics-charts.md) és a [metrikák riasztási szabályainak](../../azure-monitor/alerts/alerts-metric.md)használatával történő riasztásokhoz érhetők el.

A két kibocsátott metrika a következők:

* Összes frissítés központi telepítési gép futtatása
* Összes frissítés központi telepítési futtatása

A riasztások használata esetén mindkét metrika olyan dimenziókat támogat, amelyek további információkat biztosítanak, hogy a riasztást egy adott frissítés központi telepítésére vonatkozó részletes adatokra szűkítse. A következő táblázat a riasztások konfigurálásakor elérhető metrika és dimenziók részleteit mutatja be.

|Jel neve|Dimenziók|Description
|---|---|---|
|`Total Update Deployment Runs`|-Központi telepítési név frissítése<br>– Állapot | Riasztások egy frissítés központi telepítésének általános állapotáról.|
|`Total Update Deployment Machine Runs`|-Központi telepítési név frissítése</br>– Állapot</br>– Célszámítógép</br>-A központi telepítés futtatási AZONOSÍTÓjának frissítése    |Adott gépekre irányuló frissítési központi telepítés állapotára vonatkozó riasztások.|

## <a name="create-alert"></a>Riasztás létrehozása

Kövesse az alábbi lépéseket a riasztások beállításához, hogy megismerje a frissítés központi telepítésének állapotát. Ha még nem ismeri az Azure-riasztásokat, tekintse meg az [Azure-riasztások áttekintése](../../azure-monitor/alerts/alerts-overview.md)című témakört.

1. Az Automation-fiókban válassza a **figyelés** területen a **riasztások** elemet, majd válassza az **új riasztási szabály** lehetőséget.

2. A **riasztási szabály létrehozása** lapon az Automation-fiók már ki van választva erőforrásként. Ha módosítani szeretné, válassza az **erőforrás szerkesztése** lehetőséget.

3. Az erőforrás kiválasztása lapon válassza az Automation- **fiókok** lehetőséget a **szűrés erőforrás típusa** legördülő listából.

4. Válassza ki a használni kívánt Automation-fiókot, majd kattintson a **kész** gombra.

5. Válassza a **feltétel hozzáadása** lehetőséget a követelménynek megfelelő jel kiválasztásához.

6. Egy dimenzió esetében válasszon ki egy érvényes értéket a listából. Ha a kívánt érték nem szerepel a listában, válassza a **\+** dimenzió melletti elemet, és írja be az egyéni nevet. Ezután válassza ki a keresni kívánt értéket. Ha egy dimenzió összes értékét ki szeretné választani, kattintson a **kiválasztás \*** gombra. Ha nem választja ki a dimenzió értékét, Update Management figyelmen kívül hagyja ezt a dimenziót.

    ![Jellogika konfigurálása](./media/manage-updates-for-vm/signal-logic.png)

7. A **riasztási logika** szakaszban adja meg az értékeket az **idő összesítése** és a **küszöbérték** mezőkben, majd válassza a **kész** lehetőséget.

8. A következő lapon adja meg a riasztás nevét és leírását.

9. Állítsa be a **Súlyosság** mezőt a **tájékoztatási (2** . szint) értékre a sikeres futtatáshoz vagy **tájékoztatáshoz (az 1.** szinthez) a sikertelen futtatáshoz.

    ![Képernyőfelvétel: a riasztás részleteinek meghatározása szakasz a riasztási szabály nevének, leírásának és súlyossági mezőinek kiemelésével.](./media/manage-updates-for-vm/define-alert-details.png)

10. A riasztási szabály engedélyezéséhez válassza az **Igen** lehetőséget.

## <a name="configure-action-groups-for-your-alerts"></a>Műveleti csoportok konfigurálása a riasztásokhoz

Miután beállította a riasztásokat, beállíthat egy műveleti csoportot, amely a több riasztáson keresztül használható műveletek csoportja. A műveletek tartalmazhatnak e-mailes értesítéseket, runbookok, webhookokat és még sok mást. A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../../azure-monitor/alerts/action-groups.md) szóló cikkben talál.

1. Válasszon ki egy riasztást, majd válassza az **új létrehozása** a **műveleti csoportok** alatt lehetőséget.

2. Adjon meg egy teljes nevet és egy rövid nevet a műveleti csoportnak. A Update Management a rövid nevet használja az értesítések megadott csoport használatával történő küldésekor.

3. A **műveletek** területen adjon meg egy nevet, amely megadja a műveletet, például az **e-mail értesítéseket**.

4. A **Művelettípus** mezőben válassza ki a megfelelő típust, például **e-mail/SMS/leküldéses/hang**.

5. Válassza a **Részletek szerkesztése** lehetőséget.

6. Adja meg a művelet típusát a ablaktáblán. Ha például az **e-mailek/SMS/leküldés/hang** lehetőséget használja, adja meg a művelet nevét, jelölje be az **e-mail** jelölőnégyzetet, adjon meg egy érvényes e-mail-címet, majd kattintson **az OK gombra**.

    ![E-mail műveletcsoport konfigurálása](./media/manage-updates-for-vm/configure-email-action-group.png)

7. A Műveletcsoport hozzáadása ablaktáblán kattintson az **OK** gombra.

8. Riasztási e-mail esetén testreszabhatja az e-mail tárgyát. Válassza a **műveletek testreszabása** a **szabály létrehozása** alatt lehetőséget, majd válassza az **e-mail tárgy** elemet.

9. Ha végzett, kattintson a **Riasztási szabály létrehozása** gombra.

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure monitor riasztásokról](../../azure-monitor/alerts/alerts-overview.md).

* Tudnivalók a Log Analytics-munkaterületről származó adatok lekérdezéséhez és elemzéséhez szükséges [naplókról](../../azure-monitor/logs/log-query-overview.md) .

* A [használat és a költségek kezelése Azure monitor naplókkal](../../azure-monitor/logs/manage-cost-storage.md) : az adatmegőrzési időszak módosításával, valamint az adatfelhasználás elemzésével és riasztásával kapcsolatos költségek szabályozása.