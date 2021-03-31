---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67179517"
---
| Korlátazonosító | Korlát | Megjegyzések |
| --- | --- | --- |
| A Storage-fiók hitelesítő adatainak maximális száma |64 | |
| Mennyiségi tárolók maximális száma |64 | |
| Kötetek maximális száma |255 | |
| Az ütemtervek maximális száma sávszélesség-sablon alapján |168 |Minden órában, a hét minden napján. |
| A rétegű kötetek maximális mérete fizikai eszközökön |64 TB StorSimple 8100 és StorSimple 8600 |A StorSimple 8100 és a StorSimple 8600 fizikai eszközök. |
| Rétegekből álló kötetek maximális mérete az Azure-ban virtuális eszközökön |30 TB a StorSimple 8010 <br></br> 64 TB a StorSimple 8020 |A StorSimple 8010 és a StorSimple 8020 az Azure-beli virtuális eszközök, amelyek standard szintű tárhelyet és Premium Storage-t használnak. |
| Egy helyileg rögzített kötet maximális mérete fizikai eszközökön |9 TB a StorSimple 8100 <br></br> 24 TB a StorSimple 8600 |A StorSimple 8100 és a StorSimple 8600 fizikai eszközök. |
| ISCSI-kapcsolatok maximális száma |512 | |
| A kezdeményezők által létesített iSCSI-kapcsolatok maximális száma |512 | |
| Hozzáférés-vezérlési rekordok maximális száma eszközönként |64 | |
| Kötetek maximális száma biztonsági mentési házirend szerint |24 | |
| Biztonsági mentési szabályzatban megőrzött biztonsági másolatok maximális száma |64 | |
| Ütemezett biztonsági mentési szabályzatok maximális száma |10 | |
| A köteten megőrzött bármilyen típusú Pillanatképek maximális száma |256 |Ez az összeg helyi pillanatképeket és Felhőbeli pillanatképeket tartalmaz. |
| Bármely eszközön megtalálható Pillanatképek maximális száma |10,000 | |
| A biztonsági mentéshez, visszaállításhoz vagy klónozáshoz párhuzamosan feldolgozható kötetek maximális száma |16 |<ul><li>Ha a rendszer több mint 16 kötetet használ fel, a feldolgozási pontok válnak elérhetővé.</li><li>Egy klónozott vagy visszaállított kötet új biztonsági mentése nem hajtható végre, amíg a művelet be nem fejeződik. Helyi kötet esetén a biztonsági mentések a kötet online állapotának engedélyezése után engedélyezettek.</li></ul> |
| A biztonsági mentés és a klónozás visszaszerzési ideje a többplatformos kötetek esetében |<2 perc |<ul><li>A kötet egy visszaállítási vagy klónozási művelettől számított 2 percen belül elérhetővé válik, a kötet méretétől függetlenül.</li><li>A kötet teljesítménye kezdetben lassabb lehet a normál értéknél, mivel a legtöbb adat és metaadatok a felhőben maradnak. A teljesítmény növelheti a felhőből a StorSimple-eszközre irányuló adatforgalom mennyiségét.</li><li>A metaadatok letöltésének teljes ideje a lefoglalt kötet méretétől függ. A metaadatokat a rendszer automatikusan a háttérbe helyezi az eszközön, a lefoglalt mennyiségi adatokhoz tartozó TB-onként 5 perces sebességgel. Ezt a sebességet a felhőre irányuló internetes sávszélesség is érintheti.</li><li>A visszaállítási vagy klónozási művelet akkor fejeződik be, amikor az összes metaadat az eszközön található.</li><li>A biztonsági mentési műveletek nem hajthatók végre, amíg a visszaállítási vagy a klónozási művelet teljes mértékben nem fejeződött be. |
| A helyreállítás ideje a helyileg rögzített kötetek esetében |<2 perc |<ul><li>A kötet a visszaállítási művelettől számított 2 percen belül elérhetővé válik, a kötet méretétől függetlenül.</li><li>A kötet teljesítménye kezdetben lassabb lehet a normál értéknél, mivel a legtöbb adat és metaadatok a felhőben maradnak. A teljesítmény növelheti a felhőből a StorSimple-eszközre irányuló adatforgalom mennyiségét.</li><li>A metaadatok letöltésének teljes ideje a lefoglalt kötet méretétől függ. A metaadatokat a rendszer automatikusan a háttérbe helyezi az eszközön, a lefoglalt mennyiségi adatokhoz tartozó TB-onként 5 perces sebességgel. Ezt a sebességet a felhőre irányuló internetes sávszélesség is érintheti.</li><li>A többhelyes kötetektől eltérően, ha vannak helyileg rögzített kötetek, a köteten tárolt adatmennyiségek is helyileg letöltődnek az eszközön. A visszaállítási művelet akkor fejeződik be, amikor az összes kötetre vonatkozó adattal bekerült az eszközre.</li><li>Előfordulhat, hogy a visszaállítási műveletek hosszúak lesznek, és a visszaállítás teljes ideje a kiépített helyi kötet méretétől, az internetes sávszélességtől és az eszközön lévő adatoktól függ. Ha a visszaállítási művelet folyamatban van, a helyileg rögzített köteten lévő biztonsági mentési műveletek engedélyezettek. |
| A rendelkezésre állás visszaállítása |Legutóbbi feladatátvétel | |
| Az ügyfél olvasási/írási sebességének maximális sebessége az SSD-szinten * |920/720 MB/s egyetlen 10 Gigabit Ethernet hálózati adapterrel |Legfeljebb kétszer az MPIO-vel és két hálózati adapterrel. |
| Az ügyfél olvasási/írási sebességének maximális sebessége, ha a HDD-szinten található * |120/250 MB/s | |
| Az ügyfél olvasási/írási sebességének maximális sebessége a felhő szintjéről kiszolgálva * |11/41 MB/s |Az olvasási sebesség a megfelelő I/O-várólista mélységét generáló és fenntartó ügyfelektől függ. |

Az I/O-típusok maximális átviteli sebességét 100%-os olvasási és 100%-os írási forgatókönyvek alapján mérjük. &#42; A tényleges átviteli sebesség lehet alacsonyabb, és az I/O-vegyes és a hálózati feltételektől függ.

