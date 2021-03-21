---
title: Adatok gyűjtése egy Azure-beli virtuális gépről Azure Monitorsal | Microsoft Docs
description: Itt megtudhatja, hogyan engedélyezheti a Log Analytics-ügynök virtuálisgép-bővítményét, és ezzel az adatok összegyűjtését az Azure-beli virtuális gépekről a Log Analytics segítségével.
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 08/19/2019
ms.custom: mvc, sec-javascript-october2019
ms.openlocfilehash: 2f73614253a1ce442d2a93ce5e9b9d7051542511
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047026"
---
# <a name="collect-data-from-an-azure-virtual-machine-with-azure-monitor"></a>Adatok gyűjtése egy Azure-beli virtuális gépről Azure Monitor

A [Azure monitor](../overview.md) adatokat gyűjthet közvetlenül az Azure-beli virtuális gépekről egy log Analytics-munkaterületre a részletek és a korrelációk elemzéséhez. A Windows és [Linux](../../virtual-machines/extensions/oms-linux.md) [rendszerhez](../../virtual-machines/extensions/oms-windows.md) készült log Analytics virtuálisgép-bővítmény telepítése lehetővé teszi, hogy a Azure monitor adatokat gyűjtsön az Azure-beli virtuális gépekről. Ez a rövid útmutató bemutatja, hogyan konfigurálhat és gyűjthet adatokat az Azure Linux vagy Windows rendszerű virtuális gépekről a virtuálisgép-bővítmény használatával néhány egyszerű lépéssel.  
 
A rövid útmutató feltételezi, hogy rendelkezik egy meglévő Azure-beli virtuális géppel. Ha nem, akkor virtuális gépekre vonatkozó rövid útmutatók alapján létrehozhat egy [Windows virtuális gépet](../../virtual-machines/windows/quick-create-portal.md) vagy egy [Linux virtuális gépet](../../virtual-machines/linux/quick-create-cli.md).

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 

## <a name="create-a-workspace"></a>Munkaterület létrehozása

1. A Azure Portal válassza a **minden szolgáltatás** lehetőséget. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **log Analytics munkaterületek** lehetőséget.

    ![Azure Portal](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Válassza a **Létrehozás** lehetőséget, majd válassza ki a kívánt beállításokat a következő elemekhez:

   * Adja meg az új **Log Analytics-munkaterület** nevét, például: *DefaultLAWorkspace*.  
   * A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
   * Az **Erőforráscsoport** beállításnál válasszon ki egy meglévő erőforráscsoportot, amely egy vagy több Azure-beli virtuális gépet tartalmaz.  
   * Válassza ki a **Helyet** a virtuális gépek üzembehelyezési céljaként.  További információkért tekintse meg [a Log Analytics által támogatott régiókat](https://azure.microsoft.com/regions/services/).
   * Ha 2018. április 2. után létrehozott új előfizetésben hoz létre munkaterületet, az automatikusan a *GB-alapú* díjcsomagot használja, és a tarifacsomag kiválasztásának lehetősége nem érhető el.  Ha az április 2. előtt létrehozott meglévő előfizetéshez hoz létre munkaterületet, vagy pedig egy meglévő EA-regisztrációhoz kötött előfizetéshez, válassza ki a kívánt tarifacsomagot.  További információt az elérhető csomagokról [a Log Analytics részletes díjszabásában](https://azure.microsoft.com/pricing/details/log-analytics/) találhat.
  
        ![Log Analytics-erőforrás létrehozása panel](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Miután megadta a szükséges információkat a **log Analytics munkaterület** ablaktáblán, kattintson az **OK gombra**.  

Az **Értesítések** menüpontot kiválasztva nyomon követheti, hogyan ellenőrzi a rendszer az adatokat, és hogyan hozza létre a munkaterületet. 

## <a name="enable-the-log-analytics-vm-extension"></a>A Log Analytics virtuálisgép-bővítményének engedélyezése

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Az Azure-ban már üzembe helyezett Windows és Linux rendszerű virtuális gépekhez a Log Analytics-ügynököt a virtuálisgép-bővítménnyel kell telepíteni. A bővítmény használata leegyszerűsíti a telepítés folyamatát és automatikusan konfigurálja az ügynököt, hogy elküldje az adatokat a megadott Log Analytics-munkaterületre. Az ügynököt a rendszer automatikusan frissíti, ha újabb verziót bocsátanak ki, amely biztosítja, hogy a legújabb funkciókkal és javításokkal rendelkezzen. A továbblépés előtt ellenőrizze, hogy a virtuális gép fut-e, ellenkező esetben a folyamat sikertelen lesz.  

>[!NOTE]
>A Linuxhoz készült Log Analytics-ügynököt nem lehet úgy konfigurálni, hogy egynél több Log Analytics-munkaterületre is jelentsen. 

1. A Azure Portal válassza a bal felső sarokban található **összes szolgáltatás** elemet. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **log Analytics munkaterületek** lehetőséget.

2. A Log Analytics-munkaterületek listájában válassza ki a korábban létrehozott *DefaultLAWorkspace* elemet.

3. A bal oldali menüben a munkaterület-adatforrások területen válassza a **virtuális gépek** lehetőséget.  

4. Válassza ki a **Virtuális gépek** listájából azt a gépet, amelyre telepíteni szeretné az ügynököt. Figyelje meg, hogy a virtuális gép **Log Analytics-kapcsolat állapota** értékénél **Nincs kapcsolat** látható.

5. A virtuális gép részletei között válassza a **Csatlakozás** lehetőséget. A rendszer automatikusan telepíti és konfigurálja az ügynököt a Log Analytics-munkaterületen. Ez a folyamat néhány percet vesz igénybe, amíg az **állapot** a **csatlakozást** mutatja.

6. Az ügynök telepítése után a **Log Analytics-kapcsolat állapota** frissül, és **Ez a munkaterület** lesz.

## <a name="collect-event-and-performance-data"></a>Esemény- és teljesítményadatok gyűjtése

A Azure Monitor a hosszabb távú elemzésekhez és jelentéskészítéshez megadott Windows-eseménynaplók, illetve a Linux syslog és a teljesítményszámlálók adatait gyűjthetik, és a rendszer egy adott feltétel észlelésekor hajt végre műveleteket. A következő lépésekkel konfigurálhatja az események gyűjtését a Windows vagy Linux rendszernaplókból, illetve (kezdetnek) egyes gyakran használt teljesítményszámlálókból.  

### <a name="data-collection-from-windows-vm"></a>Adatgyűjtés Windows rendszerű virtuális gépekről

1. Válassza ki a **Speciális beállítások** elemet.

    ![A Log Analytics speciális beállításai](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

2. Válassza az **Adatok**, majd a **Windows Eseménynaplók** lehetőséget.

3. Eseménynaplókat a nevük begépelésével adhat hozzá.  Írja be a **rendszer** elemet, majd válassza ki a plusz jelet **+** .

4. A táblázatban jelölje be a **Hiba** és **Figyelmeztetés** súlyossági szintet.

5. A konfiguráció mentéséhez kattintson a **Save (Mentés** ) gombra a lap tetején.

6. Válassza a **Windowsos teljesítményadatok** lehetőséget a teljesítményszámláló-adatok egy windowsos gépen történő gyűjtésének engedélyezéséhez.

7. Amikor először konfigurálja egy új Log Analytics-munkaterület Windows-teljesítményszámlálóit, akkor gyorsan létrehozhat több gyakran használt számlálót. Ezek mindegyike mellett egy jelölőnégyzet található.

    ![Képernyőfelvétel a Windows-teljesítményszámlálók panelről, a kiválasztott számlálók listájával és a kiválasztott teljesítményszámlálók hozzáadása gombbal.](media/quick-collect-azurevm/windows-perfcounters-default.png)

    Válassza **a kijelölt teljesítményszámlálók hozzáadása** lehetőséget.  A rendszer hozzáadja a kiválasztott számlálókat, és mindegyikhez beállít egy tíz másodperces mintagyűjtési időszakot.
  
8. A konfiguráció mentéséhez kattintson a **Save (Mentés** ) gombra a lap tetején.

### <a name="data-collection-from-linux-vm"></a>Adatgyűjtés Linux rendszerű virtuális gépekről

1. Válassza a **Syslog** elemet.  

2. Eseménynaplókat a nevük begépelésével adhat hozzá.  Írja be a **syslog** -t, majd válassza ki a plusz jelet **+** .  

3. A táblázatban törölje a megjelenő **adatokat**, a **figyelmeztetést** és a **hibakeresést**. 

4. A konfiguráció mentéséhez kattintson a **Save (Mentés** ) gombra a lap tetején.

5. Válassza a **Linuxos teljesítményadatok** elemet a teljesítményszámláló-adatok gyűjtésének Linux rendszerű gépen történő engedélyezéséhez. 

6. Amikor először konfigurálja egy új Log Analytics-munkaterület Linux-teljesítményszámlálóit, akkor több gyakran használt számlálót is gyorsan létrehozhat. Ezek mindegyike mellett egy jelölőnégyzet található.

    ![Képernyőfelvétel a Linux-teljesítményszámlálók panelről, a kiválasztott számlálók listájával és a kiválasztott teljesítményszámlálók hozzáadása gombbal.](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Válassza az **alábbi konfiguráció alkalmazása a saját gépekre** lehetőséget, majd kattintson **a kijelölt teljesítményszámlálók hozzáadása** lehetőségre.  A rendszer hozzáadja a kiválasztott számlálókat, és mindegyikhez beállít egy tíz másodperces mintagyűjtési időszakot.  

7. A konfiguráció mentéséhez kattintson a **Save (Mentés** ) gombra a lap tetején.

## <a name="view-data-collected"></a>Összegyűjtött adatok megtekintése

Most, hogy engedélyezte az adatgyűjtést, futtasson le egy egyszerű naplóbeli keresést, hogy megtekinthessen néhány, a cél virtuális gépekről származó adatot.  

1. A kiválasztott munkaterületen, a bal oldali ablaktáblán válassza a **naplók** lehetőséget.

2. A naplók lekérdezése lapon írja be `Perf` a lekérdezés-szerkesztőt, és válassza a **Futtatás** lehetőséget.

    ![Példa Log Analytics-naplókeresési lekérdezésre](./media/quick-collect-windows-computer/log-analytics-portal-queryexample.png) 

    A következő képen szereplő lekérdezés például 10 000 teljesítményadatokat adott vissza. Ön ennél sokkal kevesebb találatot fog kapni.

    ![Log Analytics naplóbeli keresés eredménye](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a Log Analytics-munkaterületet. Ehhez válassza ki a korábban létrehozott Log Analytics munkaterületet, és az erőforrás lapon válassza a **Törlés** lehetőséget.


![Log Analytics-erőforrás törlése](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy már gyűjti a működési és teljesítményadatokat a Windows vagy Linux rendszerű virtuális gépeiről, könnyen nekiláthat az *ingyenesen* gyűjtött adatok felfedezésének és elemzésének, és reagálhat a kapott eredményekre.  

Az adatok megtekintésének és elemzésének ismertetéséhez lépjen tovább az útmutatóhoz.

> [!div class="nextstepaction"]
> [Adatok megtekintése és elemzése a Log Analyticsben](../logs/log-analytics-tutorial.md)