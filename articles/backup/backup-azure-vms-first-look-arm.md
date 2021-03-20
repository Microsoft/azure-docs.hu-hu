---
title: Azure-beli virtuális gép biztonsági mentése a virtuális gép beállításaiból
description: Ebből a cikkből megtudhatja, hogyan készíthet biztonsági mentést egy egyedi Azure-beli vagy több Azure-beli virtuális gépről a Azure Backup szolgáltatással.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 55b71d2a2901cdde984df3ebfd68a2a643b78b74
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89667519"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Azure-beli virtuális gép biztonsági mentése a virtuális gép beállításaiból

Ez a cikk azt ismerteti, hogyan lehet biztonsági másolatot készíteni az Azure-beli virtuális gépekről a [Azure Backup](backup-overview.md) szolgáltatással. Az Azure-beli virtuális gépek biztonsági mentését több módszer használatával végezheti el:

- Egyetlen Azure-beli virtuális gép: a cikk utasításai azt írják le, hogyan lehet biztonsági másolatot készíteni egy Azure-beli virtuális gépről közvetlenül a virtuális gép beállításairól.
- Több Azure-beli virtuális gép: beállíthat egy Recovery Services-tárolót, és konfigurálhat több Azure-beli virtuális gép biztonsági mentését. Ehhez a forgatókönyvhöz kövesse az [ebben a cikkben](backup-azure-arm-vms-prepare.md) szereplő utasításokat.

## <a name="before-you-start"></a>Előkészületek

1. [Ismerje meg](backup-architecture.md#how-does-azure-backup-work) , hogyan működik a biztonsági mentés, és [ellenőrizze](backup-support-matrix.md#azure-vm-backup-support) a támogatási követelményeket.
2. Az Azure-beli virtuális gépek biztonsági mentésének [áttekintése](backup-azure-vms-introduction.md) .

### <a name="azure-vm-agent-installation"></a>Azure VM-ügynök telepítése

Az Azure-beli virtuális gépek biztonsági mentéséhez Azure Backup telepít egy bővítményt a gépen futó virtuálisgép-ügynökön. Ha a virtuális gép Azure Piactéri rendszerképből lett létrehozva, az ügynök futni fog. Bizonyos esetekben például, ha egyéni virtuális gépet hoz létre, vagy egy gépet telepít át a helyszínen, előfordulhat, hogy manuálisan kell telepítenie az ügynököt.

- Ha manuálisan kell telepítenie a virtuálisgép-ügynököt, kövesse a Windows vagy [Linux](../virtual-machines/extensions/agent-linux.md) [rendszerű](../virtual-machines/extensions/agent-windows.md) virtuális gépekre vonatkozó utasításokat.
- Miután telepítette az ügynököt, a biztonsági mentés engedélyezésekor Azure Backup telepíti a biztonsági mentési bővítményt az ügynöknek. A felhasználó beavatkozása nélkül frissíti és kibővíti a bővítményt.

## <a name="back-up-from-azure-vm-settings"></a>Biztonsági mentés az Azure-beli virtuális gép beállításaiból

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza a **minden szolgáltatás** lehetőséget, majd a szűrő mezőbe írja be a **virtuális gépek** elemet, majd válassza a **virtuális gépek** lehetőséget.
3. A virtuális gépek listájából válassza ki azt a virtuális gépet, amelyről biztonsági másolatot szeretne készíteni.
4. A virtuális gép menüben válassza a **biztonsági mentés** lehetőséget.
5. A **Recovery Services**-tárolóban tegye a következőket:
   - Ha már rendelkezik tárolóval, válassza a **meglévő kiválasztása** lehetőséget, majd válasszon ki egy tárolót.
   - Ha nem rendelkezik tárolóval, válassza az **új létrehozása** lehetőséget. Adja meg a tároló nevét. Ugyanabban a régióban és erőforráscsoporthoz lett létrehozva, mint a virtuális gép. Ezek a beállítások nem módosíthatók, ha közvetlenül a virtuális gép beállításaiból engedélyezi a biztonsági mentést.

        ![Biztonsági mentés engedélyezése varázsló](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. A **biztonsági mentési szabályzat kiválasztása** területen tegye a következők egyikét:

   - Hagyja meg az alapértelmezett házirendet. Ezzel a beállítással naponta egyszer biztonsági másolatot készít a virtuális gépről, és 30 napig megőrzi a biztonsági mentéseket a tárolóban.
   - Ha rendelkezik ilyennel, válasszon ki egy meglévő biztonsági mentési szabályzatot.
   - Hozzon létre egy új szabályzatot, és adja meg a házirend-beállításokat.  

       ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Válassza a **Biztonsági mentés engedélyezése** parancsot. Ez a biztonsági mentési szabályzatot a virtuális géppel társítja.

    ![Biztonsági mentés engedélyezése gomb](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. A konfigurációs folyamat nyomon követhető a portál értesítéseiben.
9. A feladatok befejezése után a virtuális gép menüben válassza a **biztonsági mentés** lehetőséget. A lapon látható a virtuális gép biztonsági mentési állapota, a helyreállítási pontokra vonatkozó információk, a futó feladatok és a kiadott riasztások.

   ![Biztonsági másolat állapota](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. A biztonsági mentés engedélyezése után a rendszer egy kezdeti biztonsági mentést futtat. A kezdeti biztonsági mentést azonnal elindíthatja, vagy megvárhatja, amíg a biztonsági mentési ütemezésnek megfelelően elindul.
    - A kezdeti biztonsági mentés befejezéséig a **legutóbbi biztonsági mentés állapota** **Figyelmeztetés (kezdeti biztonsági mentés függőben)**.
    - Ha szeretné megtekinteni, hogy mikor fog futni a következő ütemezett biztonsági mentés, válassza ki a biztonsági mentési szabályzat nevét.

## <a name="run-a-backup-immediately"></a>Biztonsági mentés azonnali futtatása

1. A biztonsági mentés azonnali futtatásához a virtuális gép menüjében válassza a **biztonsági** mentés biztonsági mentése lehetőséget  >  .

    ![Biztonsági mentés futtatása](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. A **biztonsági mentés** során a Calendar (naptár) vezérlőelem segítségével válassza ki, hogy a helyreállítási pont Mikor maradjon meg > és **az OK gombra**.

    ![Biztonsági mentés megőrzésének napja](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. A portál értesítéseiből megtudhatja, hogy a biztonsági mentési feladatok aktiválva lettek. A biztonsági mentési folyamat figyeléséhez válassza az **összes feladat megtekintése** lehetőséget.

## <a name="back-up-from-the-recovery-services-vault"></a>Biztonsági mentés a Recovery Services-tárból

A [cikk](backup-azure-arm-vms-prepare.md) utasításait követve engedélyezheti az Azure-beli virtuális gépek biztonsági mentését egy Azure Backup Recovery Services-tároló beállításával, valamint a biztonsági mentés engedélyezésével a tárban.

## <a name="next-steps"></a>Következő lépések

- Ha a jelen cikkben ismertetett eljárások valamelyikével kapcsolatban nehézségekbe ütközik, tekintse meg a [hibaelhárítási útmutatót](backup-azure-vms-troubleshoot.md).
- [További](backup-azure-manage-vms.md) információ a biztonsági másolatok kezeléséről.
