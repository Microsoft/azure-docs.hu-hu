---
title: Azure Monitor for VMs figyelésének letiltása
description: Ez a cikk azt ismerteti, hogyan lehet leállítani a virtuális gépek figyelését Azure Monitor for VMsban.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 80473aa494b8fbcea5e43870b7717cd3472dd7d1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480521"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms"></a>Virtuális gépek figyelésének letiltása Azure Monitor for VMs

Miután engedélyezte a virtuális gépek (VM-EK) figyelését, később letilthatja a figyelést Azure Monitor for VMsban. Ez a cikk bemutatja, hogyan tilthatja le egy vagy több virtuális gép figyelését.  

A Azure Monitor for VMs jelenleg nem támogatja a virtuális gépek figyelésének szelektív letiltását. A Log Analytics munkaterület Azure Monitor for VMs és más megoldásokat is támogat. Más figyelési adatokat is gyűjthet. Ha a Log Analytics munkaterület biztosítja ezeket a szolgáltatásokat, meg kell ismernie a figyelés letiltásának hatását és metódusait a Kezdés előtt.

A Azure Monitor for VMs a következő összetevőkre támaszkodik a felhasználói élmény megvalósítása érdekében:

* Log Analytics munkaterület, amely a virtuális gépek és más források megfigyelési adatait tárolja.
* A munkaterületen konfigurált teljesítményszámlálók gyűjteménye. A gyűjtemény frissíti a figyelési konfigurációt a munkaterülethez csatlakozó összes virtuális gépen.
* `VMInsights`, amely a munkaterületen konfigurált figyelési megoldás. Ez a megoldás frissíti a figyelési konfigurációt a munkaterülethez csatlakozó összes virtuális gépen.
* `MicrosoftMonitoringAgent`és `DependencyAgent`az Azure VM-bővítmények. Ezek a bővítmények adatokat gyűjtenek és küldenek a munkaterületre.

A virtuális gépek figyelésének letiltására való felkészülés során tartsa szem előtt ezeket a szempontokat:

* Ha egyetlen virtuális géppel lett kiértékelve, és az előre kiválasztott alapértelmezett Log Analytics munkaterületet használta, letilthatja a figyelést, ha eltávolítja a függőségi ügynököt a virtuális gépről, és leválasztja a Log Analytics ügynököt erről a munkaterületről. Ez a megközelítés akkor megfelelő, ha a virtuális gépet más célokra szeretné használni, és később úgy dönt, hogy újracsatlakozik egy másik munkaterülethez.
* Ha olyan már meglévő Log Analytics munkaterületet jelölt ki, amely más felügyeleti megoldásokat és adatgyűjtést is támogat más forrásokból, akkor a munkaterület megszakítása vagy a munkaterületének módosítása nélkül távolíthatja el a megoldás-összetevőket a munkaterületről.  

>[!NOTE]
> Miután eltávolította a megoldás-összetevőket a munkaterületről, továbbra is megtekintheti az Azure-beli virtuális gépek teljesítmény-és leképezési adatait. Az adat végül leáll a **teljesítmény** és a **Térkép** nézetben. Az **Engedélyezés** lehetőség elérhető lesz a kiválasztott Azure-beli virtuális gépről, így később ismét engedélyezheti a figyelést.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Azure Monitor for VMs teljes eltávolítása

Ha továbbra is szüksége van a Log Analytics munkaterületre, a Azure Monitor for VMs teljes eltávolításához kövesse az alábbi lépéseket. A `VMInsights` megoldást a munkaterületről távolíthatja el.  

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A Azure Portal válassza a **minden szolgáltatás**lehetőséget. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a bemenet alapján szűri a javaslatokat. Válassza a **Log Analytics** elemet.
3. Log Analytics munkaterületek listájában válassza ki a Azure Monitor for VMs engedélyezésekor kiválasztott munkaterületet.
4. A bal oldalon válassza a **megoldások**elemet.  
5. A megoldások listájában válassza a **VMInsights (munkaterület neve)** elemet. A megoldás **Áttekintés** lapján válassza a **Törlés**lehetőséget. Amikor a rendszer felszólítja a megerősítésre, válassza az **Igen**lehetőséget.

## <a name="disable-monitoring-and-keep-the-workspace"></a>A figyelés letiltása és a munkaterület megtartása  

Ha a Log Analytics-munkaterületnek továbbra is támogatnia kell más forrásokból származó figyelést, a következő lépésekkel tiltsa le a figyelést a Azure Monitor for VMs kiértékeléséhez használt virtuális gépen. Azure-beli virtuális gépek esetén távolítsa el a függőségi ügynök virtuálisgép-bővítményét, valamint a Windows vagy Linux rendszerhez készült Log Analytics Agent virtuálisgép-bővítményt közvetlenül a virtuális gépről. 

>[!NOTE]
>Ne távolítsa el a Log Analytics ügynököt, ha: 
>
> * Azure Automation felügyeli a virtuális gépet a folyamatok előkészítéséhez, illetve a konfiguráció vagy a frissítések kezeléséhez. 
> * Azure Security Center felügyeli a virtuális gépet a biztonsági és a veszélyforrások észleléséhez. 
>
> Ha eltávolítja a Log Analytics ügynököt, megakadályozza, hogy a szolgáltatások és megoldások proaktív módon kezelhesse a virtuális gépet. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. A Azure Portal válassza a **Virtual Machines**lehetőséget. 
3. Válasszon ki egy virtuális gépet a listából. 
4. A bal oldalon válassza a **bővítmények**lehetőséget. A **bővítmények** lapon válassza a **DependencyAgent**lehetőséget.
5. A bővítmény tulajdonságai lapon válassza az **Eltávolítás**lehetőséget.
6. A **bővítmények** lapon válassza a **MicrosoftMonitoringAgent**lehetőséget. A bővítmény tulajdonságai lapon válassza az **Eltávolítás**lehetőséget.  
