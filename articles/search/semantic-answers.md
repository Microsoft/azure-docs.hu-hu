---
title: Szemantikai válasz visszaadása
titleSuffix: Azure Cognitive Search
description: Leírja egy szemantikai válasz összetételét és a válaszok beszerzését egy eredményhalmaz alapján.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 9bb62544887e0bc0269b98cd98fbf97fc477352f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722429"
---
# <a name="return-a-semantic-answer-in-azure-cognitive-search"></a>Szemantikai válasz visszaadása az Azure Cognitive Searchban

> [!IMPORTANT]
> A szemantikai keresés nyilvános előzetes verzióban érhető el, csak az előzetes verziójú REST API. Az előzetes verziójú funkciók a szolgáltatásban is elérhetők, a [kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)alatt, és nem garantált, hogy az általánosan elérhető implementációja azonos. Ezek a funkciók számlázva vannak. További információkért lásd a [rendelkezésre állást és a díjszabást](semantic-search-overview.md#availability-and-pricing).

[Szemantikai lekérdezés](semantic-how-to-query-request.md)létrehozásakor lehetősége van kinyerni a tartalmat a legfelső egyezésű dokumentumokból, amelyek közvetlenül a lekérdezésre válaszolnak. Egy vagy több válasz is szerepelhet a válaszban, amelyet aztán egy keresési oldalon lehet megjeleníteni az alkalmazás felhasználói élményének javítása érdekében.

Ebből a cikkből megtudhatja, hogyan kérhet egy szemantikai választ, kicsomagolhatja a választ, és megtudhatja, hogy milyen tartalmi jellemzőkkel bír a leghatékonyabban a kiváló minőségű válaszok előállítása.

## <a name="prerequisites"></a>Előfeltételek

A [szemantikai lekérdezésekre](semantic-how-to-query-request.md) vonatkozó összes előfeltétel a válaszokra is vonatkozik, beleértve a szolgáltatási szintet és a régiót is.

+ A lekérdezési logikának tartalmaznia kell a szemantikai lekérdezés paramétereit, valamint a "válaszok" paramétert. A szükséges paramétereket ebben a cikkben tárgyaljuk.

+ A felhasználó által megadott lekérdezési karakterláncokat olyan nyelven kell megfogalmazni, amely a kérdés jellemzőit mutatja (mi, hol, mikor, hogyan).

+ A dokumentumok keresésének tartalmaznia kell egy válasz jellemzőit tartalmazó szöveget, és a szövegnek szerepelnie kell a "searchFields" elemben felsorolt mezők egyikében. Például a "mi az a kivonatoló tábla" lekérdezés miatt, ha egyik searchFields sem tartalmaz "A kivonatoló táblát..." karaktert tartalmazó szakaszokat, akkor a válasz nem valószínű, hogy a rendszer visszaadja a választ.

## <a name="what-is-a-semantic-answer"></a>Mi az a szemantikai válasz?

A szemantikai válasz egy [szemantikai lekérdezési válasz](semantic-how-to-query-request.md)alstruktúrája. Egy vagy több, a keresési dokumentumból származó Verbatim-szakaszból áll, amely egy kérdésnek megfelelő lekérdezésre adott válaszként lett kialakítva. A visszaadott válaszhoz a kifejezéseknek vagy mondatoknak olyan keresési dokumentumban kell szerepelniük, amely a válasz nyelvi jellemzőit tartalmazza, és a lekérdezésnek kérdésként kell szerepelnie.

A Cognitive Search a legjobb válasz kiválasztásához egy gépi olvasási felolvasási modellt használ. A modell lehetséges válaszokat eredményez a rendelkezésre álló tartalomból, és ha az elég magas megbízhatósági szintet ér el, választ fog adni.

A válaszok független, legfelső szintű objektumként lesznek visszaadva a lekérdezési válasz adattartalmában, amelyet a keresési lapokon, az oldalsó keresési eredmények mellett is választhat. Szerkezetileg, ez egy tömb elem a válaszban, amely szövegből, dokumentum-kulcsból és megbízhatósági pontszámból áll.

<a name="query-params"></a>

## <a name="how-to-request-semantic-answers-in-a-query"></a>Szemantikai válaszok kérése lekérdezésekben

Szemantikai válasz visszaadásához a lekérdezésnek tartalmaznia kell a szemantikai "queryType", a "queryLanguage", a "searchFields" és a "Answers" paramétert. A "válaszok" paraméter megadásával nem garantálható, hogy a rendszer választ kapjon, de a kérésnek tartalmaznia kell ezt a paramétert, ha a válasz feldolgozását egyáltalán meg kell hívni.

A "searchFields" paraméter rendkívül fontos, hogy magas színvonalú választ adjon vissza mind a tartalom, mind a sorrend alapján (lásd alább). 

```json
{
    "search": "how do clouds form",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "title,locations,content",
    "answers": "extractive|count-3",
    "count": "true"
}
```

+ A lekérdezési karakterlánc nem lehet null értékű, és kérdésként kell megfogalmazni. Ebben az előzetes verzióban a "queryType" és a "queryLanguage" beállításnak pontosan a példában látható módon kell megadnia.

+ A "searchFields" paraméter határozza meg, hogy mely karakterlánc-mezők biztosítanak jogkivonatokat a kinyerési modell számára. Ugyanazokat a mezőket hozza létre, amelyek feliratok is előállítanak válaszokat. Ha részletes útmutatást szeretne arról, hogyan állíthatja be ezt a mezőt úgy, hogy mind a feliratok, mind a válaszok esetében működjön, tekintse meg a [SearchFields beállítása](semantic-how-to-query-request.md#searchfields)című témakört. 

+ A "válaszok" esetében a paraméterek építése az `"answers": "extractive"` , ahol a visszaadott válaszok alapértelmezett száma egy. Megnövelheti a válaszok számát úgy, hogy hozzáad egy számot a fenti példában látható módon, legfeljebb öt.  Akár egynél több válaszra van szüksége, az alkalmazás felhasználói élménytől függ, és hogyan szeretné megjeleníteni az eredményeket.

## <a name="deconstruct-an-answer-from-the-response"></a>Válasz kiépítése a válaszból

A válaszok a @search.answers tömbben vannak megadva, amely a válaszban elsőként jelenik meg. Ha a válasz meghatározatlan, a válasz a következőként jelenik meg: `"@search.answers": []` . A válaszokat tartalmazó keresési eredmények oldalának tervezésekor ügyeljen arra, hogy olyan eseteket kezeljen, ahol a válaszok nem találhatók.

A (z @search.answers ) "Key" (kulcs) az egyezéshez tartozó dokumentum kulcsa vagy azonosítója. A dokumentum kulcsa alapján [a keresési dokumentum API-](/rest/api/searchservice/lookup-document) val lekérheti a keresési dokumentum bármely vagy összes részét, hogy az szerepeljen a keresési oldalon vagy a Részletek lapon.

Mind a "text", mind a "Highlights" azonos tartalmat biztosít mind egyszerű szövegben, mind pedig kiemeli. Alapértelmezés szerint a csúcsfények a saját stílusát használják `<em>` , amelyeket a meglévő highlightPreTag és highlightPostTag paraméterekkel lehet felülbírálni. Ahogy másutt is említettük, a válasz tartalma szó szerint egy keresési dokumentumból származik. Az extrakciós modell a válasz jellemzőit keresi a megfelelő tartalom megtalálásához, de a válaszban nem készít új nyelvet.

A "pontszám" olyan megbízhatósági pontszám, amely a válasz erősségét tükrözi. Ha a válaszban több válasz is szerepel, a rendszer ezt a pontszámot használja a rendelés meghatározásához. A leggyakoribb válaszok és a legnépszerűbb feliratok különböző keresési dokumentumokból származhatnak, ahol a legfelső szintű válasz egy dokumentumból származik, míg a felső felirat egy másikból, de általában ugyanazokat a dokumentumokat fogja látni az egyes tömbben található legfelső pozícióban.

A válaszokat a "value" tömb követi, amely mindig tartalmazza a pontszámokat, a feliratokat és az alapértelmezés szerint beolvasható mezőket. Ha a Select paramétert adta meg, az "érték" tömb a megadott mezőkre korlátozódik. A válasz elemeivel kapcsolatos további információkért lásd: [szemantikai lekérdezés létrehozása](semantic-how-to-query-request.md).

A "How Do felhők Form" lekérdezés miatt a válasz a következő választ adja vissza:

```json
{
    "@search.answers": [
        {
            "key": "4123",
            "text": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form where air is ascending (over land in this case),   but not where it is descending (over the river).",
            "highlights": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form<em> where air is ascending</em> (over land in this case),   but not where it is<em> descending</em> (over the river).",
            "score": 0.94639826
        }
    ],
    "value": [
        {
            "@search.score": 0.5479723,
            "@search.rerankerScore": 1.0321671911515296,
            "@search.captions": [
                {
                    "text": "Like all clouds, it forms when the air reaches its dew point—the temperature at which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley fog, which is common in the Pacific Northwest of North America.",
                    "highlights": "Like all<em> clouds</em>, it<em> forms</em> when the air reaches its dew point—the temperature at    which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley<em> fog</em>, which is common in the Pacific Northwest of North America."
                }
            ],
            "title": "Earth Atmosphere",
            "content": "Fog is essentially a cloud lying on the ground. Like all clouds, it forms when the air reaches its dew point—the temperature at  \n\nwhich an air mass is cool enough for its water vapor to condense into liquid droplets.\n\nThis false-color image shows valley fog, which is common in the Pacific Northwest of North America. On clear winter nights, the \n\nground and overlying air cool off rapidly, especially at high elevations. Cold air is denser than warm air, and it sinks down into the \n\nvalleys. The moist air in the valleys gets chilled to its dew point, and fog forms. If undisturbed by winds, such fog may persist for \n\ndays. The Terra satellite captured this image of foggy valleys northeast of Vancouver in February 2010.\n\n\n",
            "locations": [
                "Pacific Northwest",
                "North America",
                "Vancouver"
            ]
        }
```

## <a name="tips-for-producing-high-quality-answers"></a>Tippek magas színvonalú válaszok készítéséhez

A legjobb eredmények érdekében az alábbi jellemzőkkel rendelkező dokumentumokra vonatkozó szemantikai válaszokat kell visszaadnia:

+ a "searchFields" olyan mezőket kell biztosítania, amelyek elegendő szöveget biztosítanak, amelyben a válasz valószínűleg található. A dokumentumokban csak a Verbatim szöveg jelenhet meg válaszként.

+ a lekérdezési karakterláncok nem lehetnek null értékűek (Search = `*` ), és a sztringnek tartalmaznia kell egy kérdés jellemzőit, szemben a kulcsszavas kereséssel (az önkényes kifejezések vagy kifejezések szekvenciális listája). Ha a lekérdezési karakterlánc nem válaszol, a rendszer kihagyja a válasz feldolgozását, még akkor is, ha a kérelem lekérdezési paraméterként a "válaszok" kifejezést adja meg.

+ A szemantikai kivonás és az összefoglalás korlátozza a dokumentumok egy adott időben történő elemzéséhez szükséges jogkivonatok számát. Ha olyan nagyméretű dokumentumokkal rendelkezik, amelyek több száz oldalra futnak, érdemes a tartalmat a kisebb dokumentumokra bontani.

## <a name="next-steps"></a>Következő lépések

+ [Szemantikai keresés – áttekintés](semantic-search-overview.md)
+ [Szemantikai rangsorolási algoritmus](semantic-ranking.md)
+ [Hasonlósági rangsor algoritmusa](index-ranking-similarity.md)
+ [Szemantikai lekérdezés létrehozása](semantic-how-to-query-request.md)