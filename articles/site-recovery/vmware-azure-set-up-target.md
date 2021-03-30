---
title: A VMware virtuális gép replikálási céljának előkészítése Azure Site Recovery
description: Ez a cikk azt ismerteti, hogyan készítse elő a cél Azure-környezetet a VMware virtuális gépek Azure-ba történő replikálásához.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: c721234f2e1d806d51d31f3466e441bf8360f6b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "73693168"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>A cél környezet előkészítése VMware virtuális gépek vagy fizikai kiszolgálók Azure-beli vész-helyreállítására

Ez a cikk azt ismerteti, hogyan készítse elő a cél Azure-környezetet a VMware virtuális gépek vagy fizikai kiszolgálók Azure-ba történő replikálásának megkezdéséhez.

## <a name="prerequisites"></a>Előfeltételek

A cikk a következőket feltételezi:
- Létrehozott egy Recovery Services-tárolót a [Azure Portalon](https://portal.azure.com "Azure Portal") a forrásként szolgáló gépek
- A helyszíni környezetet úgy állította be, hogy az Azure-ba replikálja a forrás [VMWare virtuális gépeket](vmware-azure-set-up-source.md) vagy [fizikai kiszolgálókat](physical-azure-set-up-source.md) .

## <a name="prepare-target"></a>Cél előkészítése

Miután befejezte az **1. lépést: válassza a védelmi cél** és a **2. lépés: forrás előkészítése** lehetőséget, a **3. lépés: cél**

![Cél előkészítése](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Előfizetés:** A legördülő menüben válassza ki azt az előfizetést, amelybe a virtuális gépeket vagy fizikai kiszolgálókat replikálni szeretné.
2. **Üzembe helyezési modell:** Válassza ki a telepítési modellt (klasszikus vagy Resource Manager)

A kiválasztott üzemi modell alapján a rendszer ellenőrzi, hogy van-e legalább egy virtuális hálózat a cél előfizetésben a virtuális gép vagy a fizikai kiszolgáló replikálásához és feladatátvételéhez.

Az érvényesítés sikeres befejezése után kattintson az OK gombra a következő lépéshez való ugráshoz.

Ha nem rendelkezik virtuális hálózattal, a lap tetején található **+ hálózat** gombra kattintva létrehozhat egyet.

## <a name="next-steps"></a>Következő lépések
[Konfigurálja a replikációs beállításokat](vmware-azure-set-up-replication.md).
