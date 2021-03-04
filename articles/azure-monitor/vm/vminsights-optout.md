---
title: A figyelés letiltása a virtuális gépekről
description: Ez a cikk azt ismerteti, hogyan lehet leállítani a virtuális gépek figyelését a VM-információkban.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 2de0dcd52745ebadb02ab8dbb563e28abf2822dc
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046482"
---
# <a name="disable-monitoring-of-your-vms-in-vm-insights"></a>Virtuális gépek figyelésének letiltása a VM-alapú adatvizsgálatokban

Miután engedélyezte a virtuális gépek (VM-EK) figyelését, később letilthatja a figyelést a virtuálisgép-betekintő szolgáltatásban. Ez a cikk bemutatja, hogyan tilthatja le egy vagy több virtuális gép figyelését.  

Jelenleg a virtuális gépek elemzése nem támogatja a virtuális gépek figyelésének szelektív letiltását. A Log Analytics munkaterület a virtuális gépekkel és más megoldásokkal is támogathatja. Más figyelési adatokat is gyűjthet. Ha a Log Analytics munkaterület biztosítja ezeket a szolgáltatásokat, meg kell ismernie a figyelés letiltásának hatását és metódusait a Kezdés előtt.

A virtuális gépekkel kapcsolatos elemzések a következő összetevőkből állnak:

* Log Analytics munkaterület, amely a virtuális gépek és más források megfigyelési adatait tárolja.
* A munkaterületen konfigurált teljesítményszámlálók gyűjteménye. A gyűjtemény frissíti a figyelési konfigurációt a munkaterülethez csatlakozó összes virtuális gépen.
* `VMInsights`, amely a munkaterületen konfigurált figyelési megoldás. Ez a megoldás frissíti a figyelési konfigurációt a munkaterülethez csatlakozó összes virtuális gépen.
* `MicrosoftMonitoringAgent` és az `DependencyAgent` Azure VM-bővítmények. Ezek a bővítmények adatokat gyűjtenek és küldenek a munkaterületre.

A virtuális gépek figyelésének letiltására való felkészülés során tartsa szem előtt ezeket a szempontokat:

* Ha egyetlen virtuális géppel lett kiértékelve, és az előre kiválasztott alapértelmezett Log Analytics munkaterületet használta, letilthatja a figyelést, ha eltávolítja a függőségi ügynököt a virtuális gépről, és leválasztja a Log Analytics ügynököt erről a munkaterületről. Ez a megközelítés akkor megfelelő, ha a virtuális gépet más célokra szeretné használni, és később úgy dönt, hogy újracsatlakozik egy másik munkaterülethez.
* Ha olyan már meglévő Log Analytics munkaterületet jelölt ki, amely más felügyeleti megoldásokat és adatgyűjtést is támogat más forrásokból, akkor a munkaterület megszakítása vagy a munkaterületének módosítása nélkül távolíthatja el a megoldás-összetevőket a munkaterületről.  

>[!NOTE]
> Miután eltávolította a megoldás-összetevőket a munkaterületről, továbbra is megtekintheti az Azure-beli virtuális gépek teljesítmény-és leképezési adatait. Az adat végül leáll a **teljesítmény** és a **Térkép** nézetben. Az **Engedélyezés** lehetőség elérhető lesz a kiválasztott Azure-beli virtuális gépről, így később ismét engedélyezheti a figyelést.  

## <a name="remove-vm-insights-completely"></a>A VM-adatfelismerések teljes eltávolítása

Ha továbbra is szüksége van a Log Analytics munkaterületre, az alábbi lépéseket követve teljes mértékben eltávolíthatja a virtuális gépeket. A `VMInsights` megoldást a munkaterületről távolíthatja el.  

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A Azure Portal válassza a **minden szolgáltatás** lehetőséget. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a bemenet alapján szűri a javaslatokat. Válassza a **Log Analytics** elemet.
3. Log Analytics munkaterületek listájában válassza ki a virtuális gépek elemzésekor kiválasztott munkaterületet.
4. A bal oldalon válassza a **megoldások** elemet.  
5. A megoldások listájában válassza a **VMInsights (munkaterület neve)** elemet. A megoldás **Áttekintés** lapján válassza a **Törlés** lehetőséget. Amikor a rendszer felszólítja a megerősítésre, válassza az **Igen** lehetőséget.

## <a name="disable-monitoring-and-keep-the-workspace"></a>A figyelés letiltása és a munkaterület megtartása  

Ha a Log Analytics-munkaterületnek továbbra is támogatnia kell a más forrásokból történő figyelést, kövesse ezeket a lépéseket a virtuális gépekre vonatkozó információk kiértékeléséhez használt virtuális gép figyelésének letiltásához. Azure-beli virtuális gépek esetén távolítsa el a függőségi ügynök virtuálisgép-bővítményét, valamint a Windows vagy Linux rendszerhez készült Log Analytics Agent virtuálisgép-bővítményt közvetlenül a virtuális gépről. 

>[!NOTE]
>Ne távolítsa el a Log Analytics ügynököt, ha: 
>
> * Azure Automation felügyeli a virtuális gépet a folyamatok előkészítéséhez, illetve a konfiguráció vagy a frissítések kezeléséhez. 
> * Azure Security Center felügyeli a virtuális gépet a biztonsági és a veszélyforrások észleléséhez. 
>
> Ha eltávolítja a Log Analytics ügynököt, megakadályozza, hogy a szolgáltatások és megoldások proaktív módon kezelhesse a virtuális gépet. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. A Azure Portal válassza a **Virtual Machines** lehetőséget. 
3. Válasszon ki egy virtuális gépet a listából. 
4. A bal oldalon válassza a **bővítmények** lehetőséget. A **bővítmények** lapon válassza a **DependencyAgent** lehetőséget.
5. A bővítmény tulajdonságai lapon válassza az **Eltávolítás** lehetőséget.
6. A **bővítmények** lapon válassza a **MicrosoftMonitoringAgent** lehetőséget. A bővítmény tulajdonságai lapon válassza az **Eltávolítás** lehetőséget.  
