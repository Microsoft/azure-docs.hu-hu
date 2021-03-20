---
title: Ügyféloldali teljesítménykövetés létrehozása
description: Ajánlott eljárások az ügyféloldali teljesítmény-profilkészítéshez a WPF használatával
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1d4ce68bdda5fbc3dfdb7396141289a58dab5bd1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92204095"
---
# <a name="create-client-side-performance-traces"></a>Ügyféloldali teljesítménykövetés létrehozása

Számos oka lehet annak, hogy az Azure-alapú távoli renderelés teljesítménye miért nem megfelelő a kívánt módon. A felhő-kiszolgáló tiszta renderelési teljesítménye mellett különösen a hálózati kapcsolatok minősége jelentős hatással van a felhasználói élményre. A kiszolgáló teljesítményének [megkereséséhez](../overview/features/performance-queries.md)tekintse meg a kiszolgálóoldali teljesítménnyel kapcsolatos lekérdezések című fejezetet.

Ez a fejezet a lehetséges ügyféloldali szűk keresztmetszetek azonosítására összpontosít *:::no-loc text="performance traces":::* .

## <a name="getting-started"></a>Első lépések

Ha még nem ismeri a Windows :::no-loc text="performance tracing"::: funkcionalitását, ez a szakasz ismerteti a legfontosabb feltételeket és alkalmazásokat, amelyekkel elsajátíthatja az első lépéseket.

### <a name="installation"></a>Telepítés

Az ARR-vel való nyomkövetéshez használt alkalmazások általános célú eszközök, amelyek minden Windows-fejlesztéshez használhatók. Ezek a [Windows Performance Toolkit](/windows-hardware/test/wpt/)segítségével érhetők el. Az eszközkészlet beszerzéséhez töltse le a [Windows Assessment and Deployment Kit csomagot](/windows-hardware/get-started/adk-install).

### <a name="terminology"></a>Terminológia

A teljesítmény-nyomkövetéssel kapcsolatos információk keresésekor a rendszer elkerülhetetlenül megkeresi a különböző feltételeket. A legfontosabbak a következők:

* `ETW`
* `ETL`
* `WPR`
* `WPA`

A **ETW** az [ **E**-  indows](/windows/win32/etw/about-event-tracing). Ez egyszerűen a Windows rendszerbe épített, hatékony kernel szintű nyomkövetési létesítmény átfogó neve. Ez az *esemény* -nyomkövetés, mivel a ETW támogató alkalmazások olyan naplózási műveletekhez bocsátanak ki eseményeket, amelyek segíthetnek a teljesítménnyel kapcsolatos problémák nyomon követésében. Alapértelmezés szerint az operációs rendszer már elküldi az eseményeket olyan dolgokhoz, mint a lemez-hozzáférés, a feladathoz tartozó kapcsolók és az ilyen. Az olyan alkalmazások, mint az ARR, egyéni eseményeket bocsátanak ki, például az eldobott kereteket, a hálózati késést stb.

Az **ETL** az **E** Vent **T** Race **L** ogging áll. Ez egyszerűen azt jelenti, hogy egy nyomkövetést gyűjtöttek (naplózott), ezért általában a nyomkövetési adatokat tároló fájlok fájlkiterjesztésként használatosak. Így ha nyomkövetést végez, általában egy. etl-fájllal fog rendelkezni \* .

A **WPR** a [ **W** indows **P** erformance **R** ecorder](/windows-hardware/test/wpt/windows-performance-recorder) értékre áll, és az az alkalmazás neve, amely elindítja és leállítja az esemény-Nyomkövetések rögzítését. A WPR egy profilt ( \* . wprp) használ, amely a naplózni kívánt eseményeket konfigurálja. Egy ilyen `wprp` fájl az ARR SDK-val van megadva. Ha egy asztali számítógépen nyomkövetést végez, a WPR közvetlenül is elindíthatja. Amikor nyomkövetést végez a HoloLens, általában a webes felületen halad át.

A **WPA** a [ **W** indows **P** erformance](/windows-hardware/test/wpt/windows-performance-analyzer) , a nalyzer pedig az \* . etl fájlok megnyitásához és a teljesítménnyel kapcsolatos problémák azonosításához használt gui-alkalmazás neve. A WPA lehetővé teszi, hogy különböző feltételek alapján rendezze az adatokat, több módon jelenítse meg az adatokat, részletezve a részleteket, és korrelálja az adatokat.

Míg az ETL-nyomkövetés bármely Windows-eszközön (helyi számítógépen, HoloLens, felhőalapú kiszolgálón stb.) hozható létre, a rendszer általában lemezre menti, és az asztali SZÁMÍTÓGÉPeken lévő WPA használatával elemzi őket. Az ETL-fájlokat más fejlesztőknek is elküldhetik, hogy lássák. Ügyeljen arra, hogy a bizalmas adatokat, például a fájlelérési utakat és az IP-címeket az ETL-nyomkövetésekben is rögzítheti. A ETW kétféleképpen is használhatja: Nyomkövetések rögzítésére vagy a Nyomkövetések elemzésére. A rögzítési Nyomkövetések közvetlen továbbítást igényelnek, és minimális beállításra van szükség. A nyomkövetési adatok elemzéséhez szükség van a WPA-eszköz és a vizsgált probléma tisztességes megismerésére. A WPA learning általános tananyaga alább található, valamint útmutatást ad az ARR-specifikus Nyomkövetések értelmezéséhez.

## <a name="recording-a-trace-on-a-local-pc"></a>Nyomkövetés rögzítése helyi számítógépen

Az ARR teljesítménybeli problémák azonosításához érdemes közvetlenül egy HoloLens elvégezni a nyomkövetést, mivel ez az egyetlen lehetőség, hogy pillanatképet kapjon a valódi teljesítmény jellemzőiről. Ha azonban kifejezetten a HoloLens teljesítmény-korlátozásai nélkül szeretne nyomkövetést végezni, vagy ha szeretné megismerni a WPA használatát, és nem kell reális nyomkövetést végeznie, itt megtudhatja, hogyan teheti meg.

### <a name="wpr-configuration"></a>WPR-konfiguráció

1. Indítsa el a alkalmazást [:::no-loc text="Windows Performance Recorder":::](/windows-hardware/test/wpt/windows-performance-recorder) a *Start menüből*.
1. **További lehetőségek** kibontása
1. Kattintson a **profilok hozzáadása...** elemre.
1. Válassza ki a *AzureRemoteRenderingNetworkProfiling. wprp* fájlt. Ez a fájl az ARR SDK-ban található az *eszközök/ETLProfiles* területen.
   A profil mostantól az *Egyéni mérések* területen jelenik meg a WPR. Győződjön meg arról, hogy az egyetlen engedélyezett profil.
1. *Első szintű osztályozás* kibontása:
    * Ha az összeset szeretné elvégezni, rögzítse az ARR hálózati események gyors nyomkövetését, és **Tiltsa le** ezt a beállítást.
    * Ha az ARR hálózati eseményeket más rendszerjellemzőkkel (például CPU vagy memóriahasználat) kell összekapcsolni, akkor **engedélyezze** ezt a beállítást.
    * Ha engedélyezi ezt a beállítást, a nyomkövetés valószínűleg több gigabájt méretű lesz, és hosszú ideig tart a Mentés és a Megnyitás a WPA-ban.

Ezt követően a WPR-konfigurációnak így kell kinéznie:

![WPR-konfiguráció](./media/wpr.png)

### <a name="recording"></a>Felvétel

A nyomkövetés rögzítésének megkezdéséhez kattintson a **Start** gombra. Bármikor elindíthatja és leállíthatja a rögzítést; ezt megelőzően nem kell lezárva az alkalmazást. Amint láthatja, hogy nem kell megadnia a nyomkövetési alkalmazást, mivel a ETW mindig a teljes rendszer nyomkövetését rögzíti. A `wprp` fájl meghatározza, hogy milyen típusú események legyenek rögzítve.

A rögzítés leállításához és az ETL-fájl tárolási helyének megadásához kattintson a **Mentés** gombra.

Most már rendelkezik egy ETL-fájllal, amelyet közvetlenül a WPA-ban nyithat meg, vagy elküldheti másnak.

## <a name="recording-a-trace-on-a-hololens"></a>Nyomkövetés rögzítése egy HoloLens

A nyomkövetés HoloLens való rögzítéséhez indítsa el az eszközt, és adja meg az IP-címét egy böngészőben az *eszköz-portál* megnyitásához.

![Eszköz portál](./media/wpr-hl.png)

1. A bal oldalon navigáljon a *teljesítmény > a teljesítmény nyomon követése* elemre.
1. **Egyéni profilok** kiválasztása
1. Kattintson **:::no-loc text="Browse...":::**
1. Válassza ki a *AzureRemoteRenderingNetworkProfiling. wprp* fájlt. Ez a fájl az ARR SDK-ban található az *eszközök/ETLProfiles* területen.
1. Kattintson a **Nyomkövetés indítása** elemre
1. A HoloLens most már rögzíti a nyomkövetést. Győződjön meg arról, hogy aktiválja a vizsgálni kívánt teljesítménnyel kapcsolatos problémákat. Ezután kattintson a **nyomkövetés leállítása** elemre.
1. A nyomkövetés a weblap alján jelenik meg. Kattintson a jobb oldalon található lemez ikonra az ETL-fájl letöltéséhez.

Most már rendelkezik egy ETL-fájllal, amelyet közvetlenül a WPA-ban nyithat meg, vagy elküldheti másnak.

## <a name="analyzing-traces-with-wpa"></a>Nyomkövetések elemzése a WPA-vel

### <a name="wpa-basics"></a>A WPA alapjai

A Windows Performance Analyzer a szabványos eszköz az ETL-fájlok megnyitásához és a Nyomkövetések vizsgálatához. Annak magyarázata, hogyan működik a WPA a jelen cikk hatókörén kívül. Az első lépésekhez tekintse meg a következő erőforrásokat:

* Tekintse meg az első áttekintést [bemutató videókat](/windows-hardware/test/wpt/windows-performance-analyzer) .
* A WPA-nak van egy *első lépések* lapja, amely a gyakori lépéseket ismerteti. Tekintse át az elérhető témaköröket. Különösen az "adatmegtekintés" alatt gyorsan bemutatjuk, hogyan hozhat létre gráfokat adott adathoz.
* A [webhelyre](https://randomascii.wordpress.com/2015/09/24/etw-central/)vonatkozó kiváló információk azonban nem mindegyike fontos a kezdőknek.

### <a name="graphing-data"></a>Az adatgráfok

Az ARR-nyomkövetés megkezdéséhez a következő részek jól ismertek.

![Teljesítmény gráf](./media/wpa-graph.png)

A fenti képen a nyomkövetési adatok és az ugyanazon adatok gráf ábrázolása látható.

A lenti táblázatban jegyezze fel a sárga (arany) sávot és a kék sávot. Ezeket a sávokat áthúzhatja, és tetszőleges helyre helyezheti őket.

**A sárga sáv bal oldalán** lévő összes oszlopot **kulcsként** értelmezi a rendszer. A kulcsok használatával a fa a bal felső ablakban alakítható ki. Itt két *fő* oszlop, a "szolgáltató neve" és a "feladat neve" szerepel. Ebből következően a faszerkezet a bal felső ablakban két szint mély. Ha átrendezi az oszlopokat, vagy oszlopokat ad hozzá vagy távolít el a kulcs területen, a fanézetben a struktúra megváltozik.

A **kék sáv jobb oldalán lévő oszlopok** a jobb felső sarokban **látható diagram megjelenítéséhez** használatosak. Az idő nagy részében csak az első oszlop használatos, de egyes diagramok esetében több adatoszlopra van szükség. A vonalas diagramok működéséhez meg kell adni az *összesítési módot* az oszlopon. Az "AVG" vagy a "Max" használata. Az összesítési mód segítségével határozható meg a gráf értéke egy adott képpontban, ha egy képpont több eseménnyel rendelkező tartományt foglal magában. Ezt megfigyelheti az Összesítés beállítása a "Sum" értékre, majd a nagyítás és kicsinyítés lehetőségre.

A középső oszlopok nem rendelkeznek speciális jelentéssel.

![Események nézet](./media/wpa-event-view.png)

Az *általános események nézet szerkesztőjében* konfigurálhatja az összes megjelenített oszlopot, az összesítési módot, a rendezést és a kulcsként vagy a gráfként használt oszlopokat. A fenti példában a **2. mező** engedélyezve van, és a 3-6 mező le van tiltva. A 2. mező általában egy ETW-esemény első *Egyéni* adatmezője, így az ARR "FrameStatistics" eseményekhez, amelyek bizonyos hálózati késési értékeket képviselnek. Az esemény további értékeinek megjelenítéséhez engedélyezze az egyéb "mező" oszlopokat.

### <a name="presets"></a>Készletek

A nyomkövetés megfelelő elemzéséhez meg kell határoznia a saját munkafolyamatot és az előnyben részesített adatmegjelenítést. Ha azonban gyors áttekintést szeretne kapni az ARR-specifikus eseményekről, a Windows szoftverlicencelési platform profilja és a fájlok előre beállítása a mappa *eszközei/ETLProfiles* között. A teljes profil betöltéséhez válassza a *profilok > alkalmazás...* lehetőséget a WPA menüsávon, vagy nyissa meg a *saját előkészletek* panelt (*ablak > saját beállításkészlet*), és válassza az *Importálás* lehetőséget. A korábbi verzióban az alábbi képen látható teljes WPA-konfiguráció lesz beállítva. Az utóbbi csak az elérhető különböző nézet-konfigurációkra vonatkozó előállításokat hajtja végre, és lehetővé teszi, hogy gyorsan megnyisson egy nézetet, hogy megtekintse az ARR-események egy adott adatát.

![Készletek](./media/wpa-arr-trace.png)

A fenti képen a különböző ARR-specifikus események, valamint a teljes CPU-kihasználtság nézete látható.

## <a name="next-steps"></a>Következő lépések

* [Kiszolgálóoldali teljesítménylekérdezések](../overview/features/performance-queries.md)