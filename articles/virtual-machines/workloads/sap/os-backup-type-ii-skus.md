---
title: Az SAP HANA operációs rendszer biztonsági mentése és helyreállítása az Azure-ban (nagyméretű példányok) II-es típusú SKU-ban | Microsoft Docs
description: Operációs rendszer biztonsági mentésének és visszaállításának elvégzése az Azure-beli SAP HANA (nagyméretű példányok) II. típusú SKU-ban
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c03aa119b40a81f553a97ec013f89f88daabf293
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101668656"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Az operációs rendszer biztonsági mentése és visszaállítása a 3. típusú bélyegzők II. típusához

Ez a dokumentum ismerteti az operációsrendszer-fájlok biztonsági mentésének és visszaállításának lépéseit a 3. típusú HANA nagyméretű példányainak **II. típusára** vonatkozóan. 

>[!Important]
> **Ez a cikk nem vonatkozik a II. típusú, nagyméretű HANA-példányok esetében a 2. változatra.** A 2. típusú Hana nagyméretű példányú, 4 HANA nagyméretű példányú bélyegekkel üzembe helyezett rendszerindító LUN-lemezekről biztonsági mentés készíthető a Storage-pillanatképekkel, mivel ez a helyzet a 3. változatban már használatos


>[!NOTE]
>Az operációs rendszer biztonsági mentési parancsfájljai a hátsó szoftvert használják, amely előre telepítve van a-kiszolgálón.  

Miután a Microsoft csapata elvégezte a kiépítés befejezését `Service Management` , alapértelmezés szerint a kiszolgáló két biztonsági mentési ütemtervtel van konfigurálva, hogy biztonsági másolatot készítsen az operációs rendszer fájlrendszeri szintjéről. A biztonsági mentési feladatok ütemezett listáját a következő paranccsal tekintheti meg:
```
#crontab –l
```
A biztonsági mentés ütemtervét bármikor módosíthatja a következő parancs használatával:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Manuális biztonsági mentés készítése

Az operációs rendszer fájlrendszerének biztonsági mentése már egy **cron-feladat** használatával van ütemezve. Az operációs rendszer fájljának biztonsági mentését azonban manuálisan is végrehajthatja. Manuális biztonsági mentés végrehajtásához futtassa a következő parancsot:

```
#rear -v mkbackup
```
A következő képernyőn látható a manuális biztonsági mentés:

![Hogyan](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Biztonsági másolat visszaállítása

A biztonsági másolatból visszaállíthat egy teljes biztonsági mentést vagy egy különálló fájlt. A visszaállításhoz használja a következő parancsot:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
A visszaállítást követően a fájl helyreáll a jelenlegi munkakönyvtárban.

A következő parancs egy fájl visszaállítását mutatja be a Backup *. tar. gz* */etc/fstabfrom* .
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>A biztonsági másolatból való visszaállítás után a fájlt a kívánt helyre kell másolnia.

A következő képernyőfelvétel a teljes biztonsági mentés visszaállítását mutatja be:

![Képernyőfelvétel: a visszaállítást kérő parancssori ablak.](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Hogyan kell telepíteni a hátsó eszközt, és módosítani a konfigurációt? 

A relaxációs és helyreállító (hátsó) csomagok **előre telepítve** vannak a HANA nagyméretű példányainak **II. típusú SKU** -ban, és nincs szükség beavatkozásra. Közvetlenül megkezdheti az operációs rendszer biztonsági mentésének hátsó használatát.
Azonban abban az esetben, ha a csomagokat saját maga is telepítenie kell, kövesse a felsorolt lépéseket a hátsó eszköz telepítéséhez és konfigurálásához.

A **hátsó** biztonsági mentési csomagok telepítéséhez használja a következő parancsokat:

**SLES** operációs rendszer esetén használja a következő parancsot:
```
#zypper install <rear rpm package>
```
**RHEL** operációs rendszer esetén használja a következő parancsot: 
```
#yum install rear -y
```
A hátsó eszköz konfigurálásához frissítenie kell a paramétereket **OUTPUT_URL**  és **BACKUP_URL**  a *fájl/etc/Rear/local.conf*.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

Az alábbi képernyőfelvételen a teljes biztonsági mentés visszaállítása látható: a ![ képernyőképen egy parancssori ablak jelenik meg, amely a hátsó eszköz használatával a visszaállítást végzi.](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
