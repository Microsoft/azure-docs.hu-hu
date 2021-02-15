---
title: Gyakornoki mód – személyre szabás
description: Megtudhatja, hogyan használhatja a tanulói módot a modell megbízhatóságának megszerzéséhez a kód módosítása nélkül.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 531917d9c48915f71354b4cd35747ecd9d33a6f8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385030"
---
# <a name="use-apprentice-mode-to-train-personalizer-without-affecting-your-existing-application"></a>A tanulói mód használata a személyre szabás kitanításához anélkül, hogy ez hatással lenne a meglévő alkalmazásra

A **valós idejű** megerősítő tanulás jellegéből adódóan a személyre szabott modelleket csak éles környezetben lehet tanítani. Új használati eset telepítésekor a személyre szabott modell nem működik hatékonyan, mert időt vesz igénybe, hogy a modell megfelelően legyen betanítva.  A **gyakornoki mód** olyan tanulási viselkedés, amely megkönnyíti ezt a helyzetet, és lehetővé teszi a modell megbízhatóságának megszerzését – anélkül, hogy a fejlesztő bármilyen kódot módosítaná.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="what-is-apprentice-mode"></a>Mi az a Apprentice mód?

Hasonlóan ahhoz, ahogy egy tanítvány tanul a főkiszolgálóról, és a tapasztalatok jobbak lehetnek; A gyakornoki mód olyan _viselkedés_ , amely lehetővé teszi a személyre szabást a meglévő alkalmazás-logikából beszerzett eredmények megfigyelésével.

A személyre szabott vonatok az alkalmazással megegyező kimenetet utánozva. Ahogy egyre több esemény folyik, a személyre szabás a meglévő alkalmazásra is felhasználható _anélkül, hogy_ ez hatással lenne a meglévő logikai és eredményre. A Azure Portal és az API által elérhető metrikák segítségével megismerheti a modell által megismert teljesítményt.

A személy megtanulta és elsajátított egy bizonyos szintű megértést, a fejlesztő a tanulói mód viselkedését online módba változtathatja. Ekkor a személyre szabás megkezdi a Range API műveleteinek befolyásolását.

## <a name="purpose-of-apprentice-mode"></a>A Apprentice mód célja

A gyakornoki mód megbízhatóságot biztosít a személyre szabott szolgáltatásban és a gépi tanulási képességeiben, és biztosítja, hogy a szolgáltatás olyan információt küldjön, amelyet az online forgalom kockáztatása nélkül is el lehet sajátítani.

A Apprentice mód használatának két fő oka a következő:

* A **hűtés** enyhítése: a gyakornoki mód segít az "új" modell betanítási idejének kezelésében és értékelésében – ha nem adja vissza a legjobb működést, és nem éri el a 60-80%-os hatékonyságot kielégítő szintet.
* A **művelet-és környezeti funkciók ellenőrzése**: a műveletekben és kontextusban eljuttatott funkciók nem megfelelőek vagy pontatlanok lehetnek – a túl kevés, túl sok, helytelen vagy túl jellemző a személyre szabáshoz az ideális hatékonysági arány eléréséhez. A funkciókkal kapcsolatos problémák megkereséséhez és kijavításához használja a [szolgáltatás-értékeléseket](concept-feature-evaluation.md) .

## <a name="when-should-you-use-apprentice-mode"></a>Mikor érdemes a Apprentice üzemmódot használni?

A személyre szabott mód használatával kitaníthatja a testre szabást a következő helyzetekben, miközben a felhasználók nem érintik a személyre szabott felhasználói élményt:

* A személyre szabást új használati esetben kell végrehajtania.
* Jelentős mértékben módosította a kontextusban vagy műveletekben küldött szolgáltatásokat.
* Jelentősen megváltozott a jutalmak kiszámításának és módjának kiszámításakor.

A gyakornoki mód nem hatékony módszer a kihatás személyre szabására a jutalmazási pontszámok miatt. Ha azt szeretné mérni, hogy az egyes rangsorolási hívások esetében milyen hatékony a lehető legjobb művelet, használja az [Offline értékeléseket](concepts-offline-evaluation.md).

## <a name="who-should-use-apprentice-mode"></a>Kinek kell használnia a Apprentice üzemmódot?

A gyakornoki mód a fejlesztők, az adatszakértők és az üzleti döntéshozók számára hasznos:

* A **fejlesztők** a tanulói módot használhatják annak biztosítására, hogy a Range és a jutalmazási API-k helyesen legyenek használatban az alkalmazásban, és az alkalmazásból a személyre küldött funkciók nem tartalmaznak hibákat, vagy nem releváns funkciókat, például időbélyeg vagy userid elemet.

* Az **adatszakértők** a tanulói mód használatával ellenőrizhetik, hogy a funkciók hatékonyak-e a személyre szabott modellek betanításához, hogy a jutalmazási várakozási idő nem túl hosszú vagy rövid.

* Az **üzleti** döntéshozók a tanulói módot használhatják arra, hogy a meglévő üzleti logikához képest javítsák a személyre szabott eredményeket (például a jutalmakat). Ez lehetővé teszi számukra, hogy tájékozott döntést hozzanak a felhasználói élményről, ahol a valós bevétel és a felhasználói elégedettség a tét.

## <a name="comparing-behaviors---apprentice-mode-and-online-mode"></a>Viselkedésmódok összehasonlítása – tanulói mód és online mód

A tanulói módban való tanulás az alábbi módokon különbözik az online módból.

|Terület|Gyakornoki mód|Online mód|
|--|--|--|
|A felhasználói élményre gyakorolt hatás|Meglévő felhasználói viselkedést is használhat a személyre szabáshoz azáltal, hogy megfigyeli (nem befolyásolja) az **alapértelmezett műveletet** , és megszerezte a jutalmat. Ez azt jelenti, hogy a felhasználói élményt és az azokból származó üzleti eredményeket nem érinti.|A rangsorolási hívás által visszaadott felső művelet megjelenítése a felhasználói viselkedés befolyásolására.|
|Tanulási sebesség|A személyre szabott mód sokkal lassabban fog tanulni, ha az online módban tanul. A gyakornoki mód csak akkor tanulható meg, ha megfigyeli az **alapértelmezett művelet** által beszerzett jutalmakat, ami korlátozza a tanulás sebességét, mivel nem végezhető el a felderítés.|Gyorsabban tanulhat, mivel képes kihasználni az aktuális modellt, és feltárni az új trendeket.|
|Tanulási hatékonyság – felső határ|A személyre szabott érték megközelíthető, nagyon ritkán egyezik, és soha nem haladhatja meg az alapszintű üzleti logika teljesítményét (az egyes rangsorolt hívások **alapértelmezett művelete** által elért jutalom összesen). Ez a közelítési felső korlátot a feltárás csökkenti. Például a 20%-os feltárás esetén nagyon valószínűtlen, hogy a tanulói mód teljesítménye meghaladja a 80%-ot, és a 60% egy ésszerű cél, amellyel online módba lehet lépni.|A személyre szabásnak meg kell haladnia az alkalmazások alapkonfigurációját, és az idő múlásával meg kell szüntetnie az offline kiértékelést és a funkciók kiértékelését, hogy továbbra is javítsa a modellt. |
|A rewardActionId Range API-értéke|A felhasználói élmény nem lesz hatással, mivel a _rewardActionId_ mindig az első, a rangsorban küldött művelet. Ez azt jelenti, hogy a Rank API a tanulói módban nem jelenik meg az alkalmazás számára. Az alkalmazásban található API-k jutalmazása nem változtathatja meg, hogy a jutalmazási API hogyan használja az egyik mód és egy másik között.|A felhasználói élményt az alkalmazás személyre szabott _rewardActionId_ fogja módosítani. |
|Értékelések|A személyre szabhatja az alapértelmezett üzleti logikában bekövetkező jutalom összegének összehasonlítását, és a jutalmak összegének személyre szabása akkor lesz elérhető, ha online módban van. Az összehasonlítás az adott erőforráshoz tartozó Azure Portal érhető el|Az [Offline értékelések](concepts-offline-evaluation.md)segítségével kiértékelheti a személyre szabott lehetőségeket, így összehasonlíthatja a teljes jutalmak személyre szabását az alkalmazás alapkonfigurációjának lehetséges hasznával.|

A Apprentice mód hatékonyságával kapcsolatos Megjegyzés:

* Az alkalmazás alapkonfigurációjának közel 100%-a a személyre szabási módban a személyre szabott hatékonyságot ritkán éri el a rendszer. és soha ne lépje túl.
* Az ajánlott eljárások nem a 100%-os elérésének megkezdéséhez szükségesek. a használati esettől függően a 60% – 80%-os tartományt kell megcélozni.

## <a name="using-apprentice-mode-to-train-with-historical-data"></a>Tanulói mód használata a korábbi adatbevitelhez

Ha jelentős mennyiségű korábbi adattal rendelkezik, azt szeretné használni a személyre szabáshoz, a tanulói mód használatával visszajátszhatja az adatvédelmet személyre szabott módon.

Állítsa be a személyre szabott üzemmódot, és hozzon létre egy olyan parancsfájlt, amely a korábbi adatokból származó műveletekkel és környezeti funkciókkal hívja meg a rangsort. Hívja meg a jutalmazási API-t az adatokban szereplő rekordok számításai alapján. Körülbelül 50 000 korábbi eseményre lesz szüksége, hogy lásson néhány eredményt, de a 500 000 javasolt az eredmények nagyobb biztonsága érdekében.

A korábbi adatokból származó képzések esetében ajánlott, hogy a (környezeti és műveleti funkciók, a Range-kérelmekhez használt JSON-elrendezések és a betanítási adatkészletbe való jutalom kiszámítása) során küldött adatok megfeleljenek a meglévő alkalmazásból elérhető adatoknak (funkciók és számítási díj).

Az offline és a post-facto adatfeldolgozás általában még hiányos és zajosabb, és a formátuma eltérő. A korábbi adatokból származó képzések azonban lehetségesek, így az eredmények nem meggyőzőek, és nem jó előrejelzők a személyre szabott ismeretek megismeréséhez, különösen akkor, ha a szolgáltatások a múltbeli adatok és a meglévő alkalmazások között változnak.

Általában a személyre szabott, a múltbeli adatokkal való képzéshez képest, a tanulói módra való váltás és a meglévő alkalmazások megismerése hatékonyabb elérési út a hatékony modellhez, kevesebb munkavégzéssel, adattervezéssel és-tisztítással.

## <a name="using-apprentice-mode-versus-ab-tests"></a>Tanulói mód és A/B tesztek használata

Csak akkor lehet hasznos, ha A/B teszteket a személyre szabott kezelésekhez a rendszer érvényesíti, és online módban tanul. A Apprentice módban csak az **alapértelmezett művelet** van használatban, ami azt jelenti, hogy az összes felhasználó ténylegesen látni fogja a vezérlési élményt.

Még ha a személyre szabás is csak a _kezelés_, akkor is ugyanez a kihívás jelenik meg, ha az adat érvényesítése jó a személyre szabott képzéshez. A gyakornoki mód felhasználható a forgalom 100%-ában, és minden felhasználónál, hogy a vezérlő (nem érintett) élményben legyen.

Ha a személyre szabott és a online tanulást használó használati esettel rendelkezik, A/B kísérletek lehetővé teszik a szabályozott kohorszok használatát, valamint az olyan eredmények tudományos összehasonlítását, amelyek összetettebbek lehetnek, mint a jutalomhoz használt jelek. Egy példa az A/B tesztre adott válasz: `In a retail website, Personalizer optimizes a layout and gets more users to _check out_ earlier, but does this reduce total revenue per transaction?`

## <a name="next-steps"></a>Következő lépések

* Az [aktív és az inaktív események](concept-active-inactive-events.md) megismerése
