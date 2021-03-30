---
title: A Azure Site Recovery-beli fizikai kiszolgálókhoz tartozó célként megadott környezet beállítása
description: Ez a cikk azt ismerteti, hogyan állítható be az Azure-környezet a fizikai kiszolgálók vész-helyreállításához Azure Site Recovery használatával.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 685f04b27cd315b04ef5c45e155e825eebe2747b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "73953903"
---
# <a name="prepare-target-vmware-to-azure"></a>Cél előkészítése (VMware – Azure)

Ez a cikk azt ismerteti, hogyan készítse elő az Azure-környezetet a Windows vagy Linux rendszerű fizikai kiszolgálók (x64) Azure-ba való replikálásának megkezdéséhez.

## <a name="prerequisites"></a>Előfeltételek

A cikk a következőket feltételezi:
- Létrehozott egy Recovery Services tárolót a fizikai kiszolgálók elleni védelemhez. Recovery Services-tárolót a [Azure Portal](https://portal.azure.com "Azure Portal")hozhat létre.
- A helyszíni [környezetet úgy állította](physical-azure-disaster-recovery.md) be, hogy a fizikai kiszolgálókat az Azure-ba replikálja.

## <a name="prepare-target"></a>Cél előkészítése

Miután befejezte az **1. lépést: válassza a védelmi cél** és a **2. lépés: forrás előkészítése** lehetőséget, a **3. lépés: cél**

![Cél előkészítése](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Előfizetés:** A legördülő menüben válassza ki azt az előfizetést, amelyre a fizikai kiszolgálókat replikálni szeretné.
2. **Üzembe helyezési modell:** Válassza ki a telepítési modellt (klasszikus vagy Resource Manager)

A kiválasztott üzemi modell alapján a rendszer ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Storage-fiókkal és virtuális hálózattal a cél előfizetésben a fizikai kiszolgálók replikálásához és feladatátvételéhez.

Az érvényesítés sikeres befejezése után kattintson az OK gombra a következő lépéshez való ugráshoz.

Ha nem rendelkezik kompatibilis Resource Manager-fiókkal vagy virtuális hálózattal, a lap tetején található **+ Storage-fiókra** vagy **+ hálózati** gombokra kattintva létrehozhat egyet.

## <a name="next-steps"></a>Következő lépések
[Konfigurálja a replikációs beállításokat](vmware-azure-set-up-replication.md).
