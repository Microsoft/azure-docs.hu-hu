---
title: 'Törlés az Azure Application konzisztens pillanatkép-eszköz használatával a következőhöz: Azure NetApp Files | Microsoft Docs'
description: Útmutatást nyújt az Azure Application konzisztens pillanatkép-eszköz törlés parancsának futtatásához, amelyet a Azure NetApp Files használatával használhat.
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
ms.openlocfilehash: 0e2e4beebedb93524da43c5a3fad750b0295f5cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632726"
---
# <a name="delete-using-azure-application-consistent-snapshot-tool-preview"></a>Törlés az Azure Application konzisztens pillanatkép-eszköz használatával (előzetes verzió)

Ez a cikk útmutatást nyújt az Azure Application konzisztens pillanatkép-eszköz törlési parancsának futtatásához, amelyet a Azure NetApp Files használatával használhat.

## <a name="introduction"></a>Bevezetés

A kötet-Pillanatképek és az adatbázis-katalógus bejegyzései a `azacsnap -c delete` paranccsal törölhetők.

> [!IMPORTANT]
> A parancs futtatása előtt kevesebb, mint 10 perc alatt létrehozott Pillanatképek nem törölhetők, mert lehetséges a pillanatkép-replikációval való interferencia.

## <a name="command-options"></a>Parancs beállításai

A `-c delete` parancs a következő beállításokkal rendelkezik:

- `--delete hana` Ha a kapcsolóval együtt használja a beállításokat `--hanasid <SID>` , és `--hanabackupid <HANA backup id>` törli a feltételnek megfelelő SAP HANA biztonsági mentési katalógusból származó bejegyzéseket.

- `--delete storage` Ha a kapcsolóval együtt használja, a `--snapshot <snapshot name>` rendszer törli a pillanatképet a háttérbeli tárolóból.

- `--delete sync` Ha a kapcsolóval együtt használja a beállításokat `--hanasid <SID>` `--hanabackupid <HANA backup id>` , és lekéri a tárolási pillanatkép nevét a biztonsági mentési katalógusból `<HANA backup id>` , majd törli a bejegyzést a biztonsági mentési katalógusban _és_ a pillanatképet a megnevezett pillanatképet tartalmazó összes kötetről.

- `--delete sync` Ha a szolgáltatással együtt használja, a rendszer a biztonsági mentési `--snapshot <snapshot name>` katalógusban lévő összes bejegyzést beolvassa a `<snapshot name>` SAP HANA biztonsági mentési azonosítót, és törli a biztonsági mentési katalógusban található bejegyzést _és_ a pillanatképet a megnevezett pillanatképet tartalmazó összes kötetről.

- `[--force]` választható Legyen *körültekintő*.  Ezzel a művelettel a rendszer megerősítő kérés nélkül kényszeríti a törlést.

- `[--configfile <config filename>]` a (z) egy opcionális paraméter, amely lehetővé teszi az egyéni konfigurációs fájlnevek nevét.

### <a name="delete-a-snapshot-using-sync-option"></a>Pillanatkép törlése a `sync` kapcsoló használatával

```bash
azacsnap -c delete --delete sync --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> Ellenőrzi, hogy a biztonsági mentési katalógusban szerepel-e a SAP HANA biztonsági mentési azonosító 157979797979-es azonosítójú bejegyzése, beolvassa a tárolási pillanatkép nevét, és törli a biztonsági mentési katalógusban található bejegyzést, valamint a pillanatképet a megnevezett pillanatképet tartalmazó összes kötetről.

```bash
azacsnap -c delete --delete sync --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> A (z) hana_hourly .2020-01 -22 _2358 nevű pillanatképhez tartozó biztonsági mentési katalógus bejegyzéseinek ellenőrzése után a rendszer beolvassa a SAP HANA Backup-azonosítót, és törli a biztonsági mentési katalógusban található bejegyzést és a pillanatképet a megnevezett pillanatképet tartalmazó összes kötetről.

### <a name="delete-a-snapshot-using-hana-option"></a>Pillanatkép törlése a `hana` kapcsoló használatával

```bash
azacsnap -c delete --delete hana --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> Törli SAP HANA a 157979797979 biztonsági mentési azonosítót a biztonsági mentési katalógusból a SID-H80.

### <a name="delete-a-snapshot-using-storage-option"></a>Pillanatkép törlése a `storage` kapcsoló használatával

```bash
azacsnap -c delete --delete storage --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Törli a pillanatképet minden olyan kötetről, amely hana_hourly .2020-01 -22 _2358 nevű pillanatképet tartalmaz.

**Kimenet a `--delete storage` kapcsoló használatával**

A rendszer megkéri a felhasználót, hogy erősítse meg a törlést.

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T221702.2535255Z
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z'.
Are you sure you want to permanently delete the snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z' from all storage volumes.? (y/n) [n]: y
Snapshot deletion completed
```

A felhasználó jóváhagyása a választható paraméter használatával elkerülhető a következő módon `--force` :

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T222201.4988618Z --force
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T222201.4988618Z'.
Snapshot deletion completed
```

## <a name="next-steps"></a>Következő lépések

- [Pillanatkép részleteinek beolvasása](azacsnap-cmd-ref-details.md)
- [Biztonsági mentés készítése](azacsnap-cmd-ref-backup.md)
