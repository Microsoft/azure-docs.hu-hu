---
title: Biztonsági mentés engedélyezése Azure-beli virtuális gép létrehozásakor
description: Ismerteti, hogyan engedélyezhető a biztonsági mentés, ha Azure-beli virtuális gépet hoz létre Azure Backup használatával.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: ad81300545686d61f42cdd8684e502c937b4fd43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89377335"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Biztonsági mentés engedélyezése Azure-beli virtuális gép létrehozásakor

Az Azure Virtual Machines (VM) biztonsági mentéséhez használja a Azure Backup szolgáltatást. A virtuális gépek a biztonsági mentési szabályzatban megadott ütemezésnek megfelelően készülnek, a helyreállítási pontok pedig biztonsági másolatokból jönnek létre. A helyreállítási pontok tárolása Recovery Services-tárolókban történik.

Ez a cikk azt ismerteti, hogyan engedélyezhető a biztonsági mentés a virtuális gép (VM) a Azure Portalban történő létrehozásakor.  

## <a name="before-you-start"></a>Előkészületek

- [Győződjön meg](backup-support-matrix-iaas.md#supported-backup-actions) arról, hogy mely operációs rendszerek támogatottak, ha a virtuális gép létrehozásakor engedélyezi a biztonsági mentést.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha még nem jelentkezett be a fiókjába, jelentkezzen be a [Azure Portalba](https://portal.azure.com).

## <a name="create-a-vm-with-backup-configured"></a>Virtuális gép létrehozása a Backup szolgáltatással konfigurálva

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget.

2. Az Azure Marketplace-en válassza a **számítás** lehetőséget, majd válasszon ki egy virtuálisgép-rendszerképet.

3. Állítsa be a virtuális gépet a [Windows](../virtual-machines/windows/quick-create-portal.md) -vagy [Linux](../virtual-machines/linux/quick-create-portal.md) -utasításoknak megfelelően.

4. A **kezelés** lap **biztonsági mentés engedélyezése** területén válassza **a be** lehetőséget.
5. Azure Backup biztonsági mentéseket egy Recovery Services-tárolóba. Válassza az **új létrehozása** lehetőséget, ha nem rendelkezik meglévő tárolóval.
6. Fogadja el a javasolt tár nevét, vagy adja meg a sajátját.
7. Itt adhatja meg vagy hozhatja létre azt az erőforráscsoportot, amelyben a tároló található. Az erőforráscsoport-tároló eltérő lehet a virtuálisgép-erőforráscsoporthoz.

    ![Virtuális gép biztonsági mentésének engedélyezése](./media/backup-during-vm-creation/enable-backup.png)

8. Fogadja el az alapértelmezett biztonsági mentési szabályzatot, vagy módosítsa a beállításokat.
    - A biztonsági mentési szabályzat meghatározza, hogy a virtuális gép milyen gyakran készítsen biztonsági mentési pillanatképeket, és hogy mennyi ideig tart a biztonsági másolatok megőrzése.
    - Az alapértelmezett házirend naponta egyszer biztonsági másolatot készít a virtuális gépről.
    - Az Azure-beli virtuális gépekre vonatkozó saját biztonsági mentési szabályzatok testreszabásával napi vagy heti biztonsági mentést készíthet.
    - [További](backup-azure-vms-introduction.md#backup-and-restore-considerations) információ az Azure-beli virtuális gépek biztonsági mentésével kapcsolatos megfontolásokról.
    - [További](backup-instant-restore-capability.md) információ az azonnali visszaállítási funkcióról.

      ![Alapértelmezett biztonsági mentési szabályzat](./media/backup-during-vm-creation/daily-policy.png)

>[!NOTE]
>[Az SSE és a páros főkulcs az Azure-beli virtuális gépek alapértelmezett titkosítási módszere](backup-encryption.md) . Azure Backup támogatja az Azure-beli virtuális gépek biztonsági mentését és visszaállítását.

## <a name="azure-backup-resource-group-for-virtual-machines"></a>Virtual Machines Azure Backup erőforráscsoport

A Backup szolgáltatás egy külön erőforráscsoportot (RG) hoz létre, amely eltér a virtuális gép erőforráscsoporthoz, amely a visszaállítási pontok gyűjteményét (RPC) tárolja. Az RPC a felügyelt virtuális gépek azonnali helyreállítási pontjait üzemelteti. A Backup szolgáltatás által létrehozott erőforráscsoport alapértelmezett elnevezési formátuma a (z `AzureBackupRG_<Geo>_<number>` ):. Például: *AzureBackupRG_northeurope_1*. Most testreszabhatja Azure Backup által létrehozott erőforráscsoport-nevet.

Megjegyzés:

1. Használhatja a RG alapértelmezett nevét, vagy szerkesztheti a vállalati követelmények szerint.
2. Adja meg a RG neve mintát bemenetként a virtuális gép biztonsági mentési szabályzatának létrehozásakor. A RG nevének a következő formátumúnak kell lennie: `<alpha-numeric string>* n <alpha-numeric string>` . az "n" kifejezés egy egész számmal (1-től kezdődően) van lecserélve, és az első RG megtelte esetén felskálázásra szolgál. Egy RG jelenleg legfeljebb 600 távoli eljáráshívások lehet.
              ![Válassza ki a nevet a szabályzat létrehozásakor](./media/backup-during-vm-creation/create-policy.png)
3. A mintában az alábbi RG-elnevezési szabályoknak kell szerepelnie, és a teljes hossz nem haladhatja meg a maximálisan megengedett RG-név hosszát.
    1. Az erőforráscsoportok nevei csak alfanumerikus karaktereket, pontokat, aláhúzást, kötőjelet és zárójelet tartalmazhatnak. Nem végződhet ponttal.
    2. Az erőforráscsoportok nevei legfeljebb 74 karaktert tartalmazhatnak, beleértve a RG nevét és az utótagot is.
4. Az első `<alpha-numeric-string>` kötelező, míg a második az "n" után nem kötelező. Ez csak akkor érvényes, ha a testreszabott nevet adja meg. Ha nem ad meg semmit a szövegmezők egyikében sem, a rendszer az alapértelmezett nevet használja.
5. A RG nevét szerkesztheti a szabályzat módosításával, ha szükséges. Ha a név minta módosul, a rendszer az új RPs-t hozza létre az új RG-ban. A régi RPs azonban továbbra is a régi RG-ban található, és nem helyezhető át, mivel az RP-gyűjtemény nem támogatja az erőforrás-áthelyezést. Végül az RPs a pontok lejárata után begyűjti a szemetet.
![Név módosítása a házirend módosításakor](./media/backup-during-vm-creation/modify-policy.png)
6. Javasoljuk, hogy ne zárolja a Backup szolgáltatás általi használatra létrehozott erőforráscsoportot.

Ha a PowerShell használatával szeretné konfigurálni a Virtual Machines Azure Backup erőforráscsoportot, tekintse meg [Azure Backup erőforráscsoport létrehozását a pillanatképek megőrzése során](backup-azure-vms-automation.md#creating-azure-backup-resource-group-during-snapshot-retention).

## <a name="start-a-backup-after-creating-the-vm"></a>Biztonsági mentés indítása a virtuális gép létrehozása után

A virtuális gép biztonsági mentése a biztonsági mentési szabályzatnak megfelelően fog futni. Javasoljuk azonban, hogy futtasson egy kezdeti biztonsági mentést.

A virtuális gép létrehozása után tegye a következőket:

1. A virtuális gép tulajdonságainál válassza a **biztonsági mentés** lehetőséget. A virtuális gép állapota kezdeti biztonsági mentés, amíg a kezdeti biztonsági mentés nem fut.
2. Az igény szerinti biztonsági mentés futtatásához kattintson a **biztonsági** Mentés gombra.

    ![Igény szerinti biztonsági mentések futtatása](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Védett virtuális gép üzembe helyezése Resource Manager-sablonnal

Az előző lépések azt ismertetik, hogyan használható a Azure Portal egy virtuális gép létrehozásához és egy Recovery Services-tárolóban való megvédéséhez. Egy vagy több virtuális gép gyors üzembe helyezéséhez és Recovery Services-tárolóban való védeleméhez tekintse meg a [Windows rendszerű virtuális gép üzembe helyezése és a biztonsági mentés engedélyezése](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)című témakört.

## <a name="next-steps"></a>Következő lépések

Most, hogy megvédte a virtuális gépet, megtudhatja, hogyan kezelheti és állíthatja vissza őket.

- [Virtuális gépek kezelése és monitorozása](backup-azure-manage-vms.md)
- [Virtuális gép visszaállítása](backup-azure-arm-restore-vms.md)

Ha bármilyen problémába ütközik, [tekintse át](backup-azure-vms-troubleshoot.md) a hibaelhárítási útmutatót.
