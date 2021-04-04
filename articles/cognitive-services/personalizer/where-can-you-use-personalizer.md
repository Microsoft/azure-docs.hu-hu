---
title: Hol és hogyan kell használni a-személyre szabott
description: A személyre szabás bármilyen helyzetben alkalmazható, ahol az alkalmazás kiválaszthatja a megfelelő elemet, műveletet vagy terméket a megjelenítéshez, hogy a élmény jobban elérhető legyen, jobb üzleti eredményeket érjen el, vagy javítsa a termelékenységet.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 87179950c33a6facb59ce499aa0ae393e53fb37f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91777224"
---
# <a name="where-and-how-to-use-personalizer"></a>Hol és hogyan kell használni a személyre szabott

Bármilyen helyzetben használhatja a személyre szabott lehetőséget, amikor az alkalmazásnak ki kell választania a megfelelő műveletet (tartalmat) a megjelenítéshez, hogy jobb legyen a élmény, a jobb üzleti eredmények elérése vagy a hatékonyság növelése érdekében.

A személyre szabás megerősítő tanulás segítségével kiválaszthatja, hogy melyik művelet (tartalom) jelenjen meg a felhasználó számára. A kijelölés a szolgáltatásnak továbbított adatmennyiségtől, minőségtől és elosztástól függően jelentősen változhat.

## <a name="example-use-cases-for-personalizer"></a>Példa a személyre szabott használati esetek használatára

* A **szándék tisztázásának &e**: segítheti a felhasználókat abban, hogy jobb felhasználói élményt nyújtsanak, ha a személyre szabott lehetőséggel nem egyértelműek.
* A menükre vonatkozó **alapértelmezett javaslatok** & lehetőségek: a robot azt sugallja, hogy a legvalószínűbb elem egy személyre szabott módon, első lépésként, nem pedig egy személytelen menüt vagy alternatívák listáját jeleníti meg.
* **Bot-tulajdonságok & hang**: olyan robotok esetében, amelyek a tónustól, a részletességtől és a stílustól függően változhatnak, érdemes lehet ezeket a tulajdonságokat megváltoztatni.
* **Értesítési & riasztás tartalma**: döntse el, hogy a riasztások milyen szöveggel legyenek felhasználva a felhasználók számára.
* **Értesítési & riasztás időzítése**: személyre szabott tanulás arról, hogy mikor küldjön értesítéseket a felhasználóknak, hogy további műveleteket folytassanak.


## <a name="expectations-required-to-use-personalizer"></a>A személyre szabáshoz szükséges elvárások

A személyre szabás olyan helyzetekben alkalmazható, amikor megfelel a következő irányelveknek, vagy megvalósíthatja azokat.

|Irányelv|Magyarázat|
|--|--|
|Üzleti cél|Az alkalmazáshoz üzleti vagy használhatósági cél tartozik.|
|Tartalom|Van egy olyan helye az alkalmazásban, ahol a felhasználók számára megjelenítendő kontextusbeli döntés alapján fejlesztheti ezt a célt.|
|Tartalom mennyisége|Egy hívásnál kevesebb mint 50 művelet van.|
|Összesített adatértékek|A legjobb választás a kollektív felhasználói viselkedésből és az összes jutalom pontszámból is elsajátítható.|
|Etikus használat|A gépi tanulás használata személyre szabáshoz az Ön által választott [felelős használati irányelvek](ethics-responsible-use.md) és döntések követik.
|Legjobb egyetlen lehetőség|A kontextusra vonatkozó döntés a lehető legjobb lehetőség (művelet) rangsorolásával állítható be a választható lehetőségek közül.|
|Felhorzsolt eredmény|Az alkalmazáshoz tartozó rangsorolt választék meghatározása a felhasználói viselkedés bizonyos aspektusának mérésével, valamint a _[jutalom pontszámának](concept-rewards.md)_ kifejezésével határozható meg.|
|Megfelelő időzítés|A jutalom pontszáma nem túl sok összetételt vagy külső tényezőt eredményez. A kísérlet időtartama elég alacsony ahhoz, hogy a jutalom pontszám kiszámítható legyen, miközben továbbra is releváns.|
|Megfelelő környezeti funkciók|Megadhatja a rangsor kontextusát legalább 5 olyan [szolgáltatás](concepts-features.md) listájának megfelelően, amelyet úgy gondol, hogy kihasználja a megfelelő választást, és nem tartalmaz felhasználóspecifikus azonosításra alkalmas adatokat.|
|Megfelelő műveleti funkciók|Minden egyes tartalomra vonatkozó döntésről, _műveletről_, legalább 5 olyan [szolgáltatásról](concepts-features.md) van szó, amelyről úgy gondolja, hogy a megfelelő választást fogja segíteni személyre szabni.|
|Napi adatértékek|Van elég esemény az optimális személyre szabáshoz, ha a probléma idővel sodródik (például hírek vagy Fashion beállítások). A személyre szabás alkalmazkodik a valós világ folytonos változásaihoz, de az eredmények nem lesznek optimálisak, ha nem áll rendelkezésre elegendő esemény és információ az új minták felderítéséhez és rendezéséhez. Olyan használati esetet kell választania, amely elég gyakran előfordul. Érdemes lehet olyan használati eseteket keresni, amelyek naponta legalább 500 alkalommal történnek.|
|Korábbi adatértékek|Az alkalmazás képes megőrizni az adatmennyiséget, hogy az előzmények legalább 100 000 interakciót gyűjtsenek. Ez lehetővé teszi a személy számára, hogy elegendő adatot gyűjtsön az offline értékelések és a házirendek optimalizálásának végrehajtásához.|

**Ne használja a személyre** szabott viselkedést, amely az összes felhasználó számára nem deríthető fel. Ha például a személyre szabott lehetőséget használja arra, hogy egy 20 lehetséges menüelemből álló lista első pizza-sorrendjét javasolja, akkor a felhasználók kapcsolattartási listájáról hívható hívás, ha segítségre van szükség a gyermekgondozásban (például "nagymama"), nem olyan dolog, amely a felhasználói bázison belül személyre szabható.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Személyre szabott webes alkalmazás használata

Tanulási hurok hozzáadása egy webalkalmazáshoz:

* Határozza meg, hogy melyik felhasználói élményre legyen személyre szabva, milyen műveletekre és funkciókra van szükség, milyen környezeti funkciókat kell használni, és milyen jutalmat kell beállítania.
* Adjon hozzá egy hivatkozást a személyre szabási SDK-hoz az alkalmazásban.
* Ha készen áll a személyre szabásra, hívja meg a Rank API-t.
* Tárolja a Napszállta. Később jutalmat küld a jutalmazási API-nak.
1. Ha biztos benne, hogy a felhasználó megtekintette a személyre szabott lapot, hívja meg az aktiválást az eseményre.
1. Várja meg a rangsorolt tartalom felhasználó általi kijelölését.
1. A jutalmazási API meghívásával megtudhatja, hogy a rangsor API kimenete milyen jól működött.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Személyre szabott csevegési robot használata

Ebből a példából megtudhatja, hogyan használhatja a személyre szabást úgy, hogy egy alapértelmezett javaslatot tegyen elérhetővé, ahelyett, hogy minden alkalommal le kellene küldenie a felhasználót egy sor menükből vagy választási lehetőségből.

* A minta [kódjának](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) beolvasása.
* Állítsa be a robot-megoldást. Győződjön meg arról, hogy a LUIS-alkalmazást közzéteszi.
* Rangsor és jutalmazási API-hívások kezelése a robothoz.
    * Kód hozzáadása a LUIS szándék-feldolgozás kezeléséhez. Ha a **none** értéket a rendszer a legfelső szintű szándék vagy a legnépszerűbb cél pontszáma alatt éri el, az üzleti logika küszöbértéke alatt küldje el a szándékok listáját a személyre a szándékok rangsorolásához.
    * Megadhatja a leképezések listáját a felhasználók számára választható hivatkozásként, amely az első szándék, hogy a rangsor API-válaszból származó legfelső rangú szándék.
    * Rögzítse a felhasználó kijelölését, és küldje el ezt a jutalmazási API-hívásban.

### <a name="recommended-bot-patterns"></a>Ajánlott bot-minták

* Személyre szabhatja az API-hívásokat minden alkalommal, amikor szükség van rá, az egyes felhasználók gyorsítótárazási eredményeivel szemben. A eszköznév szándéka idővel változhat egy személy számára, és lehetővé teszi, hogy a rangsor API felgyorsítsa az általános tanulást.
* Válassza ki a sok felhasználóval közös interakciót, hogy elegendő adattal rendelkezzen a személyre szabáshoz. Előfordulhat például, hogy a bevezető kérdések jobban illeszkednek a beszélgetési gráfban található kisebb pontosításokhoz, amelyek csak néhány felhasználó számára lehetségesek.
* A Rank API-hívások használatával engedélyezheti az "első javaslat megfelelő" beszélgetéseket, ahol a felhasználó a következőt kérdezi le: "szeretne X?" vagy "érted X?" és a felhasználó csak megerősítheti; nem kell megadnia a beállításokat arra a felhasználóra vonatkozóan, ahol a menüt ki kell választani. Például: "szeretném megrendelni egy kávét?" bot: "szeretne egy dupla Espresso?". Így a jutalmazási jel is erős, mivel közvetlenül az egyik javaslatra vonatkozik.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>A személyre szabott megoldás használata javaslattal

Számos vállalat ajánlási motorokat, marketing-és kampánycélok-eszközöket, a célközönség szegmentálását és fürtözését, az együttműködési szűrést és más eszközöket ajánl fel a nagy katalógusból az ügyfeleknek szánt termékek használatát.

A [Microsoft Ajánlói GitHub-tárháza](https://github.com/Microsoft/Recommenders) példákat és ajánlott eljárásokat biztosít a Jupyter-jegyzetfüzetként biztosított, ajánlásokat tartalmazó rendszerek létrehozásához. Az útmutató az adatelőkészítéshez, modellek létrehozásához, kiértékeléséhez, finomhangolásához és végrehajtott, számos gyakori megközelítéshez, többek között a xDeepFM, a SAR-hoz, az ALS-hez, a DKN-hez,

A személyre szabott ajánlási motorral működhet, ha jelen van.

* Az ajánlási motorok nagy mennyiségű elemet (például 500 000) tesznek elérhetővé, és egy részhalmazt (például az első 20) a több száz vagy több ezer lehetőség közül.
* A személyre szabott számos művelet sok információt tartalmaz róluk, és valós időben rangsorolja őket egy adott gazdag kontextusban, míg a legtöbb ajánlási motor csak néhány attribútumot használ a felhasználókról, a termékekről és az interakcióról.
* A személyre szabott megoldás lehetővé teszi, hogy minden alkalommal önállóan tárja fel a felhasználói beállításokat, ami jobb eredményeket eredményez, ahol a tartalom gyorsan változik, például hírek, élő események, élő közösségi tartalom, napi frissítéssel ellátott tartalom vagy szezonális tartalom.

A közös használat egy ajánlási motor kimenetének elkészítése (például egy adott ügyfél 20 legfontosabb terméke), és ezt használja a személyre szabott beviteli műveletként.

## <a name="adding-content-safeguards-to-your-application"></a>Tartalmi óvintézkedések hozzáadása az alkalmazáshoz

Ha az alkalmazás lehetővé teszi a nagy eltérések használatát a felhasználók számára megjelenített tartalomban, és előfordulhat, hogy bizonyos tartalmak nem biztonságosak vagy nem megfelelőek egyes felhasználók számára, előre meg kell terveznie, hogy a megfelelő védelmet biztosítson, hogy a felhasználók ne láthassák elfogadhatatlan tartalmat. A védelem megvalósításának legjobb mintája a következő:
    * A rangsorolni kívánt műveletek listájának beszerzése.
    * Szűrje ki azokat, amelyek nem életképesek a közönség számára.
    * Csak a következő életképes műveletek rangsorolása.
    * Jelenítse meg a legjobban rangsorolt műveletet a felhasználó számára.

Egyes architektúrákban a fenti sorozatot nehéz lehet megvalósítani. Ebben az esetben létezik egy alternatív módszer a védelmi feladatok rangsorolás utáni megvalósítására, de ki kell alakítani egy kiépítést, hogy a védelemen kívül eső műveletek ne legyenek felhasználva a személyre szabott modell betanításához.

* Szerezze be a rangsorban szereplő műveletek listáját, a tanulás inaktiválva.
* Rangsoroló műveletek.
* Ellenőrizze, hogy a legfelső szintű művelet életképes-e.
    * Ha a legfelső szintű művelet életképes, aktiválja a tanulást ebben a rangsorban, majd jelenítse meg a felhasználót.
    * Ha a legfelső szintű művelet nem életképes, ne aktiválja a betanítást ehhez a rangsoroláshoz, és döntse el saját logikáját vagy alternatív megközelítéseit a felhasználónak való megjelenítéshez. Még ha a második legjobb rangsorolási lehetőséget is használja, ne aktiválja a tanulást ehhez a rangsoroláshoz.


## <a name="next-steps"></a>Következő lépések

[Etikai & felelős használat](ethics-responsible-use.md).
