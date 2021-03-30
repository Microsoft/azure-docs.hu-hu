---
title: StorSimple 8000 sorozatú rendszerkorlátok | Microsoft Docs
description: Leírja a rendszerkorlátokat és az ajánlott méreteket a StorSimple 8000 sorozat összetevőihez és kapcsolataihoz.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7392678-0924-46c6-9c59-1665cb9b6586
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/28/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 70f2d9542082ddf7ecf1d1e7361b0ecdb14c5ef8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "68963375"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Mi a StorSimple 8000 Series rendszer korlátai?

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés

A StorSimple méretezhető és rugalmas tárolási lehetőséget biztosít az adatközpont számára. A StorSimple-megoldás megtervezésekor, üzembe helyezése és üzemeltetése során azonban bizonyos korlátokat kell szem előtt tartania. A következő táblázat ismerteti ezeket a korlátozásokat, és néhány javaslatot tartalmaz, hogy a lehető legtöbbet hozza ki a StorSimple-megoldásból.

| Korlátazonosító | Korlát | Megjegyzések |
| --- | --- | --- |
| A Storage-fiók hitelesítő adatainak maximális száma |64 | |
| Mennyiségi tárolók maximális száma |64 | |
| Kötetek maximális száma |255 | |
| A helyileg rögzített kötetek maximális száma |32 | |
| Az ütemtervek maximális száma sávszélesség-sablon alapján |168 |Minden órában, a hét minden napján (24 * 7) ütemezett ütemterv. |
| A rétegű kötetek maximális mérete fizikai eszközökön |64 TB 8100 és 8600 |8100 és 8600 fizikai eszközök. |
| Rétegekből álló kötetek maximális mérete az Azure-ban virtuális eszközökön |30 TB 8010 <br></br> 64 TB 8020 |a 8010-es és a 8020-es virtuális eszközök az Azure-ban standard szintű tárolást és Premium Storage-t használnak. |
| Egy helyileg rögzített kötet maximális mérete fizikai eszközökön |8,5 TB 8100 <br></br> 22,5 TB 8600 |8100 és 8600 fizikai eszközök. |
| ISCSI-kapcsolatok maximális száma |512 | |
| A kezdeményezők által létesített iSCSI-kapcsolatok maximális száma |512 | |
| Hozzáférés-vezérlési rekordok maximális száma eszközönként |64 | |
| Kötetek maximális száma biztonsági mentési házirend szerint |20 | |
| Az ütemezés szerint megőrzött biztonsági másolatok maximális száma (biztonsági mentési házirendben) |64 | |
| Ütemezett biztonsági mentési szabályzatok maximális száma |10 | |
| A köteten megőrzött bármilyen típusú Pillanatképek maximális száma |256 |Ez a szám helyi pillanatképeket és Felhőbeli pillanatképeket tartalmaz. |
| Bármely eszközön megtalálható Pillanatképek maximális száma |10,000 | |
| A biztonsági mentéshez, visszaállításhoz vagy klónozáshoz párhuzamosan feldolgozható kötetek maximális száma |16 |<ul><li>Ha a rendszer több mint 16 kötetet használ fel, a feldolgozási pontok válnak elérhetővé.</li><li>A klónozott vagy visszaállított kötetek új biztonsági mentései nem végezhetők el, amíg a művelet be nem fejeződik. Helyi kötet esetén azonban a biztonsági mentések a kötet online állapotának engedélyezése után engedélyezettek.</li></ul> |
| A biztonsági mentés és a klónozás visszaszerzési ideje a többplatformos kötetek esetében |< 2 perc |<ul><li>A kötet a visszaállítási vagy a klónozási művelettől számított 2 percen belül elérhetővé válik, a kötet méretétől függetlenül.</li><li>A kötet teljesítménye kezdetben lassabb lehet a normál értéknél, mivel a legtöbb adat és metaadatok a felhőben maradnak. A teljesítmény növelheti a felhőből a StorSimple-eszközre irányuló adatforgalmat.</li><li>A metaadatok letöltésének teljes ideje a lefoglalt kötet méretétől függ. A metaadatokat a rendszer automatikusan a háttérbe helyezi az eszközön, a lefoglalt mennyiségi adatokhoz tartozó TB-onként 5 perces sebességgel. Ezt a sebességet a felhőre irányuló internetes sávszélesség is érintheti.</li><li>A visszaállítási vagy klónozási művelet akkor fejeződik be, amikor az összes metaadat az eszközön található.</li><li>A biztonsági mentési műveletek nem hajthatók végre, amíg a visszaállítási vagy a klónozási művelet teljesen be nem fejeződik. |
| A helyreállítás ideje a helyileg rögzített kötetek esetében |< 2 perc |<ul><li>A kötet a visszaállítási művelettől számított 2 percen belül elérhetővé válik, a kötet méretétől függetlenül.</li><li>A kötet teljesítménye kezdetben lassabb lehet a normál értéknél, mivel a legtöbb adat és metaadatok a felhőben maradnak. A teljesítmény növelheti a felhőből a StorSimple-eszközre irányuló adatforgalmat.</li><li>A metaadatok letöltésének teljes ideje a lefoglalt kötet méretétől függ. A metaadatokat a rendszer automatikusan a háttérbe helyezi az eszközön, a lefoglalt mennyiségi adatokhoz tartozó TB-onként 5 perces sebességgel. Ezt a sebességet a felhőre irányuló internetes sávszélesség is érintheti.</li><li>A többhelyes kötetektől eltérően a helyileg rögzített kötetek esetében a köteten tárolt adatmennyiségek is helyileg letöltődnek az eszközön. A visszaállítási művelet akkor fejeződik be, amikor az összes kötetre vonatkozó adattal bekerült az eszközre.</li><li>Előfordulhat, hogy a visszaállítási műveletek hosszúak. A visszaállítás befejezésének teljes ideje a kiépített helyi kötet méretétől, az internetes sávszélességtől és az eszközön lévő adatoktól függ. Ha a visszaállítási művelet folyamatban van, a helyileg rögzített köteten lévő biztonsági mentési műveletek engedélyezettek. |
| Felhőbeli Pillanatképek feldolgozási sebessége |15 perc/TB |<ul><li>A Felhőbeli pillanatkép feltöltésre való felkészülésének minimális ideje, a biztonsági mentésben foglalt mennyiségi adatok száma (TB). </li><li> A Felhőbeli pillanatképek teljes időpontját a rendszer a pillanatkép feltöltési idejére való hozzáadásával számítja ki, amelyet az Internet-sávszélesség a felhőben érint. |
| Az ügyfél olvasási/írási sebességének maximális sebessége (az SSD-szintjéről kiszolgált) * |920/720 MB/s egyetlen 10 GbE hálózati adapterrel |Legfeljebb 2x az MPIO-vel és két hálózati adapterrel. |
| Az ügyfél olvasási/írási sebességének maximális sebessége (a HDD-szinten kiszolgált) * |120/250 MB/s | |
| Az ügyfél olvasási/írási sebességének maximális sebessége (a felhő szintjéről kiszolgált) * a 3. és újabb verziók esetében * * |40/60 MB/s a lépcsőzetes kötetek esetében<br><br>60/80 MB/s a kötet létrehozásakor kiválasztott archiválási lehetőséggel rendelkező, lépcsőzetes kötetek esetében |Az olvasási sebesség a megfelelő I/O-várólista mélységét generáló és fenntartó ügyfelektől függ. <br><br>Az elért sebesség a mögöttes Storage-fiók sebességétől függ. |

Az I/O-típusok maximális átviteli sebességét 100%-os olvasási és 100%-os írási forgatókönyvek alapján mérjük. &#42; A tényleges átviteli sebesség lehet alacsonyabb, és az I/O-vegyes és a hálózati feltételektől függ.

Előfordulhat, hogy a 3. frissítés előtt &#42;&#42; teljesítmények száma alacsonyabb lehet.

## <a name="next-steps"></a>Következő lépések
Tekintse át a [StorSimple rendszerkövetelményeit](storsimple-8000-system-requirements.md).

