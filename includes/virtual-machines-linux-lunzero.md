---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09fa612e7e5c681da16bf19e94c626ae14a3b8a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "77590754"
---
Adatlemezek Linux rendszerű virtuális gépekhez való hozzáadásakor hibák merülhetnek fel, ha egy lemez nem létezik a LUN 0 helyen. Ha manuálisan ad hozzá lemezt a `az vm disk attach -new` paranccsal, és egy LUN () értéket ad meg `--lun` , és nem engedélyezi az Azure platform számára a megfelelő logikai egység meghatározását, ügyeljen arra, hogy a (z) 0. logikai egységben már létezik a lemez. 

Vegye figyelembe a következő példát, amely a kimenet részleteit mutatja `lsscsi` :

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

A két adatlemez létezik a LUN 0 és a LUN 1 (a `lsscsi` kimeneti adatok első oszlopa `[host:channel:target:lun]` ). Mindkét lemeznek elérhetőnek kell lennie a virtuális gépen belülről. Ha manuálisan adta meg az első lemezt, amelyet fel szeretne venni az 1. LUN-ba, a másodikban pedig a 2. LUN-t, előfordulhat, hogy a lemezeket nem látja megfelelően a virtuális gépről.

> [!NOTE]
> Az Azure `host` -érték 5 az alábbi példákban, de ez a kiválasztott tárolási típustól függően változhat.
> 
> 

A lemez viselkedése nem Azure-probléma, de a Linux-kernel a SCSI-specifikációkat követi. Amikor a Linux-kernel megkeresi az SCSI-buszt a csatlakoztatott eszközökön, egy eszközt kell megtalálnia a LUN 0 helyen ahhoz, hogy a rendszer folytassa a további eszközök vizsgálatát. Például:

* Tekintse át az `lsscsi` adatlemez hozzáadása utáni kimenetet annak ellenőrzéséhez, hogy van-e lemez a LUN 0 egységben.
* Ha a lemez nem jelenik meg megfelelően a virtuális gépen, ellenőrizze, hogy létezik-e lemez a LUN 0 helyen.

