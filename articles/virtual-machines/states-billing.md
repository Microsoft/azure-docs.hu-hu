---
title: Az Azure Virtual Machines állapotai és számlázása
description: A virtuális gép által megadható különböző állapotok áttekintése, valamint a felhasználó számlázása.
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: c206cba3b23a0bf41ce32481980aa466e869bcde
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596297"
---
# <a name="states-and-billing-of-azure-virtual-machines"></a>Az Azure Virtual Machines állapotai és számlázása

Az Azure Virtual Machines (VM-EK) különböző állapotokon haladnak át, amelyek beoszthatók *kiépítés* és *energiaellátási* állapotba. Ennek a cikknek a célja, hogy leírja ezeket az állapotokat, és kiemelje, hogy mikor kell kiszámítani az ügyfeleket a példányok használatáért. 

## <a name="get-states-using-instance-view"></a>Állapotok beolvasása a példány nézet használatával

A példány nézet API a virtuális gép futtatásával kapcsolatos információkat biztosít. További információkért tekintse meg a [Virtual Machines-példány nézet](/rest/api/compute/virtualmachines/instanceview) API dokumentációját.

Az Azure Resources Explorer egyszerű KEZELŐFELÜLETet biztosít a virtuális gép futási állapotának megtekintéséhez: [erőforrás-kezelő](https://resources.azure.com/).

A kiépítési állapotok a virtuális gép tulajdonságai és a példány nézetében láthatók. A Power állapotok a virtuális gép példány nézetében érhetők el.

Az előfizetésben lévő összes virtuális gép energiaellátási állapotának lekéréséhez használja [Virtual Machines az összes olyan API-](/rest/api/compute/virtualmachines/listall) t, amelynek **statusOnly** a paraméter értéke *true (igaz*).

> [!NOTE]
> [Virtual Machines – felsorolja az összes olyan API-](/rest/api/compute/virtualmachines/listall) t, amelynek **statusOnly** értéke TRUE (igaz) értékre van állítva az előfizetésben lévő összes virtuális gép energiaellátási állapota. Bizonyos ritka helyzetekben azonban előfordulhat, hogy a beolvasási folyamat időszakos problémái miatt nem érhető el a tápellátási állapot. Ilyen helyzetekben azt javasoljuk, hogy próbálkozzon ugyanazzal az API-val, vagy használjon [Azure Resource Health](../service-health/resource-health-overview.md) vagy [Azure Resource Graphot](..//governance/resource-graph/overview.md) a virtuális gépek energiagazdálkodási állapotának vizsgálatához.
 
## <a name="power-states-and-billing"></a>Energiaellátási állapotok és számlázás

A Power állapot a virtuális gép utolsó ismert állapotát jelöli.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-power-states.png" alt-text="Ábrán látható, hogy a virtuális gép milyen Power állapotokat tud áthaladni. ":::

A következő táblázat ismerteti az egyes példányok állapotát, és azt jelzi, hogy a példány használatban van-e, vagy sem.

| Energiaellátási állapot | Description | Számlázás |  
|---|---|---|
| Indítás| A virtuális gép be van kapcsolva. |Nem számlázott * | 
| Futó | A virtuális gép teljesen fel van készülve. Ez a szabványos Munkaállapot. | Számlázása | 
| Leállítás | Ez egy átmeneti állapot a Futtatás és a leállítva között. | Számlázása| 
|Leállítva | A virtuális gép le lett állítva a vendég operációs rendszerből vagy az erő API-k használatával. Ebben az állapotban a virtuális gép továbbra is bérbe adja a mögöttes hardvert. Ezt az állapotot *leállítottnak (lefoglaltnak)* is nevezik. | Számlázása | 
| Felszabadítás | Ez a Futtatás és a visszafoglalás közötti átmeneti állapot. | Nem számlázott * | 
| Felszabadítva | A virtuális gép kiadta a bérletet a mögöttes hardveren, és teljesen ki van kapcsolva. Ennek az állapotnak a neve *leállítva (nem lefoglalt)*. | Nem számlázott * | 

Bizonyos Azure-erőforrások, például a [lemezek](https://azure.microsoft.com/pricing/details/managed-disks) és a [hálózatkezelés](https://azure.microsoft.com/pricing/details/bandwidth/) &#42; továbbra is díjat számítunk fel.


## <a name="provisioning-states"></a>Kiépítési állapotok

A kiépítési állapot a felhasználó által kezdeményezett, vezérlő-sík művelet állapota a virtuális gépen. Ezek az állapotok eltérhetnek a virtuális gép energiagazdálkodási állapotától.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-provisioning-states.png" alt-text="A képen láthatók azok a kiépítési állapotok, amelyek a virtuális gépek átmehetnek.":::

| Kiépítési állapot | Description | Energiaellátási állapot | Számlázás | 
|---|---|---|---|
| Létrehozás | Virtuális gépek létrehozása. | Indítás | Nem számlázott * | 
| Frissítés | Frissíti egy meglévő virtuális gép modelljét. Néhány nem Modelles változás egy virtuális gépen, például az Indítás és az újraindítás a frissítési állapot alá esik. | Futó | Számlázása | 
| Törlés | Virtuális gép törlése. | Felszabadítás | Nem számlázott * |
| Felszabadítás | A virtuális gép teljesen leállt, és el lett távolítva a mögöttes gazdagépről. A virtuális gép felszabadítása frissítésnek minősül, és a frissítéshez hasonló kiépítési állapotokat fog megjeleníteni. | Felszabadítás | Nem számlázott * | 

Bizonyos Azure-erőforrások, például a [lemezek](https://azure.microsoft.com/pricing/details/managed-disks) és a [hálózatkezelés](https://azure.microsoft.com/pricing/details/bandwidth/) &#42; továbbra is díjat számítunk fel.

## <a name="os-provisioning-states"></a>OPERÁCIÓSRENDSZER-kiépítési állapotok
Az operációs rendszer kiépítési állapota csak az operációsrendszer-lemezképpel létrehozott virtuális gépekre vonatkozik. A speciális lemezképek nem fogják megjeleníteni ezeket az állapotokat. 

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/os-provisioning-states.png" alt-text="A rendszerkép megjeleníti az operációs rendszer kiépítési állapotát, amelyet a virtuális gép átmehet.":::

| Operációs rendszer kiépítési állapota | Description | Energiaellátási állapot | Számlázás | 
|---|---|---|---|
| OSProvisioningInProgress | A virtuális gép fut, és a vendég operációs rendszer telepítése folyamatban van. | Futó | Számlázása | 
| OSProvisioningComplete | Ez egy rövid életű állapot. A virtuális gép gyorsan át tud térni ettől az állapottól a **sikerig**. Ha a bővítmények telepítése még folyamatban van, akkor továbbra is ezt az állapotot fogja látni, amíg be nem fejeződik. | Futó | Számlázása | 
| Sikeres | A felhasználó által kezdeményezett műveletek befejeződtek. | Futó | Számlázása | 
| Sikertelen | Sikertelen műveletet jelöl. További információért és lehetséges megoldásokhoz tekintse meg a hibakódot. | Futó  | Számlázása | 


## <a name="next-steps"></a>Következő lépések
- Tekintse át a [Azure Cost Management és a számlázási dokumentációt](../cost-management-billing/index.yml)
- Az [Azure díjszabási kalkulátor](https://azure.microsoft.com/pricing/calculator/) használatával tervezze meg az üzemelő példányokat.
- További információ a virtuális gép figyeléséről: [Virtual Machines in Azure (virtuális gépek figyelése az Azure-ban](../azure-monitor/vm/monitor-vm-azure.md)).