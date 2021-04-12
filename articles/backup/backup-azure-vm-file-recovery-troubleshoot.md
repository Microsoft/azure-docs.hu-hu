---
title: Azure-beli virtuális gép fájljának helyreállítása – problémamegoldás
description: A fájlok és mappák Azure-beli virtuális gépekről történő helyreállításakor felmerülő problémák elhárítása.
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 0ee856b10c6a5fbea6f6f76b2082949ab9c1e0db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700303"
---
# <a name="troubleshoot-issues-in-file-recovery-of-an-azure-vm-backup"></a>Az Azure-beli virtuális gépek biztonsági mentésének fájl-helyreállításával kapcsolatos problémák elhárítása

Ez a cikk hibaelhárítási lépéseket tartalmaz, amelyek segítségével megoldhatja a fájlok és mappák Azure-beli virtuális gépekről történő biztonsági másolatból való helyreállításával kapcsolatos problémákat.

## <a name="common-error-messages"></a>Gyakori hibaüzenetek

Ez a szakasz az esetlegesen megjelenő hibaüzenetek hibaelhárításának lépéseit ismerteti.

### <a name="exception-caught-while-connecting-to-target"></a>"Kivétel történt a célhoz való csatlakozáskor"

**Lehetséges ok**: a parancsfájl nem fér hozzá a helyreállítási ponthoz.

**Javasolt művelet**: a probléma megoldásához kövesse a [parancsfájl futtatásának lépéseit, de a kapcsolódás sikertelen volt](#the-script-runs-but-the-connection-to-the-iscsi-target-failed).

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>"A cél már be van jelentkezve iSCSI-munkameneten keresztül"

**Lehetséges ok**: a parancsfájl már futott ugyanazon a gépen, és a meghajtók csatlakoztatva lettek.

**Javasolt művelet**: a helyreállítási pont kötetei már csatolva vannak. Nem csatlakoztathatók az eredeti virtuális gép ugyanazzal a meghajtóbetűjelével. Böngésszen a Fájlkezelőben elérhető kötetek között.

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>"Ez a parancsfájl érvénytelen, mert a lemezek a portálon keresztül lettek leválasztva, vagy túllépte a 12 HR-korlátot. Új szkript letöltése a portálról "

**Lehetséges ok**: a lemezek leválasztása a portálról megtörtént, vagy a 12 órás időkorlát túllépve.

**Javasolt művelet**: a parancsfájl letöltése után 12 órával a rendszer érvénytelenné válik, és nem futtatható. Lépjen a portálra, és töltsön le egy új parancsfájlt a fájl-helyreállítás folytatásához.

### <a name="iscsi_tcp-module-cant-be-loaded-or-iscsi_tcp_module-not-found"></a>Az iscsi_tcp modult nem lehet betölteni (vagy) az iscsi_tcp modul nem található

**Javasolt művelet**: a probléma megoldásához kövesse a [szkriptek letöltésének](#the-script-downloads-successfully-but-fails-to-run)lépéseit, de a Futtatás nem sikerült.

## <a name="common-problems"></a>Gyakori problémák

Ez a szakasz a fájlok helyreállítására szolgáló parancsfájl letöltése és végrehajtása során felmerülő gyakori problémák elhárításának lépéseit ismerteti.

### <a name="you-cant-download-the-script"></a>A parancsfájl nem tölthető le

1. Győződjön meg arról, [hogy rendelkezik a parancsfájl letöltéséhez szükséges engedélyekkel](./backup-azure-restore-files-from-vm.md#select-recovery-point-who-can-generate-script).
1. Ellenőrizze az Azure cél IP-címeihez való kapcsolódást. Futtassa a következő parancsok egyikét egy emelt szintű parancssorból:

   `nslookup download.microsoft.com`

    vagy

    `ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>A parancsfájl letöltése sikeresen megtörtént, de a futtatása sikertelen

Ha az elemszintű helyreállítás (ILR) Python-szkriptjét SUSE Linux Enterprise Server 12 SP4 rendszeren futtatja, akkor az "iscsi_tcp modul nem tölthető be" vagy "iscsi_tcp_module nem található" hibaüzenettel meghiúsul.

**Lehetséges ok**: a ILR modul a **iscsi_tcp** használatával hoz létre TCP-kapcsolatot a Backup szolgáltatással. A SLES 12 SP4 kiadás részeként a SUSE eltávolította **iscsi_tcpt** a nyílt iSCSI-csomagból, így a ILR művelet meghiúsul.

**Javasolt művelet**: a fájl-helyreállítási parancsfájlok végrehajtása nem támogatott a SUSE 12 SP4 rendszerű virtuális gépeken. Próbálja megismételni a visszaállítási műveletet a SUSE 12 SP4 régebbi verzióján.

### <a name="the-script-runs-but-the-connection-to-the-iscsi-target-failed"></a>A parancsfájl fut, de az iSCSI-tárolóhoz való kapcsolódás sikertelen volt.

Előfordulhat, hogy egy "kivétel történt a célhoz való csatlakozáskor" hibaüzenet jelenik meg.

1. Ellenőrizze, hogy a parancsfájlt futtató gép megfelel-e a [hozzáférési követelményeknek](./backup-azure-restore-files-from-vm.md#step-4-access-requirements-to-successfully-run-the-script).
1. Ellenőrizze az Azure cél IP-címeihez való kapcsolódást. Futtassa a következő parancsok egyikét egy emelt szintű parancssorból:

   `nslookup download.microsoft.com`

   vagy

   `ping download.microsoft.com`
1. Győződjön meg a 3260-es iSCSI kimenő port eléréséről.
1. Keressen tűzfalat vagy NSG az Azure cél IP-címeire vagy a helyreállítási szolgáltatás URL-címére irányuló adatforgalom blokkolására.
1. Győződjön meg arról, hogy a víruskereső szoftver nem blokkolja a parancsfájl végrehajtását.

### <a name="youre-connected-to-the-recovery-point-but-the-disks-werent-attached"></a>Csatlakozik a helyreállítási ponthoz, de a lemezek nem voltak csatlakoztatva

A probléma megoldásához kövesse az operációs rendszerének lépéseit.

#### <a name="windows-file-recovery-fails-on-server-with-storage-pools"></a>A Windows-fájl helyreállítása sikertelen a Storage-készletekkel rendelkező kiszolgálón

Amikor első alkalommal futtatja a parancsfájlt a Windows Server 2012 R2 és a Windows Server 2016 rendszeren (a Storage-készletekkel), előfordulhat, hogy a rendszer csak olvashatóként csatlakoztatja a tárolót a virtuális géphez.

>[!Tip]
> Győződjön meg arról, [hogy rendelkezik a megfelelő géppel a szkript futtatásához](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

A probléma megoldásához kézzel rendeljen hozzá írási és olvasási hozzáférést a tárolóhoz, és csatolja a virtuális lemezeket:

1. Nyissa meg a **Kiszolgálókezelő**  >  **fájl-és tárolási szolgáltatások**  >  **kötetek**  >  **tárolási készleteit**.

   ![A tárolási készletek beállításait ábrázoló képernyőfelvétel](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. A **tárolási készlet** ablakban kattintson a jobb gombbal a rendelkezésre álló tárolóra, majd válassza a **Read-Write hozzáférés beállítása** lehetőséget.

   ![Képernyőfelvétel: a jobb gombbal kattintva a tárolási várólista beállításai láthatók.](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. Ha a tárolóhoz írási-olvasási hozzáférés van rendelve, kattintson a jobb gombbal a **virtuális lemezek** szakaszra, majd válassza a **virtuális lemez csatolása** lehetőséget.

   ![Képernyőfelvétel: a jobb gombbal a virtuális lemez beállításai láthatók.](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="linux-file-recovery-fails-to-auto-mount-because-the-disk-doesnt-contain-volumes"></a>A Linux-fájlok helyreállítása nem tud automatikusan csatlakoztatni, mert a lemez nem tartalmaz köteteket.

A fájlok helyreállítása során a Backup szolgáltatás a köteteket és az automatikus csatlakoztatásokat is észleli. Ha azonban a biztonsági másolatban szereplő lemezek nyers partíciókkal rendelkeznek, ezek a lemezek nem automatikusan vannak csatlakoztatva, és az adatlemez nem látható a helyreállításhoz.

A probléma megoldásához nyissa meg a [fájlok helyreállítása Azure-beli virtuális gépekről biztonsági mentést](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms).

#### <a name="linux-file-recovery-fails-because-the-os-couldnt-identify-the-file-system"></a>A Linux-fájlok helyreállítása sikertelen, mert az operációs rendszer nem tudta azonosítani a fájlrendszert

A fájl-helyreállítási parancsfájl futtatásakor az adatlemez nem fog kapcsolódni. "A következő partíciók csatlakoztatása sikertelen volt, mert az operációs rendszer nem tudta azonosítani a fájlrendszert" hibaüzenet jelenik meg.

A probléma megoldásához ellenőrizze, hogy a kötet titkosítva van-e egy külső gyártótól származó alkalmazással. Ha titkosítva van, a lemez vagy a virtuális gép nem jelenik meg titkosítottként a portálon.

1. Jelentkezzen be a biztonsági másolatba mentett virtuális gépre, és futtassa a következő parancsot:

   `lsblk -f`

   ![Képernyőfelvétel: a parancs eredményének megjelenítése a blokkos eszközök listázásához.](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

1. Ellenőrizze a fájlrendszert és a titkosítást. Ha a kötet titkosítva van, a File Recovery nem támogatott. További információ: [Az Azure-beli virtuális gépek biztonsági mentésének támogatási mátrixa](./backup-support-matrix-iaas.md#support-for-file-level-restore).

### <a name="disks-are-attached-but-the-volumes-arent-mounted"></a>A lemezek csatlakoztatva vannak, de a kötetek nincsenek csatlakoztatva

A probléma megoldásához kövesse az operációs rendszerének lépéseit.

#### <a name="windows"></a>Windows

A Windows rendszerhez készült fájl-helyreállítási parancsfájl futtatásakor "0 helyreállítási kötet csatolva" üzenet jelenik meg. A lemezek azonban a Lemezkezelés konzolon vannak felderítve.

**Lehetséges ok**: Ha iSCSI-kapcsolaton keresztül csatlakoztatott köteteket, néhány észlelt kötet offline állapotba került. Amikor az iSCSI-csatorna kommunikál a virtuális gép és a szolgáltatás között, észleli ezeket a köteteket, és online állapotba helyezi őket, de nincsenek csatlakoztatva.

   ![Képernyőfelvétel: a csatolt 0 helyreállítási kötet.](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

A probléma azonosításához és megoldásához hajtsa végre a következő lépéseket:

>[!Tip]
>Győződjön meg arról, [hogy rendelkezik a megfelelő géppel a szkript futtatásához](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

1. A **cmd** ablakban futtassa a **diskmgmt** parancsot a **Lemezkezelés** megnyitásához.
1. Keresse meg a további lemezeket. A következő példában a **2. lemez** egy további lemez.

   ![Képernyőfelvétel a Lemezkezelés ablakról, amely további lemezzel rendelkezik.](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. Kattintson a jobb gombbal az **új kötet** elemre, majd válassza a meghajtóbetűjel **és elérési utak módosítása** lehetőséget.

   ![Képernyőfelvétel: a további lemezen a jobb gombbal kattintva elérhető beállítások.](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. A **meghajtóbetűjel vagy elérési út módosítása** ablakban válassza **a következő meghajtóbetűjel kiosztása** elemet, rendeljen hozzá egy elérhető meghajtót, majd kattintson **az OK gombra**.

   ![Képernyőkép a meghajtóbetűjel vagy az elérési út módosítása ablakról.](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. Nyissa meg a fájlkezelőt, és tekintse meg a kiválasztott meghajtót, és vizsgálja meg a fájlokat.

#### <a name="linux"></a>Linux

>[!Tip]
>Győződjön meg arról, [hogy rendelkezik a megfelelő géppel a szkript futtatásához](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

Ha a védett linuxos virtuális gép LVM vagy RAID tömböket használ, kövesse a [fájlok helyreállítása Azure-beli virtuális gép biztonsági mentésének](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms)lépéseit.

### <a name="you-cant-copy-the-files-from-mounted-volumes"></a>A fájlok nem másolhatók a csatlakoztatott kötetekről

A másolás meghiúsulhat a következő hibával: "0x80070780: a fájl nem érhető el a rendszeren." 

Ellenőrizze, hogy a forráskiszolgálón van-e engedélyezve a lemez deduplikálása. Ha igen, győződjön meg arról, hogy a visszaállítási kiszolgáló a meghajtókon is engedélyezte a deduplikálás szolgáltatást. A deduplikálás nem állítható be úgy, hogy ne legyenek deduplikálni a meghajtókat a visszaállítási kiszolgálón.

## <a name="next-steps"></a>Következő lépések

- [Fájlok és mappák helyreállítása az Azure-beli virtuális gépek biztonsági másolatából](backup-azure-restore-files-from-vm.md)