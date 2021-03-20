---
title: Hangsegédek a Windowsban – tervezési irányelvek
titleSuffix: Azure Cognitive Services
description: Útmutató a hangügynök felhasználói felületének tervezésekor ajánlott eljárásokhoz.
services: cognitive-services
author: cfogg6
manager: spencer.king
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: adamwa
ms.openlocfilehash: 76a29af80891142fcf7f56f93f5c7acc5c58ab04
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92165145"
---
# <a name="design-assistant-experiences-for-windows-10"></a>Tervezési asszisztensi tapasztalatok a Windows 10 rendszerhez

A Windows 10-es verzióra fejlesztett hangsegédeknek az alábbi felhasználói élményre vonatkozó irányelveket kell alkalmazniuk a hangalapú aktiválás lehető legjobb élményének biztosításához Windows 10 rendszeren. Ez a dokumentum végigvezeti a fejlesztőknek a Windows 10-es Rendszerhéjral való integrációhoz szükséges kulcsfontosságú munka megértésében.

## <a name="contents"></a>Tartalom

- [A Windows 10 által támogatott hangaktiválási nézetek összefoglalása](#summary-of-voice-activation-views-supported-in-windows-10)
- [Követelmények összegzése](#requirements-summary)
- [Ajánlott eljárások a jó hallgatási élményhez](#best-practices-for-good-listening-experiences)
- [Tervezési útmutató az alkalmazáson belüli hang aktiválásához](#design-guidance-for-in-app-voice-activation)
- [Tervezési útmutató a hangalapú aktiváláshoz a zárolás felett](#design-guidance-for-voice-activation-above-lock)
- [Tervezési útmutató a hangalapú aktiválás előzetes verziójához](#design-guidance-for-voice-activation-preview)

## <a name="summary-of-voice-activation-views-supported-in-windows-10"></a>A Windows 10 által támogatott hangaktiválási nézetek összefoglalása

A Windows 10 az eszköz kontextusa alapján kikövetkezteti az ügyfél környezetének aktiválási élményét. Az alábbi Összefoglaló táblázat a képernyő bekapcsolásakor elérhető különböző nézetek magas szintű áttekintése.

| Nézet (rendelkezésre állás) | Eszköz környezete | Ügyfél célja | Akkor jelenik meg, ha | Tervezési igények |
| --- | --- | --- | --- | --- |
| **Alkalmazásban (19H1)** | A zárolás alatt a Segéd fókuszban van | Interakció a Segéd-alkalmazással | A Segéd feldolgozza a kérelmet az alkalmazásban | A fő alkalmazáson belüli nézet figyelési felülete |
| **Fenti zárolás (19H2)** | Nincs hitelesítve a zárolás felett | Interakció a segédtel, de távolságból | A rendszer zárolva van, és a Segéd kéri az aktiválást | Teljes képernyős vizualizációk a széles körű felhasználói felületen. Hozzon létre elbocsátási szabályzatokat, hogy ne tiltsa le a zárolást. |
| **Hangaktiválási előnézet (20H1)** | A zárolás alatt a Segéd nem rendelkezik fókusszal | Interakció a segédtel, de kevésbé zavaró módon | A rendszer nem éri el a zárolási és asszisztensi kérelmek háttér-aktiválását | Minimális vászon. Szükség szerint méretezze át vagy kapcsolja ki a fő alkalmazás nézetét. |

## <a name="requirements-summary"></a>Követelmények összegzése

A különböző élmények eléréséhez minimális erőfeszítés szükséges. A segédeknek azonban a megfelelő tervezési útmutatót kell alkalmazniuk az egyes nézetekhez. Az alábbi táblázat a követendő követelmények listáját tartalmazza.

| **Hangaktiválás nézet** | **Asszisztensi követelmények összegzése** |
| --- | --- |
| **Alkalmazáson belüli** | <ul><li>A kérelem feldolgozása az alkalmazásban</li><li>Felhasználói felületi kijelzőket biztosít a figyelő állapotokhoz</li><li>A felhasználói felület alkalmazkodik az ablakok méretének módosításához</li></ul> |
| **Zárolás felett** | <ul><li>Zárolás állapotának észlelése és aktiválás kérése</li><li>Ne adjon meg mindig állandó UX-t, amely letiltja a hozzáférést a Windows zárolási képernyőjéhez</li><li>A teljes képernyős vizualizációk és a hangalapú első élmény biztosítása</li><li>Az elbocsátási útmutató alább</li><li>Kövesse az alábbi adatvédelmi és biztonsági megfontolásokat</li></ul> |
| **Hangaktiválás előzetes verziója** | <ul><li>Feloldási állapot észlelése és háttérbeli aktiválás kérése</li><li>Minimális figyelési UX rajzolása az Előnézet panelen</li><li>A jobb felső sarokban rajzoljon egy Bezárás X-et, és lenyomva hagyja a folyamatos átviteli hangot</li><li>Átméretezheti vagy kikapcsolhatja a fő segéd-alkalmazás nézetét a válaszok megadásához.</li></ul> |

## <a name="best-practices-for-good-listening-experiences"></a>Ajánlott eljárások a jó hallgatási élményhez

A segédeknek olyan figyelési élményt kell kialakítaniuk, amely kritikus visszajelzéseket biztosít, így az ügyfél tisztában lehet az asszisztens állapotával. Az alábbiakban néhány lehetséges állapotot kell figyelembe venni az asszisztensi élmény kiépítésekor. Ezek csak a lehetséges javaslatok, nem kötelező útmutatások.

- A Segéd elérhető a beszédfelismerési bemenethez
- Az asszisztens az aktiválás folyamatban van (vagy egy kulcsszó vagy egy mikrofon gomb megnyomása)
- A Segéd aktívan közvetíti a hangot a Segéd felhőbe
- A Segéd készen áll az ügyfél beszédének megkezdésére
- A Segéd meghallgatja a szavakat
- A Segéd tudomásul veszi, hogy az ügyfél beszél
- A Segéd feldolgozza és felkészíti a választ
- A Segéd válaszol

Még ha az állapotok is gyorsan változnak, érdemes megfontolni az UX megadását az államok számára, mivel az időtartamok a Windows ökoszisztémán belül változóak. &quot; &quot; A megoldás része lehet a vizuális visszajelzés, valamint a rövid hangsávok vagy a csiripelés, más néven earcons. Hasonlóképpen, a vizuális kártyák és a hangleírások is jó választ biztosítanak.

## <a name="design-guidance-for-in-app-voice-activation"></a>Tervezési útmutató az alkalmazáson belüli hang aktiválásához

Ha a Segéd-alkalmazás fókuszban van, az ügyfél szándéka egyértelműen együttműködik az alkalmazással, így az összes hangaktiválási élményt a fő alkalmazás nézetnek kell kezelnie. Ezt a nézetet az ügyfél átméretezi. A Segéd-rendszerhéj interakciójának magyarázata érdekében a dokumentum többi része a contoso nevű pénzügyi szolgáltatási asszisztens konkrét példáját használja. Ebben és az azt követő diagramokban az ügyfél azt mondja majd, hogy a bal oldali rajzfilm-buborékokban a jobb oldalon megjelenik a képregény-buborékok.

**Alkalmazáson belüli nézet. Kezdeti állapot a Hangaktiválás megkezdése után:** 
 ![ képernyőfelvétel a contoso Finance Assistant alkalmazás alapértelmezett vászonra való megnyitásáról. A jobb oldalon a "contoso" című Cartoon Speech buborék látható.](media/voice-assistants/windows_voice_assistant/initial_state.png)

**Alkalmazáson belüli nézet. A sikeres hangaktiválást követően a zenehallgatási folyamat megkezdődik:** ![ képernyőkép a hangsegédről a Windowsban, miközben a hangsegéd figyeli a funkciót](media/voice-assistants/windows_voice_assistant/listening.png)

**Alkalmazáson belüli nézet. Az összes válasz az alkalmazás felhasználói felületén marad.** ![ Képernyőkép a Windows hangsegédről](media/voice-assistants/windows_voice_assistant/response.png)

## <a name="design-guidance-for-voice-activation-above-lock"></a>Tervezési útmutató a hangalapú aktiváláshoz a zárolás felett

A 19H2-mel elérhető, a Windows hangaktiválási platformra épülő asszisztensek a fenti zárolások megválaszolásához érhetők el.

### <a name="customer-opt-in"></a>Ügyfél-opt

A zárolást a fenti hangalapú aktiválás alapértelmezés szerint mindig le van tiltva. Az ügyfelek a Windows beállításaiban>az adatvédelem>a hangalapú aktiválást. A beállítás figyelésével és kérésével kapcsolatos részletekért lásd a [zárolás megvalósításának fenti útmutatóját](windows-voice-assistants-implementation-guide.md#detecting-above-lock-activation-user-preference).

### <a name="not-a-lock-screen-replacement"></a>Nem zárolási képernyő cseréje

Amíg az értesítések vagy más szabványos alkalmazás-zárolási pontok továbbra is elérhetők a Segéd számára, a Windows zárolási képernyő mindig a kezdeti felhasználói élményt határozza meg, amíg a hangalapú aktiválás be nem következik. A hangalapú aktiválás észlelése után a Segéd-alkalmazás átmenetileg megjelenik a zárolási képernyő felett. Ha el szeretné kerülni az ügyfelek összekeveredését, amikor a zárolási funkció aktív, a Segéd alkalmazásnak soha nem kell megadnia a felhasználói felületet, hogy bármilyen hitelesítő adatot kérjen vagy jelentkezzen be.

![Képernyőfelvétel a Windows zárolási képernyőjéről](media/voice-assistants/windows_voice_assistant/lock_screen1.png)

### <a name="above-lock-experience-following-voice-activation"></a>A zárolási élmény a hangalapú aktiválást követően

Ha a képernyő be van kapcsolva, a Segéd-alkalmazás teljes képernyős, és címsor nélkül jelenik meg a zárolási képernyőn. Nagyobb vizualizációk és erős hangleírások erős hangvezérelt elsődleges felülettel lehetővé teszik azokat az eseteket, amikor az ügyfél túl távol van a felhasználói felület olvasásához, vagy egy másik (nem PC) feladattal van elfoglalva.

Ha a képernyő ki van kapcsolva, a Segéd-alkalmazás lejátszhat egy earcon, amely jelzi, hogy a Segéd aktiválva van, és csak hangvételi élményt biztosít.

![Képernyőkép a hangsegédről a zárolás felett](media/voice-assistants/windows_voice_assistant/above_lock_listening.png)

### <a name="dismissal-policies"></a>Elbocsátási szabályzatok

A Segédnek meg kell valósítania az elbocsátási útmutatót ebben a szakaszban, hogy az ügyfelek könnyebben jelentkezzenek be a következő alkalommal, amikor a Windows rendszerű SZÁMÍTÓGÉPét szeretnék használni. Az alábbiakban a speciális követelmények láthatók, amelyeket a Segédnek végre kell hajtania:

- A **fenti zárolást jelző összes segéd-vászonnak tartalmaznia kell egy X betűt** a jobb felső sarokban, amely elutasítja a Segédet.
- **Bármely kulcs lenyomásakor el kell hagynia a Segéd alkalmazást is**. A billentyűzet bemenet egy hagyományos zárolási alkalmazási jel, amelyet az ügyfél szeretne bejelentkezni. Ezért a billentyűzet/szöveges bemenetek nem irányíthatók át az alkalmazásba. Ehelyett az alkalmazásnak el kell hagynia a billentyűzet bemenetének észlelését, így az ügyfél egyszerűen bejelentkezhet az eszközére.
- **Ha a képernyő leáll, az alkalmazásnak önmagát el kell kezdenie.** Ez biztosítja, hogy a felhasználó legközelebb a SZÁMÍTÓGÉPét használja, a bejelentkezési képernyő készen áll, és vár rájuk.
- Ha az alkalmazás &quot; használatban van &quot; , akkor továbbra is bezárhatja a zárolást. &quot;a használatban &quot; bármilyen bemenet vagy kimenet szerepel. Ha például zenét vagy videót tart fenn, az alkalmazás továbbra is zárolhatja a zárolást. &quot;A következő &quot; lépésekkel és más többkapcsoló párbeszédpanelekkel megtarthatja az alkalmazás zárolását.
- **Az alkalmazás elutasításának megvalósítási részletei** a [fenti zárolás megvalósítási útmutatójában](windows-voice-assistants-implementation-guide.md#closing-the-application)találhatók.

![A contoso Finance Assistant alkalmazás fenti zárolási nézetét ábrázoló képernyőkép.](media/voice-assistants/windows_voice_assistant/above_lock_response.png)

![Képernyőfelvétel a Windows zárolási képernyőjén látható asztalról.](media/voice-assistants/windows_voice_assistant/lock_screen2.png)

### <a name="privacy-amp-security-considerations-above-lock"></a>Adatvédelem &amp; biztonsági megfontolások a zárolás felett

Számos számítógép hordozható, de nem mindig az ügyfél-elérhetőségen belül van. Előfordulhat, hogy a szállodai szobákban, a repülőgép-munkahelyeken vagy a munkaterületeken is rövid ideig tartanak, ahol más személyek fizikai hozzáféréssel rendelkeznek. Ha a zárolást engedélyező asszisztensek nem állnak készen, akkor az ún &quot; . [Evil Maid](https://en.wikipedia.org/wiki/Evil_maid_attack) -támadások osztálya alá tartoznak &quot; .

Ezért az asszisztenseknek az ebben a részben ismertetett útmutatást kell követniük a felhasználói élmény fenntartásához. A zárolás fölötti interakció akkor fordul elő, ha a Windows-felhasználó nem hitelesített. Ez azt jelenti, hogy **a Segédnek való bevitelt általában nem hitelesítettként kell kezelni**.

- Az asszisztenseknek **olyan szaktudás-engedélyezési listát kell alkalmazniuk, amely azonosítja azokat a képességeket, amelyeket a rendszer** a fenti zárolást követően a biztonságos és biztonságos hozzáférés érdekében megerősít.
- A hangfelismerő technológiák bizonyos kockázatok enyhítésében is szerepet játszhatnak, de a hangszórók azonosítója nem megfelelő a Windows-hitelesítéshez.
- Az engedélyezett képzettségi listának a műveletek és képességek három osztályát kell figyelembe vennie:

| **Műveleti osztály** | **Leírás** | **Példák (nem teljes lista)** |
| --- | --- | --- |
| Biztonságos hitelesítés nélkül | Általános célú információk vagy alapszintű app Command és Control | &quot;Mennyi idő van? &quot; , &quot; a következő műsorszám lejátszása&quot; |
| Biztonságos a hangsugárzó azonosítójával | Megszemélyesítési kockázat, személyes adatok feltárása. | &quot;Mi&#39;s a következő kinevezésem? &quot; , &quot; a vásárlások listájának megtekintése &quot; , &quot; a hívás megválaszolása&quot; |
| Csak a Windows-hitelesítés után biztonságos | Magas kockázatú műveletek, amelyeket a támadók az ügyfelek sérülésére használhatnak | &quot;Vásároljon több élelmiszerboltot, &quot; &quot; törölje a (fontos) időpontot &quot; , &quot; küldjön egy (mean) szöveges üzenetet &quot; , &quot; indítson el egy (aljas) weblapot&quot; |

A contoso esetében a nyilvános Stock-információk körére vonatkozó általános információk hitelesítés nélkül biztonságosak. Az ügyfél-specifikus információk, például a tulajdonában lévő megosztások száma valószínűleg biztonságos a beszélő azonosítójával. A készletek vásárlása és értékesítése azonban soha nem engedélyezett Windows-hitelesítés nélkül.

A felhasználói élmény, a **Weblinkek és az alkalmazások közötti egyéb alkalmazások további biztonságossá tételéhez mindig a Windows blokkolja a rendszer, amíg az ügyfél be nem jelentkezik.** Végső megoldásként a Microsoft fenntartja a jogot arra, hogy eltávolítson egy alkalmazást az engedélyezett asszisztensek listájáról, ha egy súlyos biztonsági probléma nem kerül kellő időben.

## <a name="design-guidance-for-voice-activation-preview"></a>Tervezési útmutató a hangalapú aktiválás előzetes verziójához

A zárolás alatt, ha a Segéd-alkalmazás _nem_ rendelkezik fókusszal, a Windows kevésbé zavaró hangaktiválási felületet biztosít az ügyfél folyamaton belüli megtartásához. Ez különösen igaz a hamis aktiválások esetében, amelyek rendkívül zavaróak lehetnek, ha elindították a teljes alkalmazást. Az alapvető elképzelés az, hogy minden asszisztens rendelkezik egy másik otthontal a shellben, a Segéd tálcán ikon. Ha a háttérben történő aktiválásra vonatkozó kérelem történik, megjelenik egy kis nézet a Segéd tálca ikonja felett. Az asszisztenseknek kis zenehallgatási élményt kell adniuk ebben a vásznon. A kérések feldolgozása után a segédek dönthetnek úgy, hogy átméretezhetik a nézetet egy kontextusbeli válasz megjelenítéséhez, vagy a fő alkalmazás nézetét a nagyobb, részletesebb vizualizációk megjelenítéséhez.

- A minimális érték kiválasztásához az előzetes verziónak nincs címsora, ezért **Az asszisztensnek a jobb felső sarokban kell megrajzolnia egy X-et, hogy az ügyfelek el tudjanak vonni a nézetet.** Az Elvetés gomb megnyomásakor a megadott API-k [alkalmazásának bezárásához](windows-voice-assistants-implementation-guide.md#closing-the-application) tekintse meg az alkalmazást.
- A hangalapú aktiválási előnézetek támogatásához a segédek meghívhatják az ügyfelet, hogy az első futtatáskor rögzítsék az asszisztenst a tálcán.

**Hangalapú aktiválás előzetes verziója: kezdeti állapot**

A contoso Assistant otthona van a tálcán: az örvénylő, körkörös ikon.

![Képernyőkép a Windowson futó hangsegédről tálca ikon előtti aktiváláskor](media/voice-assistants/windows_voice_assistant/pre_compact_view.png)

Az **aktiválás előrehaladtával** a Segéd a háttér-aktiválást kéri. A Segéd egy kis betekintő ablaktáblát kap (az alapértelmezett szélesség 408 és magasság: 248). Ha a kiszolgálóoldali Hangaktiválás meghatározza, hogy a jel hamis pozitív volt, ez a nézet a minimális megszakítás miatt figyelmen kívül hagyható.

![Képernyőfelvétel a Windowsról készült hangsegédről kompakt nézetben az aktiválás ellenőrzése közben](media/voice-assistants/windows_voice_assistant/compact_view_activating.png)

A **végleges aktiválás megerősítésekor** a Segéd a figyelő UX-t is megjeleníti. A Hangaktiválás előzetes verziójának jobb felső részén a Segédnek mindig el kell készítenie az X. elvet.

![A hangsegéd képernyőképe a Windows figyelése kompakt nézetben](media/voice-assistants/windows_voice_assistant/compact_view_listening.png)

A **gyors válaszok** a Hangaktiválás előzetes verziójában is megjelennek. A TryResizeView lehetővé teszi, hogy a segédek különböző méreteket kérjenek.

![Képernyőkép a hangsegédről a Windows reply szolgáltatásban kompakt nézetben](media/voice-assistants/windows_voice_assistant/compact_view_response.png)

**Kikapcsolás**. A Segéd bármikor kikapcsolhatja a fő alkalmazás nézetét, hogy további információkat, párbeszédet vagy válaszokat adjon meg, amelyek több képernyős ingatlant igényelnek. A megvalósítás részleteiért tekintse meg a [tömör nézetről teljes nézetre való áttérést](windows-voice-assistants-implementation-guide.md#transition-from-compact-view-to-full-view) ismertető szakaszt.

![A hangsegéd képernyőképei a Windowsban a kompakt nézet bővítése előtt és után](media/voice-assistants/windows_voice_assistant/compact_transition.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a hangsegéd fejlesztésével](how-to-windows-voice-assistants-get-started.md)