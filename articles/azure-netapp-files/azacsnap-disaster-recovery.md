---
title: Vész-helyreállítás az Azure Application konzisztens pillanatkép-eszköz használatával a Azure NetApp Fileshoz | Microsoft Docs
description: A cikk azt ismerteti, hogyan végezhető el a vész-helyreállítás a Azure NetApp Files használatával használható Azure Application konzisztens pillanatkép-eszköz használata esetén.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 554fa394179e7cfc5b86a2b50eb754547d137a44
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870388"
---
# <a name="disaster-recovery-using-azure-application-consistent-snapshot-tool-preview"></a>Vész-helyreállítás az Azure Application konzisztens Snapshot Tool eszközzel (előzetes verzió)

Ez a cikk azt ismerteti, hogyan végezhető el a vész-helyreállítás a Azure NetApp Files használatával használható Azure Application konzisztens pillanatkép-eszköz használatakor.

> [!IMPORTANT]
> Ez a művelet csak az **Azure nagyméretű példányaira** vonatkozik.

## <a name="introduction"></a>Bevezetés

Az Azure-beli nagyméretű példány platformon a vész-helyreállítási hely is beállítható, ahol a tárolási kötetek pillanatképeit replikálni lehet.  Ha a pillanatképek megfelelően lettek konfigurálva egy ilyen beállítással, akkor lehetséges, hogy vészhelyzeti helyreállítást hajt végre ezen a helyen.  Ennek a dokumentumnak a célja, hogy a telepítés során vész-helyreállítást végezzen.

## <a name="prerequisites-for-disaster-recovery-setup"></a>A vész-helyreállítási telepítés előfeltételei

A vész-helyreállítási feladatátvétel megtervezése előtt a következő előfeltételek teljesülése szükséges.

- A DR-helyen kiépített DR-csomóponttal rendelkezik. A DR két lehetőség közül választhat. Az egyik a szokásos DR. a másik a többcélú DR.
- A tárolási replikáció működik. A Microsoft Operations csapat a DR kiépítés során automatikusan végrehajtja a tárolási replikáció beállítását. A tárolási replikációt a DR helyen található parancs használatával figyelheti `azacsnap -c details --details replication` .
- Beállította és konfigurálta a tárolási pillanatképeket az elsődleges helyen.
- Van egy HANA-példánya a DR-helyen, az elsődlegesnél ugyanazzal az SID-vel, mint az elsődleges példánnyal.
- Elolvasta és megértette az Azure-ban [SAP HANA Large instances magas rendelkezésre állású és](../virtual-machines/workloads/sap/hana-failover-procedure.md) vész-helyreállítási folyamat során leírt Dr feladatátvételi eljárást
- Beállította és konfigurálta a tárolási pillanatképeket a DR helyen.
- A rendszer létrehoz egy konfigurációs fájlt (például `DR.json` :) a Dr-tárolási kötetekkel és a Dr-kiszolgálón található kapcsolódó információkkal.
- A DR helyen a következő lépéseket végezte el:
  - Engedélyezze a kommunikációt a Storage szolgáltatással.
  - A SAP HANAsal való kommunikáció engedélyezése.

## <a name="set-up-disaster-recovery"></a>Vészhelyreállítás beállítása

A Microsoft a DR helyreállítás tárolási szintű replikálását támogatja. A DR-t kétféleképpen állíthatja be.

Az egyik **normál** és más a **többcélú**. A **normál** Dr-ben dedikált példánya van a Dr helyen a feladatátvételhez. A **többcélú** Dr-forgatókönyvben egy másik, a Dr helyen található HANA nagyméretű példány-egységen futó QA vagy Development HANA-példány található. De egy olyan előre telepített HANA-példányt is telepített, amely alvó állapotban van, és ugyanazzal a biztonsági azonosítóval rendelkezik, mint amelyik az a Hana-példány, amelyet át szeretne adni az adott HANA nagyméretű példány-egységnek. A Microsoft műveletei a környezetet a szolgáltatás bevezetésének időpontjában (SRF) megadott bemenet alapján, beleértve a tárolási replikációt is.

> [!IMPORTANT]
> Győződjön meg arról, hogy az összes előfeltétel teljesült a DR-telepítéshez.

## <a name="monitor-data-replication-from-primary-to-dr-site"></a>Az elsődlegesről DR-helyre történő adatreplikáció figyelése

A Microsoft Operations csapat már felügyeli és figyeli a DR-hivatkozást az elsődleges helyről a DR webhelyre.
A Snapshot paranccsal figyelheti az elsődleges kiszolgálóról a DR kiszolgálóra irányuló adatreplikációt `azacsnap -c details --details replication` .

## <a name="perform-a-failover-to-dr-site"></a>Feladatátvétel végrehajtása DR webhelyre

Futtassa a feladatátvételi parancsot a DR helyen ( `azacsnap -c restore --restore revertvolume` ).

> [!IMPORTANT]
> A `azacsnap -c restore --restore revertvolume` parancs megszakítja a tárterület replikálását az üzemi helyről a Dr helyre. A replikáció újbóli beállításához el kell érnie a Microsoft műveleteit. Ha a replikáció újra engedélyezve van, a rendszer az ehhez a SID-hez tartozó DR-tárolóban lévő összes adattal inicializálja. A feladatátvételt végrehajtó parancs elérhetővé teszi a legutóbb replikált tárolási pillanatképet. Ha vissza kell állítania egy régebbi pillanatképet, nyisson meg egy támogatási kérést, hogy a műveletek segítséget nyújtsanak a DR helyen visszaállított korábbi Pillanatképek megadásához.

A DR feladatátvételt a következő lépésekkel végezheti el:

- Le kell állítania a HANA-példányt az **elsődleges** helyen. Ez a művelet csak akkor szükséges, ha valóban a DR webhelyre végzi a feladatátvételt, így nincs adatkövetkezetlensége.
- Állítsa le a HANA-példányt a DR csomóponton az éles biztonsági azonosítóhoz.
- Futtassa a parancsot a `azacsnap -c restore --restore revertvolume` Dr csomóponton a helyreállítani kívánt biztonsági azonosítóval
  - A parancs megszakítja a tároló replikációs hivatkozását az elsődlegesről a DR-helyre.
  - A parancs csak a/Data és a/logbackups kötetet állítja vissza, a/Shared kötetet nem állítja helyre, hanem a meglévő/Shared a DR helyen található SID-re alkalmazza.
  - Csatlakoztassa a/Data és a/logbackups kötetet – ügyeljen arra, hogy hozzáadja az fstab-fájlhoz
- Állítsa vissza a HANA SYSTEMDB pillanatképét. A HANA Studio csak azt mutatja be, hogy a tárolási pillanatképben elérhető legújabb HANA-pillanatkép a parancs végrehajtásának részeként lett visszaállítva `azacsnap -c restore --restore revertvolume` .
- Állítsa helyre a bérlői adatbázist.
- Indítsa el a HANA-példányt a DR webhelyén az éles biztonsági azonosítóhoz (példa: H80 ebben az esetben).
- Végezzen tesztelést.

### <a name="example-performing-disaster-recovery"></a>Katasztrófa-helyreállítást végző példa

Ez az alszakasz a vész-helyreállítási hely feladatátvételének részletes lépéseit ismerteti.

#### <a name="step-1-get-the-volume-details-of-the-dr-node"></a>1. lépés: a DR-csomópont kötet-adatainak beolvasása

Futtassa a parancsot a `df –h` fájlrendszer és a társított kötetek listázásához a feladatátvétel után.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0%
/dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0%
/sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-2-shut-down-hana-on-the-primary-site"></a>2. lépés: HANA leállítása az elsődleges helyen

Ha az éles számítási feladatok teljes feladatátvételét hajtja végre, és lehetséges, hogy az elsődleges éles helyhez tud csatlakozni, állítsa le az átadott SAP HANA példány (oka) t a DR-re.

Ha például legfelső szintűként jelentkezett be, a következő példa azt mutatja be, hogyan lehet leállítani a SAP HANA.  Cserélje le a helyére a <sid> SAP HANA SID-t.

```bash
su - <sid>adm
HDB stop
```

#### <a name="step-3-shut-down-hana-on-dr-site"></a>3. lépés: a HANA leállítása a DR webhelyén

A kötetek visszaállítása előtt fontos a DR-helyen SAP HANA leállítása.

Ha például legfelső szintűként jelentkezett be, a következő példa azt mutatja be, hogyan lehet leállítani a SAP HANA.  Cserélje le a helyére a <sid> SAP HANA SID-t.

```bash
su - <sid>adm
HDB stop
```

> [!IMPORTANT]
> A kötetek visszaállítása előtt győződjön meg róla, hogy a DR helyen a HANA-példányok ki vannak kapcsolva.

#### <a name="step-4-restore-the-volumes"></a>4. lépés: a kötetek visszaállítása

```bash
azacsnap -c restore --restore revertvolume --dbsid H80
```

**_A Dr feladatátvételi parancs kimenete_**.

```bash
azacsnap --configfile DR.json -c restore --restore revertvolume --dbsid H80
```

```output
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to complete a Disaster Recovery
  failover.
* Any other restore points must be handled by Microsoft Operations.
* All volumes ('data' and 'other') are reverted to their most recent snapshot.
* The SnapMirror replication relationship between Prod and DR will be broken.

  CAUTION: a failback will be required after running this command and failback
   might not be a quick process and will require multiple steps in coordination
   with Microsoft Operations.

Do you wish to continue? (y/n) [n]: y
Checking state of HLI volumes for SID 'H80'
Configured volumes (Data and Other) are not quiesced for revert, will retry in 00:00:10 seconds
Volumes All Ok to Revert = True
Reverting volume 'hana_data_h80_mnt00001_t020_xdp' to snapshot 'H80_HANA_DATA_30MIN.2020-09-16_0330.0'
DR.json Data Volume #1 'hana_data_h80_mnt00001_t020_xdp' assigning to mountpoint 'mnt00001'
Reverting volume 'hana_log_backups_h80_t020_xdp01' to snapshot 'H80_HANA_LOGS_3MIN_X9.2020-09-16_0339.recent'
DR.json Other Volume #1 'hana_log_backups_h80_t020_xdp01' assigning to mountpoint '01'
HLI Volume revert completed for SID 'H80'
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*********************  HANA DR Restore Steps  **********************************
* Please complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
```

> [!NOTE]
> A konzol megjelenítésének befejezéséhez szükséges lépéseket a DR feladatátvételek tárterület-előkészítésének befejezéséhez kell végrehajtani.

#### <a name="step-5-unmount-unnecessary-filesystems"></a>5. lépés: felesleges fájlrendszerek leválasztása

Futtassa a parancsot a `umount` nem szükséges fájlrendszerek/kötetek leválasztásához.

```bash
umount <Mount point>
```

Válassza le az adattároló és a napló biztonsági mentési csatolási. Lehet, hogy több adatcsatlakoztatási pont van a kibővíthető forgatókönyvben.

#### <a name="step-6-configure-the-mount-points"></a>6. lépés: a csatlakoztatási pontok konfigurálása

Módosítsa a fájlt `/etc/fstab` úgy, hogy az az elsődleges biztonsági azonosító (ebben a példában a SID = H80) adatait és naplójában tárolt biztonsági másolatok bejegyzéseit adja hozzá, és hozzáadja az elsődleges hely Dr-köteteiből létrehozott új csatlakoztatási pontokhoz tartozó bejegyzéseket. Az új csatlakoztatási pont bejegyzései a parancs kimenetében érhetők el.

- Tegye megjegyzésbe a DR helyen futó meglévő csatlakoztatási pontokat a következő `#` karakterrel:

  ```output
  #172.18.20.241:/hana_data_h80_mnt00001_t020_vol /hana/data/H80/mnt00001 nfs     rw,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  #172.18.20.241:/hana_log_backups_h80_t020 /hana/logbackups/H80 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

- Adja hozzá a következő sorokat a `/etc/fstab`
  > Ennek azonos kimenetnek kell lennie a parancsból

  ```output
  10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

#### <a name="step-7-mount-the-recovery-volumes"></a>7. lépés: a helyreállítási kötetek csatlakoztatása

Az `mount –a` összes csatlakoztatási pont csatlakoztatásához hajtsa végre a parancsot.

```bash
mount -a
```

Most, ha végrehajtja, `df –h` látnia kell a `*_dp` csatlakoztatott köteteket.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0% /dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0% /sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-8-recover-the-systemdb"></a>8. lépés: a SYSTEMDB helyreállítása

A HANA Studióban kattintson a jobb gombbal a SYSTEMDB-példányra, és válassza a biztonsági mentés és helyreállítás lehetőséget, majd a "rendszeradatbázis helyreállítása" elemet.

Tekintse meg az adatbázis pillanatképből való helyreállítására vonatkozó útmutatót, konkrétan a SYSTEMDB.

#### <a name="step-9-recover-the-tenant-database"></a>9. lépés: a bérlői adatbázis helyreállítása

A HANA Studióban kattintson a jobb gombbal a SYSTEMDB-példányra, és válassza a biztonsági mentés és helyreállítás lehetőséget, majd a "bérlői adatbázis helyreállítása" elemet.

Tekintse meg a következő útmutatót: adatbázis helyreállítása pillanatképből, konkrétan a BÉRLŐi adatbázis (ok) ból.

### <a name="run-azacsnap--c-backup-at-the-dr-site"></a>Futtatás `azacsnap -c backup` a Dr webhelyén

Ha a DR helyen pillanatkép-alapú biztonsági mentéseket futtat, akkor a `azacsnap` Dr hely konfigurációs fájljában KONFIGURÁLT HANA-kiszolgáló nevének meg kell egyeznie az üzemi kiszolgáló nevével.

> [!IMPORTANT]
> A a `azacsnap -c backup` képes tároló-Pillanatképek létrehozásához a Dr helyen, ezeket a rendszer nem replikálja automatikusan egy másik helyre.  A Microsoft műveleteivel jobban megismerheti a fájlok és adatok visszaadását az eredeti üzemi helyre.

## <a name="next-steps"></a>Következő lépések

- [Pillanatkép részleteinek beolvasása](azacsnap-cmd-ref-details.md)
- [Biztonsági mentés készítése](azacsnap-cmd-ref-backup.md)
