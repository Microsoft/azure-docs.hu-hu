---
title: Az Azure-Managed Disks
description: Megtudhatja, hogyan állíthatók Managed Disks Azure-Azure Portal.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: c57d60047a5bcef58c721ee25bd8a0b3ed523aa4
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517198"
---
# <a name="restore-azure-managed-disks"></a>Az Azure-Managed Disks

Ez a cikk azt ismerteti, hogyan állíthatók [Managed Disks](../virtual-machines/managed-disks-overview.md) Azure-beli virtuális Azure Backup.

Jelenleg a Original-Location (OLR) lehetőség nem támogatott a visszaállításhoz a meglévő forráslemez cseréjével, ahonnan a biztonsági másolatok készültek. Helyreállítási pontról visszaállítással új lemezt hozhat létre ugyanazon az erőforráscsoporton belül, mint annak a forráslemeznek a lemezét, amelyről a biztonsági másolatok készültek, vagy bármely más erőforráscsoportban. Ez az úgynevezett Alternate-Location Recovery (ALR), és így a forráslemez és a visszaállított (új) lemez is megtartható.

Ebből a cikkből megtudhatja, hogyan:

- Visszaállítás új lemez létrehozásához

- A visszaállítási művelet állapotának nyomon követése

## <a name="restore-to-create-a-new-disk"></a>Visszaállítás új lemez létrehozásához

A Backup Vault felügyelt identitással fér hozzá más Azure-erőforrásokhoz. A biztonsági másolatból való visszaállításhoz a Backup-tároló felügyelt identitásának engedélykészletre van szüksége arra az erőforráscsoportra, ahol a lemezt vissza kívánja állítani.

A Backup-tároló egy rendszer által hozzárendelt felügyelt identitást használ, amely erőforrásonként egy-egyre van korlátozva, és az erőforrás életciklusához van kötve. A felügyelt identitáshoz az Azure szerepköralapú hozzáférés-vezérlésével (Azure RBAC) adhat engedélyeket. A felügyelt identitás egy speciális típusú szolgáltatásnév, amely csak Azure-erőforrásokkal használható. További információ a [felügyelt identitásokkal kapcsolatban:](../active-directory/managed-identities-azure-resources/overview.md).

A visszaállítási művelet végrehajtásához a következő előfeltételek szükségesek:

1. Rendelje hozzá **a Lemez-visszaállítási** operátor szerepkört a Backup-tároló felügyelt identitásához abban az erőforráscsoportban, amelyben a lemez a Azure Backup vissza.

    >[!NOTE]
    > Kiválaszthatja ugyanazt az erőforráscsoportot, mint a forráslemez, amelyről a biztonsági másolatokat készíti, vagy bármely más erőforráscsoportba ugyanazon vagy egy másik előfizetésen belül.

    1. Ugrás arra az erőforráscsoportra, amelyben a lemezt vissza kell állítani. Az erőforráscsoport például *TargetRG.*

    1. A **Hozzáférés-vezérlés (IAM) alatt válassza** a **Szerepkör-hozzárendelések hozzáadása lehetőséget.**

    1. A jobb oldali helyi panelen válassza a **Lemez-visszaállítási operátor** lehetőséget a **Szerepkör** legördülő listában. Válassza ki a Backup-tároló felügyelt identitását, és mentse **a következőt:**.

        >[!TIP]
        >Írja be a backup-tároló nevét a tároló felügyelt identitásának kiválasztásához.

        ![Lemez-visszaállítási operátori szerepkör kiválasztása](./media/restore-managed-disks/disk-restore-operator-role.png)

1. Ellenőrizze, hogy a Backup-tároló felügyelt identitása rendelkezik-e a megfelelő szerepkör-hozzárendelésekkel abban az erőforráscsoportban, amelyben a lemez vissza lesz állítva.

    1. Ugrás a **Backup-tárolóra – > identitás,** majd az **Azure-beli szerepkör-hozzárendelések kiválasztása**

        ![Azure-beli szerepkör-hozzárendelések kiválasztása](./media/restore-managed-disks/azure-role-assignments.png)

    1. Ellenőrizze, hogy a szerepkör, az erőforrás neve és az erőforrástípus megfelelően jelenik-e meg.

        ![Szerepkör, erőforrásnév és erőforrástípus ellenőrzése](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >Bár a szerepkör-hozzárendelések megfelelően megjelennek a portálon, az engedély a Backup-tároló felügyelt identitására való alkalmazása körülbelül 15 percet is igénybe vehet.
    >
    >Ütemezett biztonsági mentések vagy igény szerinti biztonsági mentési művelet során a Azure Backup a lemez növekményes pillanatképét a lemez biztonsági mentésének konfigurálása során megadott pillanatkép-erőforráscsoportban tárolja. Azure Backup a visszaállítási művelet során ezeket a növekményes pillanatképeket használja. Ha a pillanatképeket törlik vagy áthelyezik a Pillanatkép erőforráscsoportból, vagy ha a Backup-tároló szerepkör-hozzárendelései vissza vannak vonva a pillanatkép-erőforráscsoporton, a visszaállítási művelet sikertelen lesz.

1. Ha a visszaállítani szükséges lemez ügyfél által kezelt kulcsokkal [(CMK)](../virtual-machines/disks-enable-customer-managed-keys-portal.md) van titkosítva, vagy platform által  felügyelt és felhasználó által kezelt kulcsokkal dupla titkosítást [használ,](../virtual-machines/disks-enable-double-encryption-at-rest-portal.md)akkor rendeljen olvasói szerepkör-engedélyt a Backup-tároló felügyelt identitásához a Disk **Encryption Set** erőforráson.

Ha az előfeltételek teljesülnek, kövesse az alábbi lépéseket a visszaállítási művelet végrehajtásához.

1. A [Azure Portal](https://portal.azure.com/)a **Biztonsági mentési központba.** A **Kezelés szakaszban** válassza a Biztonságimásolat-példányok lehetőséget.  A biztonságimásolat-példányok listájából válassza ki azt a lemezes biztonsági mentési példányt, amelynek a visszaállítási műveletét el szeretné végezni.

    ![Biztonságimásolat-példányok listája](./media/restore-managed-disks/backup-instances.png)

    Ezt a műveletet a lemez biztonsági mentésének konfiguráláshoz használt Backup-tárolóból is elvégezheti.

1. A **Biztonságimásolat-példány** képernyőn válassza ki a visszaállítási művelet végrehajtásához használni kívánt visszaállítási pontot, majd válassza a **Visszaállítás lehetőséget.**

    ![Visszaállítási pont kiválasztása](./media/restore-managed-disks/select-restore-point.png)

1. A Visszaállítás **munkafolyamatban** tekintse át az **Alapvető** beállítások és a **Helyreállítási pont** kiválasztása lap információit, majd válassza a **Tovább: Paraméterek visszaállítása lehetőséget.**

    ![Az alapok áttekintése és A helyreállítási pont információinak kiválasztása](./media/restore-managed-disks/review-information.png)

1. A **Visszaállítási paraméterek lapon** válassza  ki a Cél-előfizetést és a Cél erőforráscsoportot, ahová vissza szeretné állítani a biztonsági másolatot.  Adja meg a visszaállítani szükséges lemez nevét. Válassza **a Tovább: Áttekintés és visszaállítás lehetőséget.**

    ![Paraméterek visszaállítása](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >Az azure-beli virtuális gépek biztonsági Azure Backup a Lemezes biztonsági mentési megoldással biztonsági mentést készít a Azure Backup az Azure-beli virtuális gépek helyreállítási tárával való biztonsági mentésére is. Ha konfigurálta annak az Azure-beli virtuális gépnek a védelmét, amelyhez ez a lemez csatolva van, az Azure-beli virtuális gép visszaállítási műveletét is használhatja. Dönthet úgy, hogy visszaállítja a virtuális gépet, vagy a lemezeket és fájlokat vagy mappákat a megfelelő Azure-beli virtuális gép biztonságimásolat-példányának helyreállítási pontjáról. További információ: [Azure-beli virtuális gépek biztonsági mentése.](./about-azure-vm-restore.md)

1. Ha az ellenőrzés sikeres volt, válassza a **Visszaállítás lehetőséget** a visszaállítási művelet elkezdéshez.

    ![Visszaállítási művelet kezdeményezése](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > A visszaállítási művelet aktiválása eltarthat néhány percig. Az érvényesítés meghiúsulhat, ha:
    >
    > - a Visszaállított lemez neve  alatt megadott nevű lemez már létezik a Cél **erőforráscsoportban**
    > - a Backup-tároló felügyelt identitása nem tartalmaz érvényes szerepkör-hozzárendeléseket a **Cél erőforráscsoporton**
    > - a Backup-tároló felügyelt identitás szerepkör-hozzárendelései  vissza vannak vonva a pillanatkép-erőforráscsoporton, ahol a növekményes pillanatképek vannak tárolva
    > - Ha a növekményes pillanatképeket törlik vagy áthelyezik a pillanatkép-erőforráscsoportból

A Visszaállítás új lemezt hoz létre a helyreállítási művelet során megadott célerőforrás-csoport kiválasztott helyreállítási pontjából. A visszaállított lemez meglévő virtuális gépen való használathoz további lépéseket kell végrehajtania:

- Ha a visszaállított lemez adatlemez, meglévő lemezt csatolhat egy virtuális géphez. Ha a visszaállított lemez operációsrendszer-lemez, felcserélheti egy virtuális gép operációsrendszer-lemezét a Azure Portal  virtuális gép panelén a Virtuális gép panel – > Lemezek menüben a **Beállítások szakaszban.** 

    ![Operációsrendszer-lemezek felcserélve](./media/restore-managed-disks/swap-os-disks.png)

- Windows rendszerű virtuális gépek esetén, ha a visszaállított lemez [](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-the-portal) adatlemez, az utasításokat követve válassza le az eredeti adatlemezt a virtuális gépről. Ezután [csatolja a visszaállított lemezt](../virtual-machines/windows/attach-managed-disk-portal.md) a virtuális géphez. Az utasításokat követve [cserélje](../virtual-machines/windows/os-disk-swap.md) le a virtuális gép operációsrendszer-lemezét a visszaállított lemezre.

- Linux rendszerű virtuális gépek esetén, ha a visszaállított lemez [](../virtual-machines/linux/detach-disk.md#detach-a-data-disk-using-the-portal) adatlemez, az utasításokat követve válassza le az eredeti adatlemezt a virtuális gépről. Ezután [csatolja a visszaállított lemezt](../virtual-machines/linux/attach-disk-portal.md#attach-an-existing-disk) a virtuális géphez. Az utasításokat követve [cserélje](../virtual-machines/linux/os-disk-swap.md) le a virtuális gép operációsrendszer-lemezét a visszaállított lemezre.

Javasoljuk, hogy a  visszaállítási művelet sikeres befejezése után vonja vissza  a Lemez-visszaállítási operátor szerepkör-hozzárendelést a Backup-tároló felügyelt identitásában a Cél erőforráscsoportban.

## <a name="track-a-restore-operation"></a>Visszaállítási művelet nyomon követése

A visszaállítási művelet aktiválása után a biztonsági mentési szolgáltatás létrehoz egy követési feladatot. Azure Backup megjeleníti a feladatról szóló értesítéseket a portálon. A visszaállítási feladat előrehaladásának megtekintése:

1. Ugrás a **Biztonságimásolat-példány képernyőre.** Megjeleníti a feladatok irányítópultját az elmúlt hét napra vonatkozó működéssel és állapottal.

    ![Feladatok irányítópult](./media/restore-managed-disks/jobs-dashboard.png)

1. A visszaállítási művelet állapotának  megtekintéséhez válassza a Az összes megtekintése lehetőséget a biztonságimásolat-példány folyamatban lévő és korábbi feladatának megtekintéséhez.

    ![Válassza a Az összes megtekintése](./media/restore-managed-disks/view-all.png)

1. Tekintse át a biztonsági mentési és visszaállítási feladatok listáját és azok állapotát. Válasszon ki egy feladatot a feladatok listájából a feladat részleteinek megtekintéséhez.

    ![Feladatok listája](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>Következő lépések

- [Azure Disk Backup – gyakori kérdések](disk-backup-faq.yml)