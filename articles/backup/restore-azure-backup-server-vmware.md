---
title: VMware virtuális gépek visszaállítása az Azure Backup Server használatával
description: A VMware vCenter/ESXi-kiszolgálón futó VMware virtuális gépek visszaállításához használja a Azure Backup Server (MABS) szolgáltatást.
ms.topic: conceptual
ms.date: 08/18/2019
ms.openlocfilehash: b3f61aa828db39aeb11b1ce46a850d9a5b868653
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88263520"
---
# <a name="restore-vmware-virtual-machines"></a>VMware virtuális gépek visszaállítása

Ez a cikk bemutatja, hogyan állíthatja vissza a VMware virtuális gépek helyreállítási pontjait a Microsoft Azure Backup Server (MABS) használatával. A MABS az adatok helyreállítására való használatáról a [védett adatok helyreállítása](./backup-azure-alternate-dpm-server.md)című cikkben olvashat bővebben. A MABS felügyeleti konzol kétféleképpen találhat helyreállítható adatkeresést vagy tallózást. Az Adathelyreállítás során előfordulhat, hogy nem kívánja visszaállítani az adathalmazt vagy egy virtuális gépet ugyanarra a helyre. Emiatt a MABS három helyreállítási lehetőséget támogat a VMware virtuális gépek biztonsági másolatainak:

* **Eredeti hely helyreállítása (OLR)** – a védett virtuális gép eredeti helyükre történő visszaállításához használja a OLR-t. A virtuális gépeket csak akkor állíthatja vissza az eredeti helyükre, ha nincs lemez hozzáadva vagy törölve, mert a biztonsági mentés történt. Ha lemezek lettek hozzáadva vagy törölve, akkor másik helyre történő helyreállítást kell használnia.

* **Másik helyre történő helyreállítás (ALR)** – ha az eredeti virtuális gép hiányzik, vagy nem szeretné megzavarni az eredeti virtuális gépet, állítsa helyre a virtuális gépet egy másik helyre. Egy virtuális gép másik helyre történő helyreállításához meg kell adnia egy ESXi-gazdagép, erőforráskészlet, mappa, valamint a tároló adattárát és elérési útját. A visszaállított virtuális gép az eredeti virtuális gépről való megkülönböztetéséhez a MABS a virtuális gép nevéhez hozzáfűzi a "-helyreállítva" elemet.

* **Különálló fájl helye-helyreállítás (ILR)** – ha a védett virtuális gép egy Windows Server rendszerű virtuális gép, a virtuális gépen található egyes fájlok/MAPPÁK a MABS ILR képességével állíthatók helyre. Az egyes fájlok helyreállításához tekintse meg a jelen cikk későbbi részében ismertetett eljárást.

## <a name="restore-a-recovery-point"></a>Helyreállítási pont visszaállítása

1. A MABS felügyeleti konzol válassza a **helyreállítási nézet** lehetőséget.

2. A Tallózás panelen tallózással vagy szűréssel keresse meg a helyreállítani kívánt virtuális gépet. Miután kiválasztott egy virtuális gépet vagy mappát, a helyreállítási pontok ablaktáblán megjelennek a rendelkezésre álló helyreállítási pontok.

    ![Rendelkezésre álló helyreállítási pontok](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. A **helyreállítási pontok mező esetében** a naptár és legördülő menük használatával válassza ki a helyreállítási pont létrehozásának dátumát. A félkövérrel szedett naptári dátumok rendelkeznek elérhető helyreállítási pontokkal.

4. Az eszköz menüszalagján válassza a **helyreállítás** lehetőséget a **helyreállítási varázsló** megnyitásához.

    ![Helyreállítási varázsló, a helyreállítás kiválasztásának áttekintése](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. Kattintson a **Next (tovább** ) gombra a **helyreállítási beállítások megadása** képernyőre való továbblépés előtt.

6. Ha engedélyezni szeretné a hálózati sávszélesség szabályozását a **helyreállítási beállítások megadása** képernyőn, válassza a **módosítás** lehetőséget. Ha le szeretné hagyni a hálózati sávszélesség-szabályozást, válassza a **tovább** lehetőséget. A varázsló ezen képernyőjén nem érhetők el más beállítások a VMware virtuális gépekhez. Ha úgy dönt, hogy módosítja a hálózati sávszélesség szabályozását, a szabályozás párbeszédpanelen válassza a **hálózati sávszélesség-használat szabályozásának engedélyezése** lehetőséget a beállítás bekapcsolásához. Ha engedélyezve van, konfigurálja a **beállításokat** és a **munkatervet**.

7. A **helyreállítási típus kiválasztása** képernyőn válassza ki, hogy az eredeti példányra kívánja-e visszaállítani, vagy egy új helyre. Ezután kattintson a **Tovább** gombra.

     * Ha a **helyreállítás az eredeti példányra** lehetőséget választja, nem kell további döntéseket hoznia a varázslóban. A rendszer az eredeti példányra vonatkozó adatgyűjtést használja.

     * Ha **bármelyik gazdagépen a helyreállítás virtuális géphez** lehetőséget választja, akkor a **célhely megadása** képernyőn adja meg az **ESXi-gazdagép, az erőforráskészlet, a mappa** és az **elérési út** adatait.

      ![Helyreállítási típus kiválasztása](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. Az **Összefoglalás** képernyőn tekintse át a beállításokat, majd **kattintson a helyreállítás** elemre a helyreállítási folyamat elindításához. A **helyreállítás állapota** képernyőn a helyreállítási művelet előrehaladása látható.

## <a name="restore-an-individual-file-from-a-vm"></a>Egyedi fájl visszaállítása virtuális gépről

A védett virtuális gépek helyreállítási pontjairól is visszaállíthatja az egyes fájlokat. Ez a funkció csak Windows Server rendszerű virtuális gépek esetén érhető el. Az egyes fájlok visszaállítása hasonló a teljes virtuális gép visszaállításához, kivéve, ha a VMDK navigál, és a kívánt fájl (oka) t keresi a helyreállítási folyamat elindítása előtt. Egyéni fájl helyreállítása vagy Windows Server rendszerű virtuális gépről származó fájlok kiválasztása:

>[!NOTE]
>Egy különálló fájl virtuális gépről való visszaállítása csak a Windows rendszerű virtuális gépek és a lemezes helyreállítási pontok esetében érhető el.

1. A MABS felügyeleti konzol válassza a **helyreállítási** nézet lehetőséget.

2. A **Tallózás** panelen tallózással vagy szűréssel keresse meg a helyreállítani kívánt virtuális gépet. Miután kiválasztott egy virtuális gépet vagy mappát, a **helyreállítási pontok ablaktáblán** megjelennek a rendelkezésre álló helyreállítási pontok.

    !["Helyreállítási pontok a következőhöz" panel](./media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

3. A **helyreállítási pontok:** ablaktáblán a naptár használatával válassza ki a kívánt helyreállítási pont (ok) t tartalmazó dátumot. A biztonsági mentési házirend konfigurálásának módjától függően a dátumok több helyreállítási ponttal is rendelkezhetnek. Miután kiválasztotta a helyreállítási pont készítésének napját, győződjön meg arról, hogy a megfelelő **helyreállítási időt** választotta. Ha a kiválasztott dátum több helyreállítási ponttal rendelkezik, válassza ki a helyreállítási pontot a helyreállítási idő legördülő menüben. Miután kiválasztotta a helyreállítási pontot, megjelenik a helyreállítható elemek listája az **elérési út:** ablaktáblán.

4. A visszaállítani kívánt fájlok megkereséséhez az **elérési út** ablaktáblán kattintson duplán a helyreállítható **elem** oszlopban található elemre a megnyitásához. Válassza ki a helyreállítani kívánt fájlt, fájlokat vagy mappákat. Több elem kiválasztásához nyomja le a **CTRL** billentyűt az egyes elemek kiválasztásakor. Az **elérési út** ablaktáblán keresse meg a **helyreállítható elem** oszlopban megjelenő fájlok vagy mappák listáját. Az **alábbi keresési lista** nem keres almappákban. Az almappákban való kereséshez kattintson duplán a mappára. A **fel** gomb használatával lépjen át egy alárendelt mappából a szülőmappa mappájába. Több elemet is kijelölhet (fájlok és mappák), de ugyanabban a szülő mappában kell lenniük. Ugyanabban a helyreállítási feladatokban nem állíthatók helyre több mappából származó elemek.

    ![Visszaállítási kijelölés áttekintése](./media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

5. Miután kiválasztotta az elem (eke) t a helyreállításhoz, a felügyeleti konzol eszköz menüszalagján válassza **a helyreállítás elemet** a **helyreállítási varázsló** megnyitásához. A helyreállítási varázslóban a helyreállítási beállítások **áttekintése** képernyő megjeleníti a helyreállítandó kijelölt elemeket.

6. Ha engedélyezni szeretné a hálózati sávszélesség szabályozását a **helyreállítási beállítások megadása** képernyőn, válassza a **módosítás** lehetőséget. Ha le szeretné hagyni a hálózati sávszélesség-szabályozást, válassza a **tovább** lehetőséget. A varázsló ezen képernyőjén nem érhetők el más beállítások a VMware virtuális gépekhez. Ha úgy dönt, hogy módosítja a hálózati sávszélesség szabályozását, a szabályozás párbeszédpanelen válassza a **hálózati sávszélesség-használat szabályozásának engedélyezése** lehetőséget a beállítás bekapcsolásához. Ha engedélyezve van, konfigurálja a **beállításokat** és a **munkatervet**.
7. A **helyreállítási típus kiválasztása** képernyőn válassza a **tovább** lehetőséget. A fájl (ok) vagy mappa (ok) csak a hálózati mappába állíthatók helyre.
8. A **célhely megadása** képernyőn válassza a **Tallózás** lehetőséget a fájlok vagy mappák hálózati helyének megkereséséhez. A MABS létrehoz egy mappát, ahol az összes helyreállított elem másolása történik. A Mappanév az előtaggal, MABS_day-hónaptal rendelkezik. Ha kijelöl egy helyet a helyreállított fájlokhoz vagy mappához, a rendszer megadja a hely részleteit (a célhely, a célhely elérési útja és a rendelkezésre álló terület).

    ![A fájlok visszaállítási helyének meghatározása](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. A **helyreállítási beállítások megadása** képernyőn válassza ki, hogy melyik biztonsági beállítást kívánja alkalmazni. Dönthet úgy, hogy módosítja a hálózati sávszélesség-használat szabályozását, de alapértelmezés szerint le van tiltva a szabályozás. Továbbá a **Tárolóhálózati helyreállítás** és az **értesítés** nincs engedélyezve.
10. Az **Összefoglalás** képernyőn tekintse át a beállításokat, majd **kattintson a helyreállítás** elemre a helyreállítási folyamat elindításához. A **helyreállítás állapota** képernyőn a helyreállítási művelet előrehaladása látható.

## <a name="next-steps"></a>Következő lépések

A Azure Backup Server használata során felmerülő problémák elhárításához tekintse át [Azure Backup Server hibaelhárítási útmutatóját](./backup-azure-mabs-troubleshoot.md).
