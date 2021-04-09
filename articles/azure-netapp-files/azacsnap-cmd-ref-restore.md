---
title: Visszaállítás az Azure Application konzisztens pillanatkép-eszköz használatával a Azure NetApp Fileshoz | Microsoft Docs
description: Útmutatást nyújt az Azure Application konzisztens pillanatkép-eszköz Restore parancsának futtatásához, amelyet a Azure NetApp Files használhat.
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
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 793b4da8fcf46ba4d5618f8ada86f9c3c8026ffd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865264"
---
# <a name="restore-using-azure-application-consistent-snapshot-tool-preview"></a>Visszaállítás az Azure Application konzisztens pillanatkép-eszköz használatával (előzetes verzió)

Ez a cikk útmutatást nyújt az Azure Application konzisztens pillanatkép-eszköz visszaállítási parancsának futtatásához, amelyet a Azure NetApp Files használatával használhat.

## <a name="introduction"></a>Bevezetés

A pillanatképből kötet-visszaállítást végez a parancs használatával `azacsnap -c restore` .

> [!IMPORTANT]
> Ez nem végez adatbázis-helyreállítást, csak a kötetek (ek) visszaállítását az alábbi lehetőségek mindegyike esetében.

## <a name="command-options"></a>Parancs beállításai

A `-c restore` parancs a következő beállításokkal rendelkezik:

- `--restore snaptovol` Létrehoz egy új kötetet a célként megadott köteten lévő legújabb pillanatkép alapján. Ez a parancs egy új "klónozott" kötetet hoz létre a beállított célként megadott kötet alapján, amely a legújabb kötet-pillanatképet használja az új kötet létrehozásához.  Ez a parancs nem szakítja meg a tárolási replikálást az elsődlegesről a másodlagosra. A legújabb elérhető Pillanatképek klónozása Ehelyett a DR helyen történik, és a klónozott kötetek ajánlott fájlrendszer-csatolási jelennek meg. Ezt a parancsot a **Dr régióban** (azaz a cél feladatátvételi rendszeren) található Azure nagyméretű példányrendszer-rendszeren kell futtatni.

- `--restore revertvolume` A legutóbbi pillanatkép alapján visszaállít egy korábbi állapotba a célként megadott kötetet.  A parancs használata a DR feladatátvétel részeként a párosított DR régióban. Ez a parancs **leállítja** a tárolási replikációt az elsődleges helyről a másodlagos helyre, és visszaállítja a CÉLKÉNT megadott Dr kötet (eke) t a Dr köteteken lévő legújabb elérhető pillanatképre, valamint a visszaállított Dr kötetek ajánlott fájlrendszer-csatolási. Ezt a parancsot a **Dr régióban** (azaz a cél feladatátvételi rendszeren) található Azure nagyméretű példányrendszer-rendszeren kell futtatni.
    > [!NOTE]
    > A sub-Command ( `--restore revertvolume` ) csak az Azure nagyméretű példányai esetében érhető el, és nem érhető el Azure NetApp Files számára.
- `--dbsid <SAP HANA SID>` a konfigurációs fájlból a SAP HANA SID van kiválasztva, hogy alkalmazza a kötet-visszaállítási parancsokat a következőre:.

- `[--configfile <config filename>]` a (z) egy opcionális paraméter, amely lehetővé teszi az egyéni konfigurációs fájlnevek nevét.

## <a name="perform-a-test-dr-failover-azacsnap--c-restore---restore-snaptovol"></a>A DR feladatátvételi teszt végrehajtása `azacsnap -c restore --restore snaptovol`

Ez a parancs olyan, mint a "teljes" DR feladatátvételi parancs ( `--restore restorevolume` ), de az elsődleges hely és a vész-helyreállítási hely közötti replikáció megszakítása helyett a rendszer létrehozza a klónozási kötetet a vész-helyreállítási kötetekből, így lehetővé téve a legújabb pillanatkép visszaállítását a Dr helyen. Ezeket a klónozott köteteket ezután az ügyfél felhasználhatja a vész-helyreállítás tesztelésére anélkül, hogy végre kellene hajtania a HANA-környezet teljes feladatátvételét, amely megszakítja az elsődleges hely és a vész-helyreállítási hely közötti replikációs megállapodást.

- Így több különböző visszaállítási pont is tesztelhető, amelyek mindegyike saját visszaállítási ponttal rendelkezik.
- A klónt a parancs végrehajtásának időbélyegzője jelöli ki, és a futtatáskor elérhető legfrissebb adatok és egyéb Pillanatképek jelölését jelöli.

> [!IMPORTANT]
> Ez a művelet csak az Azure nagyméretű példányára vonatkozik.
>
> - A parancs végrehajtásához szükség van a kapcsolattartási e-mail-címre a klónok 4 hét után történő törlése előtt.
> - A parancs minden végrehajtása egy új klónt hoz létre, amelyet a Microsoft-műveleteknek törölni kell a teszt lezárása után.
> - A rendszer 4 hét után automatikusan törli a létrehozott klónozott köteteket.

A konfigurációs fájlnak (például `DR.json` :) csak a Dr köteteket kell tartalmaznia, és nem az éles köteteket, ellenkező esetben az éles köteteken létrehozhatók a klónok.

### <a name="output-of-the-azacsnap--c-restore---restore-snaptovol-command-for-single-node-scenario"></a>A parancs kimenete `azacsnap -c restore --restore snaptovol` (Single-Node forgatókönyv esetén)

```output
> azacsnap --configfile DR.json -c restore --restore snaptovol --dbsid H80
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to simulate a Disaster Recovery
  failover without actually requiring a failover and subsequent failback.
* Any other restore points must be handled by Microsoft Operations.
* As part of the process, a clone is created of the each of the 'data' and 'other'
  volumes per the configuration file.

Do you wish to continue? (y/n) [n]: y

About to create clones of volumes based on the latest snapshot, these will be
kept for 4 weeks before being automatically deleted by Microsoft Operations.
Enter an email address to contact when deleting clones: <b>person@nowhere.com</b>
Checking state of HLI volumes for SID 'PEW'
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_sapprdhdb80_mnt00001_t020_xdp_rwclone_20200916_0256  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_sapprdhdb80_t020_xdp_rwclone_20200916_0256  /hana/log_backups/H80/01 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*******************  HANA Test DR Restore Steps  ******************************
* Complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
*  These snapshot copies (clones) are kept for 4 weeks before                  *
*  being automatically removed.                                                *
*  Please contact Microsoft Operations to delete them earlier.                 *
********************************************************************************
```

> [!IMPORTANT]
> A "csatlakoztatási pontok kötet szerint" kimenete eltér a különböző forgatókönyvek esetében.

## <a name="perform-full-dr-failover-azacsnap--c-restore---restore-revertvolume"></a>Teljes DR feladatátvétel végrehajtása `azacsnap -c restore --restore revertvolume`

Ez a parancs **leállítja** a tárolási replikációt az elsődleges helyről a másodlagos helyre, visszaállítja a legújabb pillanatképet a Dr köteteken, és megadja a Dr kötetek csatolási.

Ezt a parancsot a DR-kiszolgálón egy konfigurációs fájllal (például) kell végrehajtani `DR.json` , csak Dr kötetekkel!

Végezzen feladatátvételt a DR-helyre a parancs végrehajtásával `azacsnap -c restore --restore revertvolume` .  Ehhez a parancshoz paraméterként hozzá kell adni egy SID-t. Ez a HANA-példány SID-azonosítója, amelyet a DR-helyen kell helyreállítani.

> [!IMPORTANT]
> Csak akkor futtassa ezt a parancsot, ha a DR gyakorlat vagy a teszt végrehajtását tervezi. Ez a parancs megtöri a replikálást. A replikálás újbóli engedélyezéséhez kapcsolatba kell lépnie a Microsoft-műveletekkel.

Magas szinten a DR feladatátvétel végrehajtásának lépései a következők:

- Le kell állítania a HANA-példányt az **elsődleges** helyen. Ez a művelet csak akkor szükséges, ha a feladatátvételt valóban a DR webhelyre végzi el az adatkövetkezetlenségek elkerülése érdekében.
- Állítsa le a HANA-példányt a DR csomóponton az éles biztonsági azonosítóhoz.
- Futtassa a parancsot a `azacsnap -c restore --restore revertvolume` Dr csomóponton a helyreállítani kívánt biztonsági azonosítóval.
  - A parancs megszakítja a tároló replikációs hivatkozását az elsődlegesről a DR-helyre.
  - A parancs visszaállítja az "adatmennyiség" és a "többi" kötetet a konfigurált értékre.  Ez a művelet általában a és a fájlrendszerek kötetei esetében lehet `/hana/data` `/hana/logbackups` .  A `/hana/shared` fájlrendszert nem lehet helyreállítani, hanem a meglévőt a Dr helyen található SID-re használja `/hana/shared` .
  - A és a kötetek csatlakoztatása `/hana/data` `/hana/logbackups` – Győződjön meg arról, hogy hozzá vannak adva a `/etc/fstab` fájlhoz
- Állítsa vissza a HANA SYSTEMDB pillanatképét. A HANA Studio csak a Pillanatkép-parancs végrehajtásának részeként a tárolási pillanatképben elérhető legújabb HANA-pillanatképet jeleníti meg `azacsnap -c restore --restore revertvolume` .
- Állítsa helyre a bérlői adatbázist.
- Indítsa el a HANA-példányt a DR webhelyén az éles biztonsági azonosítóhoz (példa: H80 ebben az esetben).
- Végezzen el egy adatbázis-tesztelést.

## <a name="next-steps"></a>Következő lépések

- [Biztonsági mentés készítése](azacsnap-cmd-ref-backup.md)
- [Pillanatkép részleteinek beolvasása](azacsnap-cmd-ref-details.md)
