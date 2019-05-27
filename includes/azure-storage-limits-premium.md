---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7b75e24e5d59206ee8330c3ca9eaf86eacbad13d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114803"
---
### <a name="premium-performance-block-blob-storage"></a>Prémium szintű teljesítményt blokkblob-tárolás

Prémium szintű teljesítményt block blob storage-fiókban az alkalmazásokat, amelyek kisebb, használja a kilobájtoktól a petabájtokig tartományát, objektumok van optimalizálva. Ideális a nagy tranzakciós díjakat biztosítanak vagy konzisztens közel valós idejű tároló igénylő alkalmazásokhoz. Prémium szintű teljesítményt blokkblob típusú tárolás az alkalmazások méretezése a célja. Ha azt tervezi, a kérések száma másodpercenként több ezer vagy több petabájtnyi, a tárolási kapacitást több száz igénylő alkalmazás üzembe helyezése, lépjen kapcsolatba velünk beküld egy támogatási kérést az a [az Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="premium-performance-filestorage-preview"></a>Prémium szintű teljesítményt FileStorage (előzetes verzió)

Prémium files használjon egy egyedi nevű tárfiókot **FileStorage (előzetes verzió)**. Az ilyen típusú fiókok számítási feladatokat, és magas IOPS, nagy átviteli sebességhez és egységes közel valós idejű lett tervezve. Prémium szintű file storage a létesített megosztás méretének együtt növekszik.

|Terület  |Cél  |
|---------|---------|
|Maximális méret kiépítve     |100 TiB     |
|Megosztások   |Korlátlan  |
|IOPS     |100,000    |
|Bejövő forgalom|4,136 MiB/s     |
|Kimenő forgalom|6,204 MiB/s |

 Prémium szintű fájl megosztása a méretezési célokat, tekintse meg a [prémium files tárolók skálázása](../articles/storage/common/storage-scalability-targets.md#premium-files-scale-targets) szakaszban.

### <a name="premium-performance-page-blob-storage"></a>Prémium szintű teljesítményt lap a blob storage

Prémium szintű teljesítményt, általános célú v1 vagy v2-tárfiókok rendelkezik a következő teljesítménycélokat:

| Teljes számla kapacitás                            | Helyileg redundáns tárfiókok teljes sávszélesség                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Lemez kapacitása: 35 TB <br>Pillanatkép-kapacitás: 10 TB | Felfelé 50 Gigabit / másodperc a bejövő<sup>1</sup> + kimenő<sup>2</sup> |

<sup>1</sup> tárfiók küldött összes adatot (kérést)

<sup>2</sup> egy tárfiókból fogadott összes adatot (választ)

Ha nem felügyelt lemezek prémium szintű teljesítményt tárfiókok használja, és az alkalmazás meghaladja a skálázhatósági célokat, az egy tárfiókban, érdemes migrálása felügyelt lemezekre. Ha nem szeretné migrálása felügyelt lemezekre, hozhat létre az alkalmazás több tárfiók használata. Ezt követően az adatok particionálása ezen a tárfiókon keresztül. Például, ha 51 TB-os lemezek csatolása több virtuális gép között elosztva őket két tárfiókot. 35 TB csak az egyetlen prémium szintű storage-fiókok. Győződjön meg arról, hogy a teljesítmény egyetlen premium storage-fiók soha nem rendelkezik több mint 35 TB kiosztott lemezeket.