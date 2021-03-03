---
title: Azure-Managed Disks visszaállítása
description: Ismerje meg, hogyan állíthatja vissza az Azure Managed Diskst a Azure Portalból.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 995217cd17d1e2a16cd7a5f963ee88aa7116d4a7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703749"
---
# <a name="restore-azure-managed-disks-in-preview"></a>Az Azure Managed Disks visszaállítása (előzetes verzió)

>[!IMPORTANT]
>Az Azure Disk Backup előzetes verzióban érhető el, és nem ajánlott éles környezetben üzemelő számítási feladatokhoz. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A régió elérhetőségét a [támogatási mátrixban](disk-backup-support-matrix.md)tekintheti meg.
>
>[Töltse ki ezt a kérdőívet](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) az előzetes verzióra való feliratkozáshoz.

Ez a cikk azt ismerteti, hogyan állíthatja vissza az [Azure Managed Diskst](../virtual-machines/managed-disks-overview.md) egy Azure Backup által létrehozott visszaállítási pontról.

Jelenleg a visszaállítási Original-Location helyreállítási (OLR) lehetőség a meglévő forrás lemezének lecserélésekor, ahonnan a biztonsági másolatok készültek, nem támogatottak. A helyreállítási pontról visszaállíthatók úgy, hogy egy új lemezt hozzanak létre ugyanabba az erőforráscsoporthoz, amelyből a biztonsági mentéseket vagy más erőforráscsoportot is létrehozták. Ez a Alternate-Location Recovery (ALR) néven ismert, amely segít megőrizni a forrás-és a visszaállított (új) lemezt is.

Ebből a cikkből megtudhatja, hogyan:

- Visszaállítás új lemez létrehozásához

- A visszaállítási művelet állapotának nyomon követése

## <a name="restore-to-create-a-new-disk"></a>Visszaállítás új lemez létrehozásához

A Backup-tároló felügyelt identitás használatával fér hozzá más Azure-erőforrásokhoz. A biztonsági másolatból történő visszaállításhoz a Backup-tár felügyelt identitásához meg kell adni azon erőforráscsoport engedélyeit, amelyben a lemezt vissza kell állítani.

A Backup-tároló egy rendszerhez rendelt felügyelt identitást használ, amely egy erőforrásra korlátozódik, és az erőforrás életciklusához van kötve. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával engedélyeket adhat a felügyelt identitásnak. A felügyelt identitás olyan speciális típusú szolgáltatásnév, amelyet csak az Azure-erőforrásokkal lehet használni. További információ a [felügyelt identitásokról](../active-directory/managed-identities-azure-resources/overview.md).

A visszaállítási művelet végrehajtásához a következő előfeltételek szükségesek:

1. Rendelje hozzá a **lemez-visszaállítási operátori** szerepkört a biztonságimásolat-tároló felügyelt identitásához az erőforráscsoporthoz, ahol a Azure Backup szolgáltatás visszaállítja a lemezt.

    >[!NOTE]
    > Megadhatja ugyanazt az erőforráscsoportot, mint annak a forrásnak a lemezét, amelyről biztonsági mentés készül, vagy bármely más, azonos vagy eltérő előfizetésben található erőforráscsoporthoz.

    1. Keresse meg azt az erőforráscsoportot, amelyben a lemezt vissza kívánja állítani. Az erőforráscsoport például a *TargetRG*.

    1. Nyissa meg a **hozzáférés-vezérlés (iam)** lehetőséget, és válassza a **szerepkör-hozzárendelések hozzáadása** elemet.

    1. A jobb oldali kontextus ablaktáblán válassza a **lemez visszaállítása operátor** elemet a **szerepkör** legördülő listában. Válassza ki a biztonságimásolat-tároló felügyelt identitását, és **mentse**.

        >[!TIP]
        >A tár felügyelt identitásának kiválasztásához írja be a Backup-tároló nevét.

        ![Lemez-visszaállítási operátori szerepkör kiválasztása](./media/restore-managed-disks/disk-restore-operator-role.png)

1. Ellenőrizze, hogy a biztonságimásolat-tároló felügyelt identitása rendelkezik-e a megfelelő szerepkör-hozzárendelésekkel azon az erőforráscsoporton, amelyben a lemez vissza lesz állítva.

    1. Lépjen a **Backup-tároló – > identitás** elemre, és válassza az **Azure-szerepkör-hozzárendelések** lehetőséget.

        ![Azure-beli szerepkör-hozzárendelések kiválasztása](./media/restore-managed-disks/azure-role-assignments.png)

    1. Győződjön meg arról, hogy a szerepkör, az erőforrás neve és az erőforrástípus helyesen jelenik meg.

        ![Szerepkör, erőforrás neve és erőforrástípus ellenőrzése](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >Noha a szerepkör-hozzárendelések helyesen jelennek meg a portálon, körülbelül 15 percet vesz igénybe, hogy az engedély a biztonsági mentési tár felügyelt identitására legyen alkalmazva.
    >
    >Ütemezett biztonsági mentések vagy igény szerinti biztonsági mentési művelet során a Azure Backup a lemez biztonsági mentésének konfigurálása során megadott pillanatkép-erőforráscsoport lemezes növekményes pillanatképeit tárolja. A Azure Backup ezeket a növekményes pillanatképeket használja a visszaállítási művelet során. Ha a pillanatképeket törli vagy áthelyezi a pillanatkép-erőforráscsoporthoz, vagy ha a biztonságimásolat-tároló szerepkör-hozzárendeléseit visszavonják a pillanatkép-erőforráscsoport esetében, a visszaállítási művelet sikertelen lesz.

1. Ha a visszaállítani kívánt lemez az [ügyfél által felügyelt kulcsokkal (CMK)](../virtual-machines/disks-enable-customer-managed-keys-portal.md) van titkosítva, vagy ha a [platform által felügyelt kulcsokkal és az ügyfél által felügyelt](../virtual-machines/disks-enable-double-encryption-at-rest-portal.md)kulcsokkal használja a kettős titkosítást, akkor a **lemez titkosítási készletének** erőforrásában rendelje hozzá az **olvasói** szerepkört a biztonságimásolat-tároló felügyelt identitásához.

Ha az előfeltételek teljesülnek, kövesse az alábbi lépéseket a visszaállítási művelet végrehajtásához.

1. A [Azure Portal](https://portal.azure.com/)nyissa meg a **Backup centert**. A **kezelés** szakaszban válassza a **biztonsági mentési példányok** lehetőséget. A biztonsági mentési példányok listájából válassza ki azt a lemezes biztonsági mentési példányt, amelynek a visszaállítási műveletét el szeretné végezni.

    ![Biztonsági mentési példányok listája](./media/restore-managed-disks/backup-instances.png)

    Ezt a műveletet a lemez biztonsági mentésének konfigurálásához használt Backup-tárolóból is végrehajthatja.

1. A **biztonsági mentési példány** képernyőn válassza ki azt a visszaállítási pontot, amelyet a visszaállítási művelet végrehajtásához használni kíván, majd válassza a **visszaállítás** lehetőséget.

    ![Visszaállítási pont kiválasztása](./media/restore-managed-disks/select-restore-point.png)

1. A **visszaállítási** munkafolyamatban tekintse át az **alapismereteket** , és **válassza a helyreállítási pont** lap információit, és válassza a **Tovább: paraméterek visszaállítása** lehetőséget.

    ![Alapismeretek áttekintése és a helyreállítási pontok adatainak kiválasztása](./media/restore-managed-disks/review-information.png)

1. A **Restore parameters (paraméterek visszaállítása** ) lapon válassza ki azt a **cél-előfizetést** és **célként szolgáló erőforráscsoportot** , amelyben vissza kívánja állítani a biztonsági mentést. Adja meg a visszaállítani kívánt lemez nevét. Válassza a **Tovább: felülvizsgálat + visszaállítás** lehetőséget.

    ![Visszaállítási paraméterek](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >A lemezes biztonsági mentési megoldással biztonsági mentést Azure Backup lemezeket a Azure Backup az Azure virtuális gép biztonsági mentési megoldásával is biztonsági mentést készíthet a Recovery Services-tárolóval. Ha úgy konfigurálta az Azure-beli virtuális gép védelmét, amelyhez ez a lemez csatlakoztatva van, akkor az Azure virtuális gép visszaállítási műveletét is használhatja. Dönthet úgy, hogy visszaállítja a virtuális gépet, a lemezeket és a fájlokat vagy mappákat a megfelelő Azure-beli virtuális gép biztonsági mentési példányának helyreállítási pontjából. További információ: Azure-beli [virtuális gépek biztonsági mentése](./about-azure-vm-restore.md).

1. Ha az ellenőrzés sikeres, válassza a **visszaállítás** lehetőséget a visszaállítási művelet elindításához.

    ![Visszaállítási művelet kezdeményezése](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > A visszaállítási művelet elindításához néhány percet is igénybe vehet. Az érvényesítés sikertelen lehet, ha:
    >
    > - már létezik egy azonos nevű lemez a **visszaállított lemez nevében** a célként megadott **erőforráscsoporthoz**
    > - a biztonságimásolat-tároló felügyelt identitása nem rendelkezik érvényes szerepkör-hozzárendelésekkel a **célként megadott erőforráscsoporthoz**
    > - a biztonsági mentési tár felügyelt identitási szerepkörének hozzárendelései visszavonva lesznek a **Pillanatkép-erőforráscsoport** esetében, ahol a növekményes Pillanatképek tárolódnak
    > - Ha növekményes pillanatképeket töröl vagy áthelyez a pillanatkép-erőforráscsoporthoz

A Restore parancs egy új lemezt hoz létre a kijelölt helyreállítási pontból a visszaállítási művelet során megadott cél erőforráscsoport alapján. Ha egy meglévő virtuális gépen szeretné használni a visszaállított lemezt, további lépéseket kell végrehajtania:

- Ha a visszaállított lemez adatlemez, akkor a meglévő lemezt csatlakoztathatja egy virtuális géphez. Ha a visszaállított lemez operációsrendszer-lemez, akkor a virtuális gép operációsrendszer-lemezét a **Beállítások** szakaszban található **virtuális gép** ablaktábla-> **lemezek** menüjéből cserélheti le a Azure Portal.

    ![OPERÁCIÓSRENDSZER-lemezek cseréje](./media/restore-managed-disks/swap-os-disks.png)

- Windows rendszerű virtuális gépek esetén, ha a visszaállított lemez adatlemez, kövesse az utasításokat az [eredeti adatlemez leválasztásához](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-the-portal) a virtuális gépről. Ezután [csatlakoztassa a visszaállított lemezt](../virtual-machines/windows/attach-managed-disk-portal.md) a virtuális géphez. Az utasításokat követve cserélje le a virtuális gép [operációsrendszer-lemezét](../virtual-machines/windows/os-disk-swap.md) a visszaállított lemezre.

- Linux rendszerű virtuális gépek esetén, ha a visszaállított lemez adatlemez, kövesse az utasításokat az [eredeti adatlemez leválasztásához](../virtual-machines/linux/detach-disk.md#detach-a-data-disk-using-the-portal) a virtuális gépről. Ezután [csatlakoztassa a visszaállított lemezt](../virtual-machines/linux/attach-disk-portal.md#attach-an-existing-disk) a virtuális géphez. Az utasításokat követve cserélje le a virtuális gép [operációsrendszer-lemezét](../virtual-machines/linux/os-disk-swap.md) a visszaállított lemezre.

Javasoljuk, hogy a visszaállítási művelet sikeres befejezése után visszavonja a **lemez-visszaállítási operátor** szerepkör-hozzárendelést a biztonságimásolat-tároló felügyelt identitásáról a **cél erőforráscsoporthoz** .

## <a name="track-a-restore-operation"></a>Visszaállítási művelet nyomon követése

A visszaállítási művelet elindítását követően a Backup szolgáltatás létrehoz egy feladatot a követéshez. A Azure Backup megjeleníti a feladatokkal kapcsolatos értesítéseket a portálon. A visszaállítási feladatok előrehaladásának megtekintése:

1. Lépjen a **biztonsági mentési példány** képernyőjére. Megjeleníti a feladatok irányítópultot az elmúlt hét nap műveletével és állapotával.

    ![Feladatok irányítópult](./media/restore-managed-disks/jobs-dashboard.png)

1. A visszaállítási művelet állapotának megtekintéséhez válassza az **összes megtekintése** lehetőséget a biztonsági mentési példány aktuális és korábbi feladatainak megjelenítéséhez.

    ![Válassza az összes megtekintése lehetőséget](./media/restore-managed-disks/view-all.png)

1. Tekintse át a biztonsági mentési és visszaállítási feladatok listáját és azok állapotát. Válasszon feladatot a feladatok listájáról a feladat részleteinek megtekintéséhez.

    ![Feladatok listája](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>Következő lépések

- [Azure Disk Backup – gyakori kérdések](disk-backup-faq.md)