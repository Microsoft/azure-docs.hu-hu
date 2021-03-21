---
title: StorSimple helyileg rögzített kötetek – GYIK | Microsoft Docs
description: A StorSimple helyileg rögzített kötetekkel kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2017
ms.author: manuaery
ms.openlocfilehash: 483fa81b409e1bd740af85b431a86b6c814831e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002714"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple, helyileg rögzített kötetek: gyakori kérdések (GYIK)
## <a name="overview"></a>Áttekintés
A következő kérdések és válaszok láthatók, ha a StorSimple helyileg rögzített kötetet hoz létre, valamint egy többkötetes kötetet egy helyileg rögzített kötetre (és fordítva), vagy egy helyileg rögzített kötet biztonsági mentésére és visszaállítására.

A kérdések és válaszok a következő kategóriákba vannak rendezve

* Helyileg rögzített kötet létrehozása
* Helyileg rögzített biztonsági mentés
* Lépcsőzetes kötet átalakítása helyileg rögzített kötetre
* Helyileg rögzített kötet visszaállítása
* Feladatátvétel helyileg rögzített köteten

## <a name="questions-about-creating-a-locally-pinned-volume"></a>A helyileg rögzített kötetek létrehozásával kapcsolatos kérdések
**Q.** Mekkora az 8000 sorozatú eszközökön létrehozható helyileg rögzített kötet maximális mérete?

**A StorSimple** 8000 Series Update 3,0-es verzióját futtató eszközökön helyileg rögzített köteteket hozhat létre akár 8,5 TB-ig, akár 200 TB-ig, a 8100-es eszközön. A nagyobb 8600-as eszközön gyors helyi kötetekhez legfeljebb 22,5 TB, rétegzett kötetekhez legfeljebb 500 TB területet oszthat ki.

**Q.** Nemrég frissítettem a 8100-es eszközt a 3,0-es frissítéshez, és amikor egy helyileg rögzített kötetet próbálok létrehozni, a maximálisan megengedett méret csak 6 TB és nem 8,5 TB. Miért nem hozható létre 8,5 TB-os kötet?

**Ha az eszközön a 3,0** -es frissítés fut, helyileg rögzített köteteket akár 8,5 TB-ig, akár 200 TB-ra is kiépítheti a 8100 eszközön. Ha az eszközön már vannak lépcsőzetes kötetek, akkor a helyileg rögzített kötetek létrehozásához rendelkezésre álló terület arányos lesz ennél a maximális korlátnál. Ha például az 8100-eszközön (amely a többplatformos kapacitás felében) már megközelítőleg 106 TB-os mennyiségű kötet lett kiépítve, akkor a 8100-eszközön létrehozható helyi kötetek maximális mérete a 4 TB-ra lesz csökkentve (ez nagyjából a maximálisan rögzített kötet kapacitása).

Mivel a többhelyes kötetek munkakészletének üzemeltetéséhez az eszközön egy helyi terület van használatban, a helyileg rögzített kötetek létrehozásához rendelkezésre álló terület csökken, ha az eszköz többkötetes kötetekkel rendelkezik. Ezzel szemben a helyileg rögzített kötet létrehozása a lépcsőzetes kötetek számára is csökkenti a rendelkezésre álló területet. A következő táblázatok összefoglalják a 8100-es és a 8600-es, a helyileg rögzített kötetek létrehozásakor rendelkezésre álló, rétegű kapacitást.

#### <a name="update-30"></a>3,0-es frissítés 

| Helyileg rögzített kötetek kiépített kapacitása | A többrétegű kötetek számára kiépíthető kapacitás – 8100 | A többrétegű kötetek számára kiépíthető kapacitás – 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176,5 TB |477,8 TB |
| 4 TB |105,9 TB |411,1 TB |
| 8,5 TB |0 TB |311,1 TB |
| 10 TB |NA |277,8 TB |
| 15 TB |NA |166,7 TB |
| 22,5 TB |NA |0 TB |

**Q.** Miért van egy hosszú ideig futó művelet a helyileg rögzített kötet létrehozásakor?

**Egy.** A helyileg rögzített kötetek sűrűn vannak kiépítve. Ha helyet szeretne létrehozni az eszköz helyi szintjein, előfordulhat, hogy a meglévő rétegű kötetek egyes adatait a rendszer a létesítési folyamat során leküldi a felhőbe. Mivel ez a kiépített kötet méretétől, az eszközön lévő meglévő adatoktól és a felhő számára elérhető sávszélességtől függ, a helyi kötetek létrehozásához szükséges idő több óra is lehet.

**Q.** Mennyi ideig tart egy helyileg rögzített kötet létrehozása?

**Egy.** Mivel a helyileg rögzített kötetek dinamikusan vannak kiépítve, a kiépítési folyamat során előfordulhat, hogy a lépcsőzetes kötetek bizonyos meglévő adatait a rendszer leküldi a felhőbe. Ezért a helyileg rögzített kötetek létrehozásához szükséges idő több tényezőtől függ, többek között a kötet méretétől, az eszközön lévő adatoktól és a rendelkezésre álló sávszélességtől. Egy olyan, frissen telepített eszközön, amely nem tartalmaz köteteket, a helyileg rögzített kötetek létrehozásának ideje körülbelül 10 perc/terabájt. A helyi kötetek létrehozása azonban több órát is igénybe vehet, a használatban lévő eszközön a fent ismertetett tényezők alapján.

**Q.** Helyileg rögzített kötetet szeretnék létrehozni. Vannak olyan ajánlott eljárások, amelyekről tisztában kell lennie?

**Egy.** A helyileg rögzített kötetek olyan számítási feladatokhoz alkalmasak, amelyek mindig helyi garanciát igényelnek, és érzékenyek a Felhőbeli késésekre. A helyi kötetek bármely számítási feladathoz való használatának megfontolása során vegye figyelembe a következőket:

* A helyileg rögzített kötetek kiosztása sűrűn történik, és a helyi kötetek létrehozása hatással van a felhasználható kötetek szabad területére. Ezért javasoljuk, hogy kisebb méretű kötetekkel kezdjen el, és vertikális felskálázást, mivel a tárolási követelmények megnövekednek.
* A helyi kötetek kiosztása hosszú ideig tartó művelet, amely a már meglévő adatoknak a lépcsőzetes kötetekről a felhőbe való leküldését is magában foglalja. Ennek eredményeképpen a kötetek kevesebb teljesítményt tapasztalhatnak.
* A helyi kötetek kiépített ideje egy időigényes művelet. A tényleges idő több tényezőtől függ: a kiépített kötet méretétől, az eszközön lévő adatoktól és a rendelkezésre álló sávszélességtől. Ha nem készített biztonsági másolatot a meglévő kötetekről a felhőbe, akkor a kötetek létrehozása lassabb. Javasoljuk, hogy a helyi kötetek kiépítése előtt készítsen Felhőbeli pillanatképeket a meglévő kötetekről.
* A meglévő rétegű kötetek a helyileg rögzített kötetekre konvertálhatók, és ez az átalakítás magában foglalja az eszköz tárterületének kiépítését az eredményül kapott helyileg rögzített kötetre (a többhelyes adatoknak a felhőből történő leállítása mellett). Ez egy hosszú ideig futó művelet, amely a fent ismertetett tényezőktől függ. Javasoljuk, hogy az átalakítás előtt biztonsági másolatot készít a meglévő kötetekről, mivel a folyamat akkor is lassabb lesz, ha a meglévő kötetek nem rendelkeznek biztonsági mentéssel. Az eszköz ennél a folyamatnál is csökkentheti a teljesítményt.

További információ a [helyileg rögzített kötetek létrehozásáról](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**Q.** Több helyileg rögzített kötet is létrehozható egyszerre?

**Egy.** Igen, de a helyileg rögzített kötet-létrehozási és bővítési feladatok egymás után lesznek feldolgozva.

A helyileg rögzített kötetek kiosztása sűrűn történik, és ehhez helyi tárterületet kell létrehozni az eszközön (ami azt eredményezheti, hogy a kiépítési folyamat során a rendszer leküldi a lépcsőzetes kötetek meglévő adatait a felhőbe). Ezért ha egy kiépítési feladat folyamatban van, a rendszer a többi helyi kötet-létrehozási feladatot a várólistára helyezi, amíg a feladat be nem fejeződik.

Hasonlóképpen, ha egy meglévő helyi kötet kibontása folyamatban van, vagy egy többsoros kötetet helyez át egy helyileg rögzített kötetre, akkor az új helyileg rögzített kötet létrehozása a várólistára kerül, amíg az előző feladatot be nem fejeződik. Egy helyileg rögzített kötet méretének kibővítése magában foglalja az adott kötethez tartozó meglévő helyi terület kiterjesztését. A többrétegűről helyileg rögzített kötetre történő átalakítás is magában foglalja az eredményül kapott helyileg rögzített kötet helyi területének létrehozását is. Mindkét művelet során a helyi terület létrehozása vagy bővítése hosszú ideig futó feladatot eredményez.

Ezek a feladatok a StorSimple Eszközkezelő szolgáltatás **feladatok** paneljén tekinthetők meg. Az aktívan feldolgozott feladatot folyamatosan frissíti a rendszer, hogy tükrözze a lemezterület-kiépítés folyamatát. A fennmaradó helyileg rögzített mennyiségi feladatok futásnak vannak megjelölve, de a folyamatuk elakadt, és a várólistán lévő sorrendben lesznek kiválasztva.

**Q.** Egy helyileg rögzített kötetet töröltem. Miért nem látható a visszaigényelt terület a rendelkezésre álló helyen, amikor megpróbálok új kötetet létrehozni?

**Egy.** Ha töröl egy helyileg rögzített kötetet, előfordulhat, hogy az új kötetek számára elérhető terület nem frissül azonnal. A StorSimple Eszközkezelő szolgáltatás körülbelül óránként frissíti a helyi helyet. Javasoljuk, hogy várjon egy órát, mielőtt megpróbálja létrehozni az új kötetet.

**Q.** A felhőalapú berendezés támogatja a helyileg rögzített kötetek használatát?

**Egy.** A helyileg rögzített kötetek nem támogatottak a felhőalapú berendezésen (8010-es és 8020-es eszközök, korábban StorSimple virtuális eszköz néven).

**Q.** Használhatom a Azure PowerShell parancsmagokat helyileg rögzített kötetek létrehozásához és kezeléséhez?

**Egy.** Nem, a helyileg rögzített kötetek nem hozhatók létre Azure PowerShell-parancsmagokon keresztül (a Azure PowerShell-on keresztül létrehozott köteteket a rendszer rétegű). Azt is javasoljuk, hogy ne használja a Azure PowerShell-parancsmagokat a helyileg rögzített kötetek tulajdonságainak módosításához, mivel a kötet típusának nem megfelelő hatása lesz a lépcsőzetesen.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>A helyileg rögzített kötetek biztonsági mentésével kapcsolatos kérdések
**Q.** Támogatottak-e a helyileg rögzített kötetek helyi pillanatképei?

**Egy.** Igen, helyi pillanatképeket készíthet a helyileg rögzített kötetekről. Azt javasoljuk azonban, hogy rendszeresen biztonsági másolatot készít a helyileg rögzített kötetekről a Felhőbeli pillanatképekkel, így biztosítva, hogy az adatai a vészhelyzetek esetén is védve legyenek.

Ne feledje, hogy a helyileg rögzített kötetek helyi pillanatképei is a felhőbe kerülnek, és nem garantáltak, hogy az eszköz helyi szintjében maradnak.

**Q.** Vannak irányelvek a helyileg rögzített kötetekhez tartozó helyi Pillanatképek kezeléséhez?

**Egy.** A helyileg rögzített köteten található nagy adatváltozások mellett előforduló gyakori helyi Pillanatképek miatt előfordulhat, hogy az eszközön a helyi tárterületet gyorsan felhasználják, és a lépcsőzetes kötetek adatainak a felhőbe való leküldését eredményezik. Ezért javasoljuk, hogy csökkentse a helyi Pillanatképek számát.

**Q.** Riasztást kaptam arról, hogy a helyileg rögzített kötetek helyi pillanatképei érvénytelenítve lehetnek. Mikor fordulhat elő?

**Egy.** A helyileg rögzített köteten a nagy adatváltozások mellett előforduló gyakori helyi Pillanatképek az eszközön a helyi tárterület gyors felhasználását okozhatják. Ha az eszköz helyi szintjei nagy mértékben használatban vannak, a kiterjesztett felhő meghibásodása miatt előfordulhat, hogy az eszköz megtelik, és a kötetre való bejövő írások érvénytelenné tehetik a pillanatképeket (mivel a pillanatképek frissítése nem történik meg, hogy a korábbi, felülírt adatblokkokra hivatkozzon). Ebben az esetben a kötetre írt írások továbbra is kiszolgálva lesznek, de a helyi Pillanatképek érvénytelenek lehetnek. Nincs hatással a meglévő Felhőbeli pillanatképekre.

A riasztás arra figyelmeztet, hogy ilyen helyzet merülhet fel, és a helyi Pillanatképek időpontjának áttekintésével megtekintheti, hogy kevésbé gyakori helyi pillanatképeket szeretne, vagy törli a már nem szükséges régebbi helyi pillanatképeket.

Ha a helyi Pillanatképek érvénytelenítve lettek, a rendszer értesítést küld arról, hogy az adott biztonsági mentési szabályzat helyi pillanatképei érvénytelenítve lettek az érvénytelenített helyi Pillanatképek időbélyegzője mellett. Ezeket a pillanatképeket automatikusan törli a rendszer, és nem fogja tudni megtekinteni őket a Azure Portal **biztonsági mentési katalógusok** paneljén.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>A többszintes kötetek egy helyileg rögzített kötetre való átalakításával kapcsolatos kérdések
**Q.** A többkötetes kötetek egy helyileg rögzített kötetre való konvertálása során némi lassulást figyelek az eszközön. Miért történik ez?

**Egy.** Az átalakítási folyamat két lépésből áll:

1. Az eszközön lévő hely kiépítés a hamarosan átalakított, helyileg rögzített kötetre.
2. A felhőből származó, többszintes adatok letöltése a helyi garanciák biztosításához.

Mindkét lépés olyan hosszú ideig futó művelet, amely az átalakított kötet méretétől, az eszközön lévő adatoktól és a rendelkezésre álló sávszélességtől függ. Mivel a meglévő rétegű kötetek adatai a kiépítési folyamat részeként a felhőbe kerülhetnek, az eszköz ebben az időszakban csökkentheti a teljesítményt. Emellett az átalakítási folyamat lassabb lehet, ha:

* A meglévő kötetek biztonsági mentése nem történt meg a felhőben. Ezért javasoljuk, hogy az átalakítás elindítása előtt készítsen biztonsági másolatot a kötetekről.
* A sávszélesség-szabályozási házirendek alkalmazása megtörtént, ami korlátozhatja a rendelkezésre álló sávszélességet a felhőre. Ezért javasoljuk, hogy egy dedikált 40 Mbps vagy több kapcsolódási lehetőséget biztosítson a felhőhöz.
* Az átalakítási folyamat több órát is igénybe vehet a fent ismertetett tényezők miatt; Ezért javasoljuk, hogy a műveletet a nem csúcsok idején vagy egy hétvégén hajtsa végre, hogy elkerülje a végfelhasználók hatását.

További információ a [többplatformos kötetek egy helyileg rögzített kötetre való átalakításáról](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**Q.** Törölhetem a kötet-átalakítási műveletet?

**Egy.** Nem, a megszakítja az átalakítási műveletet a kezdeményezést követően. Az előző kérdésben leírtaknak megfelelően vegye figyelembe, hogy milyen teljesítménnyel kapcsolatos problémák merülhetnek fel a folyamat során, és kövesse a fent felsorolt ajánlott eljárásokat az átalakítás tervezésekor.

**Q.** Mi történik a kötettel, ha az átalakítási művelet meghiúsul?

**Egy.** A kötetek konvertálása a felhőalapú kapcsolódási problémák miatt sikertelen lehet. Előfordulhat, hogy az eszköz végül leállítja az átalakítási folyamatot, miután a felhőből nem sikerült leállítani a többszintes adatmennyiséget. Ilyen esetben a kötet típusa továbbra is a forrás kötet típusa lesz a konverzió előtt, és:

* A rendszer kritikus riasztást küld a kötet-átalakítási hibákról. További információ a [helyileg rögzített kötetekkel kapcsolatos riasztásokról](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Ha egy többplatformos, helyileg rögzített kötetre konvertál, a kötet továbbra is a többkötetes kötetek tulajdonságait fogja tartalmazni, mivel az adatforgalom továbbra is a felhőben található. Javasoljuk, hogy hárítsa el a kapcsolódási problémákat, majd próbálja megismételni az átalakítási műveletet.
* Hasonlóképpen, ha egy lépcsőzetesen rögzített kötetre való áttérés nem sikerül, bár a kötetet helyileg rögzített kötetként jelöli meg a rendszer, akkor az egy lépcsőzetes kötetként fog működni (mivel az adatok kikerülhetnek a felhőbe). Azonban továbbra is helyet foglal az eszköz helyi szintjein. Ez a terület nem lesz elérhető más helyileg rögzített kötetekhez. Javasoljuk, hogy próbálja megismételni a műveletet annak érdekében, hogy a kötet-átalakítás befejeződik, és az eszközön lévő helyi terület visszaigénylése megtörténjen.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>A helyileg rögzített kötetek visszaállításával kapcsolatos kérdések
**Q.** A helyileg rögzített kötetek azonnal visszaállíthatók?

**Egy.** Igen, a helyileg rögzített kötetek azonnal visszaállíthatók. Amint a kötet metaadat-információit a felhőből a visszaállítási művelet részeként lekéri a rendszer, a kötet online állapotba kerül, és a gazdagép is elérhetővé válik. A mennyiségi adatokra vonatkozó helyi garanciák azonban nem jelennek meg addig, amíg az összes adat le nem töltődik a felhőből, és a visszaállítás időtartama alatt a kötetek csökkenthetik a teljesítményt.

**Q.** Mennyi ideig tart egy helyileg rögzített kötet visszaállítása?

**Egy.** A helyileg rögzített kötetek azonnal visszaállnak és online állapotba kerülnek, amint a kötet metaadat-információit lekérik a felhőből, miközben a kötet adatai továbbra is le lesznek töltve a háttérben. A visszaállítási művelet utolsó része – a mennyiségi adatmennyiségre vonatkozó helyi garanciák visszaszerzése – hosszú ideig futó művelet, és az összes, a helyi állapotba kerülő eszközre több óráig is eltarthat. A végrehajtáshoz szükséges idő több tényezőtől függ, például a visszaállítani kívánt kötet méretétől és a rendelkezésre álló sávszélességtől. Ha a visszaállítani kívánt eredeti kötet törölve lett, a rendszer további időt vesz igénybe, hogy létrehozza a helyi területet az eszközön a visszaállítási művelet részeként.

**Q.** Vissza kell állítania a meglévő helyileg rögzített kötetet egy régebbi pillanatképre (amelyet a rendszer a kötetre bontva). A kötetet ebben az esetben a rendszer rétegűként állítja vissza?

**Egy.** Nem, a kötet helyileg rögzített kötetként lesz visszaállítva. Bár a pillanatkép időpontját a kötet kiosztásának időpontjában, a meglévő kötetek visszaállítása közben a StorSimple mindig a lemezen lévő kötet típusát használja, mivel jelenleg már létezik.

**Q.** Nemrég bővítettem a helyileg rögzített kötetet, de most újra kell visszaállítani az adatmennyiséget, amikor a kötet mérete kisebb volt. Visszaállítja az aktuális kötet átméretezését, és a visszaállítás befejeződése után ki kell bővíteni a kötet méretét?

**Egy.** Igen, a visszaállítás átméretezi a kötetet, és a visszaállítás befejezése után ki kell bővíteni a kötet méretét.

**Q.** Módosíthatom a kötetek típusát a visszaállítás során?

**Egy.** Nem, a kötet típusa nem módosítható a visszaállítás során.

* A törölt kötetek a pillanatképben tárolt típusként lesznek visszaállítva.
* A meglévő kötetek a jelenlegi típusuk alapján állíthatók vissza, a pillanatképben tárolt típustól függetlenül (lásd az előző két kérdést).

**Q.** Helyre kell állítania a helyileg rögzített kötetet, de helytelen időpontot választottam a pillanatképben. Törölhetem a jelenlegi visszaállítási műveletet?

**Egy.** Igen, megszakíthatja a folyamatban lévő visszaállítási műveletet. A kötet állapota visszaáll a visszaállítás elején lévő állapotba. Azonban a köteten végrehajtott összes írás elvész, miközben a visszaállítás folyamatban van.

**Q.** Elindítottam egy visszaállítási műveletet az egyik helyileg rögzített köteten, és most meglátok egy pillanatképet a várakozó fájlok katalógusában, amely nem gyűjti újra a létrehozást. Mire használható?

**Egy.** Ez az ideiglenes pillanatkép, amely a visszaállítási művelet előtt jön létre, és a visszaállításhoz használatos, ha a visszaállítást megszakították vagy sikertelenek. Ne törölje ezt a pillanatképet; a visszaállítás befejezésekor a rendszer automatikusan törli. Ez akkor fordulhat elő, ha a visszaállítási feladatokhoz csak a helyileg rögzített kötetek vagy a helyileg rögzített és a lépcsőzetes kötetek kombinációja tartozik. Ha a visszaállítási feladatokban csak a lépcsőzetes kötetek szerepelnek, akkor ez a viselkedés nem fog történni.

**Q.** Megtehetem egy helyileg rögzített kötet klónozását?

**Egy.** Igen, írhat. A helyileg rögzített kötet azonban alapértelmezés szerint rétegű kötetként lesz klónozott. További információ a [helyileg rögzített kötetek klónozásáról](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>A helyileg rögzített kötetek feladatátvételével kapcsolatos kérdések
**Q.** Az eszközem feladatátvételét egy másik fizikai eszközre kell átadni. A helyileg rögzített kötetek feladatátvétele helyileg rögzített vagy lépcsőzetes módon történik?

**Egy.** A helyileg rögzített kötetek feladatátvétele helyileg rögzítettként történik, ha a célként megadott eszközön a StorSimple 8000 Series Update 3 vagy újabb verziója fut.

További információ a [feladatátvételről és a helyileg rögzített kötetekről a verziók között](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**Q.** A rendszer azonnal visszaállítja a helyileg rögzített köteteket a vész-helyreállítás során (DR)?

**Egy.** Igen, a helyileg rögzített kötetek azonnal visszaállíthatók a feladatátvétel során. Amint a kötet metaadat-információi a felhőből a feladatátvételi művelet részeként vannak leképezve, a kötet online állapotba kerül a célszámítógépen, és a gazdagép is hozzáférhet. Eközben a kötet adatai továbbra is le lesznek letöltve a háttérben, és a feladatátvétel időtartama alatt a kötetek alacsonyabb teljesítményt tapasztalhatnak.

**Q.** Meglátom a feladatátvételi feladatot, Hogyan követhetem nyomon a megcélzott eszközön visszaállítani kívánt helyileg rögzített kötet állapotát?

**Egy.** Feladatátvételi művelet során a feladatátvételi feladatot a rendszer befejezettként jelöli meg, ha a feladatátvételi készlet összes kötetét azonnal visszaállították, és online állapotba állították a céleszköz. Ebbe beletartozik minden olyan helyileg rögzített kötet, amely feladatátvételt okozhatott; azonban a helyi garanciák csak akkor lesznek elérhetők, ha a kötethez tartozó összes adattal letöltötték. A feladatátvétel során létrehozott megfelelő visszaállítási feladatok figyelésével nyomon követheti ezt az előrehaladást minden olyan helyileg rögzített kötet esetében, amelyet átvettek. Ezek az egyéni visszaállítási feladatok csak a helyileg rögzített kötetekhez lesznek létrehozva.

**Q.** Módosíthatom a kötetek típusát a feladatátvétel során?

**Egy.** Nem, a kötet típusát nem lehet a feladatátvétel során módosítani. Ha egy StorSimple 8000 Series Update 3 rendszert futtató másik fizikai eszközre végez feladatátvételt, a kötetek feladatátvétele a pillanatképben tárolt kötet típusa alapján történik.

**Q.** Végezhetek feladatátvételt egy helyileg rögzített kötettel rendelkező mennyiségi tárolóban a felhőalapú készüléken?

**Egy.** Igen, írhat. A helyileg rögzített kötetek feladatait a rendszer lépcsőzetes kötetekként adja át. További információ a [feladatátvételről és a helyileg rögzített kötetekről a verziók között](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

