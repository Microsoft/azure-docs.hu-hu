---
title: Azure-beli virtuális gép figyelése Azure Monitor
description: Megtudhatja, hogyan gyűjthet és elemezheti az Azure-beli virtuális gépek adatait Azure Monitorban.
ms.service: azure-monitor
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 03/10/2020
ms.openlocfilehash: defeeb42340cbc3203141561aa33a2b4b7b00bb1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100628616"
---
# <a name="quickstart-monitor-an-azure-virtual-machine-with-azure-monitor"></a>Gyors útmutató: Azure-beli virtuális gép figyelése Azure Monitor
[Azure monitor](../overview.md) elindítja az adatok gyűjtését az Azure-beli virtuális gépekről a létrehozásuk pillanatában. Ebben a rövid útmutatóban röviden ismerteti az Azure-beli virtuális gépek számára automatikusan összegyűjtött adatokat, valamint azt, hogy miként tekintheti meg a Azure Portalban. Ezután engedélyezi [Azure monitor for VMS](../vm/vminsights-overview.md) a virtuális géphez, amely lehetővé teszi az ügynökök számára a virtuális gép számára az adatok gyűjtését és elemzését a vendég operációs rendszerből, beleértve a folyamatokat és a függőségeiket.

A rövid útmutató feltételezi, hogy rendelkezik egy meglévő Azure-beli virtuális géppel. Ha nem, létrehozhat egy [Windows rendszerű virtuális gépet](../../virtual-machines/windows/quick-create-portal.md) , vagy létrehozhat egy [linuxos virtuális gépet](../../virtual-machines/linux/quick-create-cli.md) , amely a VM-útmutatókat követi.

Az Azure-erőforrásokból gyűjtött monitorozási adatok részletes ismertetését lásd: Azure-beli [virtuális gépek figyelése Azure monitorokkal](../insights/monitor-vm-azure.md).


## <a name="complete-the-monitor-an-azure-resource-quickstart"></a>Fejezze be az Azure-erőforrás monitorozását.
Az előfizetéshez tartozó virtuális gép áttekintés lapját, a tevékenység naplóját és a metrikáit az Azure Monitor segítségével tekintheti meg az [Azure-erőforrások figyelésével](../learn/quick-monitor-azure-resource.md) . Az Azure-beli virtuális gépek ugyanazt a figyelési adatot gyűjtik, mint bármely más Azure-erőforrást, de ez csak a gazda virtuális gép esetében van. A rövid útmutató hátralévő részében a vendég operációs rendszer és a számítási feladatok figyelésére fog összpontosítani.


## <a name="enable-azure-monitor-for-vms"></a>Azure Monitor for VMs engedélyezése
A rendszer a gazdagép-virtuális gép metrikáit és tevékenységi naplóit gyűjti, és egy olyan konfigurációt igényel, amely a vendég operációs rendszer és a munkaterhelések figyelési adatainak gyűjtésére és elemzésére szolgál. Azure Monitor for VMs telepíti ezeket az ügynököket, és további hatékony funkciókat biztosít a virtuális gépek monitorozásához.

1. Nyissa meg a virtuális gép menüjét.
2. Az **Áttekintés** lapon kattintson **az adatok beolvasása elemre** , vagy kattintson az **adatok elemzése** lehetőségre a **figyelés** menüben.

    ![Áttekintő oldal](media/quick-monitor-azure-vm/overview-insights.png)

3. Ha Azure Monitor for VMs még nincs engedélyezve a virtuális gépen, kattintson az **Engedélyezés** gombra. 

    ![Betekintés engedélyezése](media/quick-monitor-azure-vm/enable-insights.png)

4. Ha a virtuális gép még nincs csatolva Log Analytics munkaterülethez, a rendszer kérni fogja, hogy válasszon ki egy meglévő munkaterületet, vagy hozzon létre egy újat. Válassza ki az alapértelmezett értéket, amely egy egyedi névvel rendelkező munkaterület, amely ugyanabban a régióban található, mint a virtuális gép.

    ![Munkaterület kiválasztása](media/quick-monitor-azure-vm/select-workspace.png)

5. Az előkészítés eltarthat néhány percig, amíg a bővítmények engedélyezve vannak, és a rendszer ügynököket telepít a virtuális gépre. Ha elkészült, egy üzenet jelenik meg, amely szerint a rendszer sikeresen telepítette az eredményeket. A Azure Monitor for VMs megnyitásához kattintson a **Azure monitor** elemre.

    ![Azure Monitor megnyitása](media/quick-monitor-azure-vm/azure-monitor.png)

6. A virtuális gépet az előfizetésében szereplő összes más virtuális géppel megtekintheti. Válassza a **nem figyelt** lapot, ha szeretné megtekinteni az előfizetésében nem szereplő virtuális gépeket.

    ![Bevezetés](media/quick-monitor-azure-vm/get-started.png)


## <a name="configure-workspace"></a>Munkaterület konfigurálása
Új Log Analytics munkaterület létrehozásakor be kell állítani a naplók gyűjtését. Ezt a konfigurációt csak egyszer kell elvégezni, mivel a rendszer a konfigurációt minden olyan virtuális géphez elküldi, amely csatlakozik hozzá.

1. Válassza a **munkaterület-konfiguráció** lehetőséget, majd válassza ki a munkaterületet.

2. **Speciális beállítások** kiválasztása

    ![A Log Analytics speciális beállításai](../vm/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

### <a name="data-collection-from-windows-vm"></a>Adatgyűjtés Windows rendszerű virtuális gépekről


2. Válassza az **Adatok**, majd a **Windows Eseménynaplók** lehetőséget.

3. Adjon hozzá egy eseménynaplót a napló nevének beírásával.  Írja be a **rendszer** elemet, majd válassza ki a plusz jelet **+** .

4. A táblázatban jelölje be a **Hiba** és **Figyelmeztetés** súlyossági szintet.

5. A konfiguráció mentéséhez kattintson a **Save (Mentés** ) gombra a lap tetején.

### <a name="data-collection-from-linux-vm"></a>Adatgyűjtés Linux rendszerű virtuális gépekről

1. Válassza **az** adatelemet, majd válassza a **syslog** lehetőséget.

2. Adjon hozzá egy eseménynaplót a napló nevének beírásával.  Írja be a **syslog** -t, majd válassza ki a plusz jelet **+** .  

3. A táblázatban törölje a megjelenő **adatokat**, a **figyelmeztetést** és a **hibakeresést**. 

4. A konfiguráció mentéséhez kattintson a **Save (Mentés** ) gombra a lap tetején.

## <a name="view-data-collected"></a>Összegyűjtött adatok megtekintése

7. Kattintson a virtuális gépre, majd válassza a **teljesítmény** fület, amely a **figyelés** menü **elemzése csempén** található. Ez a virtuális gép vendég operációs rendszerében gyűjtött teljesítményszámlálók kiválasztási csoportját mutatja be. Görgessen lefelé a további számlálók megjelenítéséhez, és mozgassa az egeret egy gráfra az átlag és a százalékos érték különböző időpontokban való megtekintéséhez.

    ![A képernyőfelvételen a teljesítmény panel látható.](media/quick-monitor-azure-vm/performance.png)

9. Válassza a **Térkép** lehetőséget a Maps funkció megnyitásához, amely a virtuális gépen futó folyamatokat és azok függőségeit jeleníti meg. Válassza a **Tulajdonságok** lehetőséget a Tulajdonságok ablaktábla megnyitásához, ha még nincs megnyitva.

    ![A képernyőképen a Térkép ablaktábla látható.](media/quick-monitor-azure-vm/map.png)

11. Bontsa ki a virtuális gép folyamatait. Válassza ki az egyik folyamatot a részletek megtekintéséhez és a függőségek kiemeléséhez.

    ![Képernyőfelvétel: a Térkép ablaktábla a virtuális gépek kibontott folyamataival bővült.](media/quick-monitor-azure-vm/processes.png)

12. Válassza újra a virtuális gépet, majd válassza a **naplózási események** lehetőséget. 

    ![Események naplózása](media/quick-monitor-azure-vm/log-events.png)

13. A virtuális gép Log Analytics munkaterületén tárolt táblák listája látható. A lista eltérő lehet attól függően, hogy Windows vagy Linux rendszerű virtuális gépet használ-e. Kattintson az **esemény** táblára. Ez magában foglalja a Windows-Eseménynapló összes eseményét. Log Analytics megnyílik egy egyszerű lekérdezéssel az Eseménynapló bejegyzéseinek lekéréséhez.

    ![Log Analytics](media/quick-monitor-azure-vm/log-analytics.png)

## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban engedélyezte a Azure Monitor for VMst egy virtuális géphez, és konfigurálta az Log Analytics munkaterületet a vendég operációs rendszer eseményeinek gyűjtésére. Az adatok megtekintésének és elemzésének ismertetéséhez lépjen tovább az útmutatóhoz.

> [!div class="nextstepaction"]
> [Adatok megtekintése és elemzése a Log Analyticsben](../log-query/log-analytics-tutorial.md)