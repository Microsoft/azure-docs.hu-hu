---
title: Az Azure-beli virtuális gépek karbantartási vezérlése a Azure Portal használatával
description: Megtudhatja, hogyan szabályozhatja, hogy az Azure-beli virtuális gépek karbantartását hogyan alkalmazza a rendszer a karbantartási ellenőrzés és a Azure Portal használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 0dea30723a3ebe1598d6304ac3c98bfe1b55b466
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563013"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>Frissítések vezérlése a karbantartási és a Azure Portal

A karbantartási ellenőrzéssel eldöntheti, hogy mikor alkalmazza a frissítéseket az elkülönített virtuális gépekre és az Azure dedikált gazdagépekre. Ez a témakör a karbantartás-vezérlés Azure Portal beállításait ismerteti. További információ a karbantartási ellenőrzés, a hozzá tartozó korlátozások és egyéb felügyeleti lehetőségek használatáról: a [platform frissítéseinek kezelése a karbantartási ellenőrzéssel](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Karbantartási konfiguráció létrehozása

1. Jelentkezzen be az Azure Portalra.

1. Keressen **karbantartási konfigurációkat**.

   ![A karbantartási konfigurációk megnyitását bemutató képernyőfelvétel](media/virtual-machines-maintenance-control-portal/maintenance-configurations-search.png)

1. Kattintson a **Hozzáadás** parancsra.

   ![A karbantartási konfiguráció hozzáadását bemutató képernyőkép](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add.png)

1. Válassza ki az előfizetést és az erőforráscsoportot, adja meg a konfiguráció nevét, és válasszon régiót. Kattintson a **Tovább** gombra.

   ![Képernyőfelvétel a karbantartási konfiguráció alapjairól](media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics.png)

1. Címkék és értékek hozzáadása. Kattintson a **Tovább** gombra.

   ![A címkék karbantartási konfigurációba való felvételét bemutató képernyőkép](media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags.png)

1. Tekintse át az összegzést. Kattintson a **Létrehozás** lehetőségre.

   ![A karbantartási konfiguráció létrehozását bemutató képernyőkép](media/virtual-machines-maintenance-control-portal/maintenance-configurations-create.png)

1. Az üzembe helyezés befejezése után kattintson az **erőforrás keresése** elemre.

   ![A karbantartási konfiguráció telepítésének befejezését bemutató képernyőkép](media/virtual-machines-maintenance-control-portal/maintenance-configurations-deployment-complete.png)

## <a name="assign-the-configuration"></a>A konfiguráció kiosztása

A karbantartási konfiguráció részletek lapján kattintson a hozzárendelések elemre, majd az **Erőforrás hozzárendelése** elemre. 

![Az erőforrás hozzárendelését bemutató képernyőkép](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

Válassza ki azokat az erőforrásokat, amelyekhez hozzá szeretné rendelni a karbantartási konfigurációt, majd kattintson **az OK** gombra. A **Type (típus** ) oszlopban látható, hogy az erőforrás egy elkülönített virtuális gép vagy egy dedikált Azure-gazdagép. A konfiguráció hozzárendeléséhez a virtuális gépnek futnia kell. Hiba történik, ha egy leállított virtuális géphez próbál meg egy konfigurációt hozzárendelni. 

<!---Shantanu to add details about the error case--->

![Az erőforrás kiválasztását bemutató képernyőkép](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>Konfiguráció keresése

Ellenőrizheti, hogy a konfiguráció helyesen lett-e alkalmazva, vagy ellenőrizze, hogy van-e a karbantartási **konfigurációkhoz** jelenleg hozzárendelt karbantartási konfiguráció. A **Type (típus** ) oszlopban látható, hogy a konfiguráció hozzá van-e rendelve egy elkülönített virtuális géphez vagy egy dedikált Azure-gazdagéphez. 

![A karbantartási konfiguráció ellenőrzését bemutató képernyőkép](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

Egy adott virtuális gép konfigurációját a Tulajdonságok lapján is megtekintheti. Kattintson a **karbantartás** gombra a virtuális géphez rendelt konfiguráció megtekintéséhez.

![A gazdagép karbantartásának ellenőrzését bemutató képernyőkép](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>Függőben lévő frissítések keresése

Két módon is ellenőrizhető, hogy a frissítések függőben vannak-e karbantartási konfigurációra. A **karbantartási konfigurációk** területen a konfiguráció részleteinél kattintson a **hozzárendelések** elemre, és jelölje be a **karbantartási állapotot**.

![A függőben lévő frissítések ellenőrzését bemutató képernyőkép](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

Egy adott gazdagépet a dedikált gazdagép **Virtual Machines** vagy tulajdonságaival is megadhat. 

![A kijelölt karbantartási állapotot bemutató képernyőkép.](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>Frissítések alkalmazása

Igény szerint függőben lévő frissítéseket is alkalmazhat a **Virtual Machines** használatával. A virtuális gép részleteinél kattintson a **karbantartás** elemre, majd kattintson a **karbantartás alkalmazása most** lehetőségre.

![A függőben lévő frissítések alkalmazásának módját bemutató képernyőkép](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>Frissítések alkalmazási állapotának keresése 

A frissítések állapotát a **karbantartási konfigurációk** konfigurációjában, vagy a **Virtual Machines** használatával tekintheti meg. A virtuális gép részleteinél kattintson a **karbantartás** elemre. A következő példában a **karbantartási állapot** egy frissítést **vár függőben**.

![A függőben lévő frissítések állapotának ellenőrzését bemutató képernyőkép](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>Karbantartási konfiguráció törlése

Konfiguráció törléséhez nyissa meg a konfigurációs adatokat, és kattintson a **Törlés** gombra.

![A konfiguráció törlését bemutató képernyőkép.](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>Következő lépések

További információ: [karbantartás és frissítések](maintenance-and-updates.md).
