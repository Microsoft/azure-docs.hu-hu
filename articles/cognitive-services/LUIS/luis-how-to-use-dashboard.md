---
title: Irányítópult – Language Understanding – LUIS
titleSuffix: Azure Cognitive Services
description: Javítsa ki a leképezéseket és az entitásokat a betanított alkalmazás irányítópultján. Az irányítópult az alkalmazás általános információit jeleníti meg, és kiemeli azokat a leképezéseket, amelyeket rögzíteni kell.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/08/2019
ms.openlocfilehash: 4867a065a85fab1e4abc7f19401239e5b76e1da4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91541407"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Az alkalmazás fejlesztése az irányítópult használatával

Ha például hosszúságú kimondott szöveg használ, megkeresheti és kijavíthatja a betanított alkalmazások szándékait. Az irányítópult az alkalmazás általános információit jeleníti meg, és kiemeli azokat a leképezéseket, amelyeket rögzíteni kell.

Az irányítópultok elemzésének áttekintése egy iterációs folyamat, amellyel megismételhető a modell módosítása és fejlesztése.

Ezen a lapon nem lesznek releváns elemzések olyan alkalmazásokhoz, amelyek nem rendelkeznek példákkal hosszúságú kimondott szöveg, más néven _csak minta_ alkalmazások.

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Milyen problémákat lehet kijavítani az irányítópultról?

Az irányítópulton a három probléma a következőkkel foglalkozik:

|Probléma|Diagram színe|Magyarázat|
|--|--|--|
|Adategyensúlyhiány|-|Ez akkor fordul elő, ha a hosszúságú kimondott szöveg mennyisége jelentősen eltér. Az összes szándéknak _nagyjából_ azonos számú példát kell hosszúságú kimondott szöveg – kivéve a none szándékot. Az alkalmazásban a hosszúságú kimondott szöveg teljes mennyiségének 10%-15%-át kell tartalmaznia.<br><br> Ha az adatok kiegyensúlyozva vannak, de a cél pontossága bizonyos küszöbérték felett van, akkor ez az egyensúlyhiány nem jelent problémát.<br><br>**Kezdje ezzel a problémával – ez lehet a többi probléma kiváltó oka.**|
|Nem egyértelmű előrejelzések|Narancssárga|Ez akkor fordulhat elő, ha a legfelső szintű szándék és a következő mutató pontszámai elég közel vannak ahhoz, hogy a következő képzésen is meghaladják őket, mert [negatív mintavételezés](luis-how-to-train.md#train-with-all-data) vagy több, a szándékhoz hozzáadott példa hosszúságú kimondott szöveg. |
|Helytelen előrejelzések|Piros|Ez akkor fordul elő, ha a címkével ellátott szándék (a szándéka) esetében nem várható meg a Kimondás.|

A helyes előrejelzések a kék színnel jelennek meg.

Az irányítópult ezeket a problémákat jeleníti meg, és jelzi, hogy mely szándékok érintettek, és azt javasolja, hogy mit kell tennie az alkalmazás fejlesztéséhez.

## <a name="before-app-is-trained"></a>Az alkalmazás képzése előtt

Az alkalmazás betanítása előtt az irányítópult nem tartalmaz javaslatokat a javításokhoz. Az alkalmazás betanításával tekintheti meg ezeket a javaslatokat.

## <a name="check-your-publishing-status"></a>A közzétételi állapot keresése

A **közzétételi állapot** kártya az aktív verzió utolsó közzétételével kapcsolatos információkat tartalmaz.

Győződjön meg arról, hogy az aktív verzió a javítani kívánt verzió.

![Az irányítópulton az alkalmazás külső szolgáltatásai, a közzétett régiók és az összesített végpontok találatai láthatók.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Ez megjeleníti a külső szolgáltatásokat, a közzétett régiókat és az összesített végpontok találatait is.

## <a name="review-training-evaluation"></a>A betanítás értékelésének áttekintése

A **betanítási kiértékelési** kártya az alkalmazás teljes pontossága terület összesített összegzését tartalmazza. A pontszám a szándék minőségét jelzi.

![A betanítási kiértékelési kártya az alkalmazás általános pontosságával kapcsolatos első adatterületet tartalmazza.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

A diagram a helyesen megjósolt szándékokat és a különböző színekkel rendelkező problémás területeket jelzi. Az alkalmazásnak a javaslatokkal való fejlesztése során ez a pontszám nő.

A javasolt javítások a probléma típusa szerint vannak elválasztva, és az alkalmazás legjelentősebb része. Ha szeretné áttekinteni és elhárítani a hibákat, használja az oldal alján található **[hibákkal rendelkező leképezéseket](#intents-with-errors)** .

Minden egyes problémás térségnek meg kell oldania a szándékait. Ha kiválasztja a leképezés nevét, a **szándék** lap megnyílik a hosszúságú kimondott szöveg alkalmazott szűrővel. Ez a szűrő lehetővé teszi, hogy a problémát okozó hosszúságú kimondott szöveg koncentráljon.

### <a name="compare-changes-across-versions"></a>Változások összehasonlítása verziók között

Az alkalmazás módosítása előtt hozzon létre egy új verziót. Az új verzióban végezze el a javasolt módosításokat a cél példa hosszúságú kimondott szöveg, majd a tanítás újra. Az irányítópult lap **betanítási kiértékelési** kártyáján a változtatások összevetéséhez használja a **Megjelenítés a betanított verzióról** lehetőséget.

![Változások összehasonlítása verziók között](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>A verzió javítása például hosszúságú kimondott szöveg és-átképzések hozzáadásával vagy szerkesztésével

Az alkalmazás javításának elsődleges módszere a példa hosszúságú kimondott szöveg és-betanításának hozzáadása vagy szerkesztése. Az új vagy módosított hosszúságú kimondott szöveg követnie kell a [különböző hosszúságú kimondott szöveg](luis-concept-utterance.md)vonatkozó irányelveket.

A példaként szolgáló hosszúságú kimondott szöveg olyan személynek kell elvégeznie, aki:

* nagy mértékben megértette, hogy milyen hosszúságú kimondott szöveg vannak a különböző szándékok.
* tudja, hogyan tévesztendő össze a hosszúságú kimondott szöveg egy másik szándékkal.
* eldöntheti, hogy két szándékkal, amelyek gyakran zavarosak egymással, egyetlen szándékkal kell összecsukni. Ebben az esetben a különböző adategységeket kell kihúzni.

### <a name="patterns-and-phrase-lists"></a>Minták és kifejezések listája

Az elemzési lap nem jelzi, hogy mikor használjon [mintázatokat](luis-concept-patterns.md) vagy [kifejezéseket tartalmazó listát](luis-concept-feature.md). Ha hozzáadja őket, akkor a helytelen vagy nem egyértelmű előrejelzésekkel segíthet, de nem segít az adategyensúlyhiányban.

### <a name="review-data-imbalance"></a>Az adategyensúlyhiány áttekintése

Kezdje ezzel a problémával – ez lehet a többi probléma kiváltó oka.

Az **adategyensúlyhiány** -leképezési lista olyan leképezéseket mutat be, amelyeknek további hosszúságú kimondott szöveg van szükségük az adategyensúlyhiány kijavítani.

**A probléma megoldásához**:

* Vegyen fel további hosszúságú kimondott szöveg a szándékba, majd végezze el újból a betanítást.

Ne vegyen fel hosszúságú kimondott szöveg a none (nincs) szándékba, hacsak az az irányítópulton nem javasolt.

> [!Tip]
> Használja az oldal harmadik szakaszát a **hosszúságú kimondott szöveg (Number)** beállítással, amely egy gyors vizuális útmutató, amelynek **hosszúságú kimondott szöveg** több hosszúságú kimondott szöveg van szükség.
    ![Használja az "hosszúságú kimondott szöveg (szám)" lehetőséget az adategyensúlyhiány megkereséséhez.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Helytelen előrejelzések áttekintése

A **helytelen előrejelzési** leképezési lista azokat a leképezéseket jeleníti meg, amelyek hosszúságú kimondott szöveg rendelkeznek, amelyeket példaként használ egy adott szándékhoz, de a különböző szándékok alapján várható.

**A probléma megoldásához**:

* Szerkessze a hosszúságú kimondott szöveg, hogy pontosabb legyen a szándék és a képzés.
* A leképezések összevonása, ha a hosszúságú kimondott szöveg túlságosan szorosan illeszkednek, és újra betanítják őket.

### <a name="review-unclear-predictions"></a>A nem egyértelmű előrejelzések áttekintése

A nem **egyértelmű előrejelzési** szándékok listája a hosszúságú kimondott szöveg olyan előrejelzési pontszámokat jelenít meg, amelyek nem eléggé megfelelőek a legközelebbi riválistól, hogy a Kimondás legfőbb célja a [negatív mintavételezés](luis-how-to-train.md#train-with-all-data)miatt megváltozhat a következő képzésen.

**A probléma megoldásához**;

* Szerkessze a hosszúságú kimondott szöveg, hogy pontosabb legyen a szándék és a képzés.
* A leképezések összevonása, ha a hosszúságú kimondott szöveg túlságosan szorosan illeszkednek, és újra betanítják őket.

## <a name="utterances-per-intent"></a>Hosszúságú kimondott szöveg/szándék

Ez a kártya az alkalmazás teljes állapotát mutatja a szándékok között. A leképezések és az újraképzések kijavítása után folytassa a kártyán a problémákkal kapcsolatos további áttekintést.

A következő diagram egy jól kiegyensúlyozott alkalmazást mutat be, amely szinte nem oldja meg a problémát.

![A következő diagram egy jól kiegyensúlyozott alkalmazást mutat be, amely szinte nem oldja meg a problémát.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

A következő diagram egy nem megfelelően kiegyensúlyozott alkalmazást mutat be, amely számos problémát orvosol.

![A képernyőfelvételek alapján több nem egyértelmű vagy helytelenül előre jelzett eredmény jelenik meg.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Vigye az egérmutatót az egyes leképezési sávok fölé a szándékkal kapcsolatos információk lekéréséhez.

![A képernyőfelvételek a nem egyértelmű vagy helytelenül előre jelzett eredmények részleteivel jelenítik meg az előrejelzéseket.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

A **Rendezés** szempontja funkció használatával rendezheti a leképezéseket a probléma típusa alapján, így a problémával kapcsolatos problémákra koncentrálhat.

## <a name="intents-with-errors"></a>Hibákkal kapcsolatos leképezések

Ez a kártya lehetővé teszi egy adott szándékkal kapcsolatos problémák áttekintését. Ennek a kártyának az alapértelmezett nézete a legtöbb problémás szándék, hogy tudja, hol kell összpontosítania az erőfeszítéseket.

![A hibák kártyával való leképezés lehetővé teszi egy adott szándékkal kapcsolatos problémák áttekintését. A kártyát alapértelmezés szerint a legtöbb problémás szándék alapján szűri a rendszer, így biztos lehet benne, hogy hol kívánja összpontosítani az erőfeszítéseit.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

A felső fánk diagram a három problémás típussal kapcsolatos problémákat jeleníti meg. Ha problémák merülnek fel a három probléma típusában, mindegyik típushoz tartozik egy saját diagram, valamint minden rivális szándék.

### <a name="filter-intents-by-issue-and-percentage"></a>Leképezések szűrése a probléma és a százalék alapján

A kártya ezen szakasza lehetővé teszi, hogy megkeresse a hosszúságú kimondott szöveg kívül eső példákat. Ideális esetben azt szeretné, hogy a megfelelő előrejelzések jelentősek legyenek. Ez a százalék az üzleti és az ügyfél-vezérelt.

Határozza meg, hogy milyen százalékos arányt szeretne a vállalata számára.

A szűrő segítségével megtalálhatja az adott problémával kapcsolatos leképezéseket:

|Szűrő|Javasolt százalék|Cél|
|--|--|--|
|A legtöbb problémás szándék|-|**Kezdjen el ide** – a hosszúságú kimondott szöveg kijavításával az alkalmazás a többi javításnál nagyobb javulást tesz elérhetővé.|
|Az alábbi helyes előrejelzések|60%|Ez a hosszúságú kimondott szöveg százalékos aránya a kiválasztott szándékban, amelyek helyesek, de a küszöbérték alatti megbízhatósági pontszámok. |
|Nem egyértelmű előrejelzések a fentiekben|15%|Ez a kijelölt szándékban lévő hosszúságú kimondott szöveg százaléka, amely zavaros a legközelebbi rivális szándékkal.|
|Helytelen jóslatok|15%|Ez a kiválasztott szándékban helytelenül előre jelzett hosszúságú kimondott szöveg százalékos aránya. |

### <a name="correct-prediction-threshold"></a>Helyes előrejelzési küszöbérték

Mi az a biztos, hogy előrejelzési megbízhatósági pontszám? Az alkalmazásfejlesztés kezdetekor a 60% lehet a cél. Használja az **alábbi helyes előrejelzéseket** a 60%-os százalékos értékkel, hogy megkeresse a kijelölt szándékban lévő összes hosszúságú kimondott szöveg, amelyet rögzíteni kell.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Nem egyértelmű vagy helytelen előrejelzési küszöbérték

Ez a két szűrő lehetővé teszi, hogy megkeresse a hosszúságú kimondott szöveg a küszöbértéken túli kiválasztott szándékban. Ezt a két százalékot a hiba százalékában tekintheti át. Ha a jóslatok 10-15%-os hibájának arányát szeretné megállapítani, állítsa a szűrési küszöbértéket 15%-ra az érték felett található összes hosszúságú kimondott szöveg megkereséséhez.

## <a name="next-steps"></a>Következő lépések

* [Azure-erőforrások kezelése](luis-how-to-azure-subscription.md)
