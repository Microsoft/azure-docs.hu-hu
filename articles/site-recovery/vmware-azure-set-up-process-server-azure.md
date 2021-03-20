---
title: Folyamat-kiszolgáló VMware/fizikai feladat-visszavétel beállítása Azure Site Recovery
description: Ez a cikk azt ismerteti, hogyan állíthat be egy folyamat-kiszolgálót az Azure-ban az Azure-beli virtuális gépek VMware-re való visszavételéhez.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: ramamill
ms.openlocfilehash: 16214fa07b14c8db2f32fa2c69739b7fa3dc1907
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008479"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Azure-beli folyamatkiszolgálók beállítása feladat-visszavételhez

Miután a VMware virtuális gépeket vagy fizikai kiszolgálókat az Azure-ba felhasználta [site Recovery](site-recovery-overview.md)segítségével, visszatérhet a helyszíni helyre, amikor újra működik és fut. A feladat-visszavételhez létre kell hoznia egy ideiglenes folyamat-kiszolgálót az Azure-ban az Azure-ból a helyszíni rendszerbe történő replikáció kezeléséhez. Ezt a virtuális gépet a feladat-visszavétel befejezése után törölheti.

## <a name="before-you-start"></a>Előkészületek

További információ az ismételt [védelemről](vmware-azure-reprotect.md) és a feladat- [visszavétel](vmware-azure-failback.md) folyamatáról.

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Folyamat-kiszolgáló üzembe helyezése az Azure-ban

1. A tárolóban > **site Recovery infrastruktúra** a >    >  **konfigurációs kiszolgálók** kezelése lapon válassza ki a konfigurációs kiszolgálót.
2. A kiszolgáló lapon kattintson a **+ folyamat kiszolgáló** elemre.
3. A **folyamat-kiszolgáló hozzáadása** oldalon, és válassza a Process Server üzembe helyezése az Azure-ban lehetőséget.
4. Itt adhatja meg az Azure-beállításokat, beleértve a feladatátvételhez használt előfizetést, az erőforráscsoportot, a feladatátvételhez használt Azure-régiót, valamint azt a virtuális hálózatot, amelyben az Azure-beli virtuális gépek találhatók. Ha több Azure-hálózatot is használt, mindegyikhez szükség van egy Process Serverre.

   ![Process Server-gyűjtemény hozzáadása elem](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. A **kiszolgáló neve**, a **Felhasználónév** és a **jelszó** mezőben adja meg a folyamat kiszolgálójának nevét, valamint a rendszergazdai jogosultságokkal rendelkező hitelesítő adatokat a kiszolgálón.
5. Itt adhatja meg a kiszolgálói virtuálisgép-lemezekhez használandó Storage-fiókot, valamint azt az alhálózatot, amelyben a folyamat-kiszolgáló virtuális gép található, valamint a kiszolgáló IP-címe, amely a virtuális gép indításakor lesz hozzárendelve.
6. Kattintson az **OK** gombra a Process Server virtuális gép üzembe helyezésének megkezdéséhez. A Process Server Standard_A8_v2 SKU-ra lesz telepítve. Győződjön meg arról, hogy ez a virtuálisgép-SKU elérhető az előfizetéséhez.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>A (Azure-ban futó) folyamat-kiszolgáló regisztrálása egy konfigurációs kiszolgálóra (helyszíni rendszeren)

Miután a Process Server-alapú virtuális gép működik, regisztrálnia kell a helyszíni konfigurációs kiszolgálóval, a következőképpen:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


