---
title: Áthelyezési hibák elhárítása
description: Az erőforrások új erőforráscsoporthoz vagy előfizetésbe való áthelyezésének hibája.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 41b1e2435caf9874f3582a3394664c7b7f5a8d29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90054162"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Azure-erőforrások új erőforráscsoportba vagy előfizetésbe való áthelyezésének hibakeresése

Ez a cikk javaslatokat nyújt az erőforrások áthelyezésekor felmerülő problémák megoldásához.

## <a name="upgrade-a-subscription"></a>Előfizetés frissítése

Ha valóban frissíteni szeretné az Azure-előfizetését (például az ingyenesről az utólagos elszámolású váltásra), akkor konvertálnia kell az előfizetést.

* Az ingyenes próbaverzió frissítését lásd: [ingyenes próbaverzió frissítése vagy Microsoft Imagine Azure-előfizetés](../../cost-management-billing/manage/upgrade-azure-subscription.md)utólagos elszámolású.
* Az utólagos elszámolású fiók módosításához lásd: [Az Azure utólagos elszámolású előfizetés módosítása egy másik ajánlatra](../../cost-management-billing/manage/switch-azure-offer.md).

Ha nem tudja konvertálni az előfizetést, [hozzon létre egy Azure-támogatási kérést](../../azure-portal/supportability/how-to-create-azure-support-request.md). Válassza az **előfizetés kezelése** lehetőséget a probléma típusához.

## <a name="service-limitations"></a>Szolgáltatási korlátozások

Egyes szolgáltatások az erőforrások áthelyezésekor további szempontokat igényelnek. Ha a következő szolgáltatásokat helyezi át, ellenőrizze az útmutatást és a korlátozásokat.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Klasszikus üzemi modell](./move-limitations/classic-model-move-limitations.md)
* [Hálózat](./move-limitations/networking-move-limitations.md)
* [Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtual Machines](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Nagyméretű kérelmek

Ha lehetséges, a nagy léptékű áthelyezési műveletekre kell váltania. A Resource Manager azonnal hibát jelez, ha több mint 800 erőforrás van egyetlen műveletben. A kevesebb mint 800 erőforrás áthelyezése azonban időtúllépés miatt is meghiúsulhat.

## <a name="resource-not-in-succeeded-state"></a>Az erőforrás nem sikeres állapotban van

Ha olyan hibaüzenetet kap, amely azt jelzi, hogy egy erőforrás nem helyezhető át, mert nem sikeres állapotban van, előfordulhat, hogy egy függő erőforrás, amely blokkolja az áthelyezést. A hibakód általában a **MoveCannotProceedWithResourcesNotInSucceededState**.

Ha a forrás vagy a cél erőforráscsoport virtuális hálózatot tartalmaz, a rendszer az áthelyezés során ellenőrzi, hogy a virtuális hálózat összes függő erőforrásának állapota be van-e jelölve. Az ellenőrzési szolgáltatás közvetlenül és közvetve a virtuális hálózattól függ. Ha valamelyik erőforrás hibás állapotban van, az áthelyezés le lesz tiltva. Ha például a virtuális hálózatot használó virtuális gép meghiúsult, az áthelyezés le lesz tiltva. Az áthelyezés akkor is le van tiltva, ha a virtuális gép nem az áthelyezett erőforrások egyike, és az áthelyezéshez nem az egyik erőforráscsoport.

Ha ezt a hibaüzenetet kapja, két lehetőség közül választhat. Helyezze át az erőforrásokat olyan erőforráscsoporthoz, amely nem rendelkezik virtuális hálózattal, vagy [forduljon az ügyfélszolgálathoz](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Következő lépések

Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](move-resource-group-and-subscription.md).
