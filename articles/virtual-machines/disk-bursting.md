---
title: Felügyelt lemez kitörése
description: Ismerje meg az Azure-lemezekhez és az Azure-beli virtuális gépekhez készült lemezek kitörését.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 9758b026ef205e6608f7fc4110219dc5f267369e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568715"
---
# <a name="managed-disk-bursting"></a>Felügyelt lemez kitörése
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

Az Azure [Premium SSD](disks-types.md#premium-ssd) -k két modellt kínálnak:

- Egy igény szerinti burst modell (előzetes verzió), ahol a lemez feltört, amikor az igényei meghaladják a jelenlegi kapacitását. Ez a modell további díjakat eredményez, amikor a lemez feltört. A nem kreditek kitörése csak a 512 GiB-nál nagyobb méretű lemezeken érhető el.
- Egy kredit-alapú modell, amelyben a lemez csak abban az esetben lesz feltört, ha a kredit gyűjtője feltörte a jóváírást. Ez a modell nem számít fel további díjat a lemez feltörése esetén. A kredit-alapú adatmennyiség csak a 512 GiB és kisebb lemezeken érhető el.

Emellett a [felügyelt lemezek teljesítményi szintje is módosítható](disks-change-performance.md), ami ideális lehet, ha a számítási feladat egyébként a burst-ban fut.

|  |Kredit-alapú kitörés  |Igény szerinti kitörés  |Teljesítményszint módosítása  |
|---------|---------|---------|---------|
| **Forgatókönyvek**|Ideális a rövid távú skálázáshoz (30 perc vagy kevesebb).|Ideális a rövid távú skálázáshoz (nem korlátozott időtartamú).|Ideális, ha a számítási feladat egyébként folyamatosan fut a burst folyamat során.|
|**Költségek**     |Ingyenes         |A Cost változó, a részletekért tekintse meg a [Számlázási](#billing) szakaszt.        |Az egyes teljesítményszint díja rögzített, a részletekért tekintse meg [Managed Disks díjszabását](https://azure.microsoft.com/pricing/details/managed-disks/) .         |
|**Rendelkezésre állás**     |Csak a prémium szintű SSD-512 GiB és kisebb méretekben érhető el.         |Csak a 512 GiB-nál nagyobb prémium SSD-k esetén érhető el.         |A prémium szintű SSD-méretek számára elérhető.         |
|**Engedélyezés**     |Alapértelmezés szerint engedélyezve van a jogosult lemezeken.         |A felhasználónak engedélyezve kell lennie.         |A felhasználónak manuálisan kell módosítania a szintet.         |

## <a name="common-scenarios"></a>Gyakori forgatókönyvek
A következő forgatókönyvek nagy mértékben kihasználhatják a betörést:
- Az **indítási idők javítása** – a Betöréssel a példány jelentősen gyorsabb ütemben indul. A Premium-kompatibilis virtuális gépek alapértelmezett operációsrendszer-lemeze például a P4-lemez, amely legfeljebb 120 IOPS és 25 MB/s kiépített teljesítményű. A Betöréssel a P4 akár 3500 IOPS-t és 170 MB/s-ot is biztosít, ami lehetővé teszi, hogy az indítás akár 6X-ig gyorsítható legyen.
- **Kötegelt feladatok kezelése** – egyes alkalmazások számítási feladatai ciklikus jellegűek. A legtöbb esetben az alapteljesítményre és a gyorsabb teljesítményre van szükségük, rövid ideig. Erre példa egy olyan könyvelési program, amely kis mennyiségű lemezterületet igénylő napi tranzakciókat dolgoz fel. A hónap végén a program befejezte a nagy mennyiségű lemezes adatforgalomra vonatkozó jelentések összeegyeztetését.
- **Forgalmi tüskék** – a webkiszolgálók és alkalmazásaiik bármikor megtapasztalják a forgalom túlterhelését. Ha a webkiszolgálót olyan virtuális gépek vagy lemezek használják, amelyek kipukkanást használnak, a kiszolgálók jobban fel lesznek szerelve a forgalmi tüskék kezelésére. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>Következő lépések

Az igény szerinti bursás engedélyezéséhez tekintse [meg az igény szerinti kitörés engedélyezése](disks-enable-bursting.md)című témakört.
Ha meg szeretné tudni, hogyan szerezhet be betekintést a feltört erőforrásaiba, tekintse meg a [lemezek felszakadási mérőszámait](disks-metrics.md).
