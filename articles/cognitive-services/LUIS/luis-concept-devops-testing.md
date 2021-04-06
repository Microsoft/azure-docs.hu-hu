---
title: A LUIS-alkalmazások DevOps tesztelése
description: A Language Understanding (LUIS) alkalmazás tesztelése DevOps-környezetben.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/3/2020
ms.openlocfilehash: cd2fd8dc8c10864089b198db1ca1089f994a3ffb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98788451"
---
# <a name="testing-for-luis-devops"></a>A LUIS DevOps tesztelése

A Language Understanding (LUIS) alkalmazást fejlesztő szoftverfejlesztők DevOps eljárásokat alkalmazhatnak a [verziókövetés](luis-concept-devops-sourcecontrol.md), az [automatizált buildek](luis-concept-devops-automation.md), a [tesztelés](luis-concept-devops-testing.md)és a [kiadási felügyelet terén](luis-concept-devops-automation.md#release-management) az alábbi irányelvek alapján.

Az agilis szoftverfejlesztés módszereiben a tesztelés a minőségi szoftverek fejlesztése terén is szerves szerepet játszik. A LUIS-alkalmazás minden jelentős változását olyan tesztek kísérik, amelyek a fejlesztőnek az alkalmazásba való beépítését szolgáló új funkciók tesztelésére szolgálnak. Ezek a tesztek bekerülnek a forráskód-tárházba a `.lu` Luis-alkalmazás forrásával együtt. A módosítás megvalósítása akkor fejeződik be, amikor az alkalmazás megfelel a teszteknek.

A tesztek a [CI/CD-munkafolyamatok](luis-concept-devops-automation.md)kritikus részét képezik. Ha a LUIS-alkalmazás módosításait egy pull-kérelemben (PR) vagy a változtatások a fő ágra való egyesítése után javasolják, akkor a CI-munkafolyamatoknak futtatniuk kell a teszteket annak ellenőrzéséhez, hogy a frissítések nem okozott-e regressziót.

## <a name="how-to-do-unit-testing-and-batch-testing"></a>Az egység tesztelése és a kötegelt tesztelés

A folyamatos integrációs munkafolyamatokban két különböző típusú tesztet kell végrehajtania a LUIS-alkalmazáshoz:

- **Unit-tesztek** – a Luis-alkalmazás legfontosabb funkcióit ellenőrző viszonylag egyszerű tesztek. Az egység tesztelése akkor megy át, amikor a várt szándék és a várt entitások visszakerülnek egy adott tesztre. A teszt futtatásának sikeres befejezéséhez minden egység tesztnek meg kell felelnie.  
Ez a fajta tesztelés hasonló a [Luis-portálon](https://www.luis.ai/)elvégezhető [interaktív teszteléshez](./luis-concept-test.md) .

- **Batch-tesztek** – a Batch Testing egy átfogó teszt a jelenlegi betanított modellen a teljesítmény méréséhez. Az egységgel végzett tesztektól eltérően a Batch-tesztelés nem halad át | sikertelen tesztelés. A Batch-tesztelés várhatóan nem minden teszt a várt szándékú és várt entitásokat fogja visszaadni. Ehelyett egy batch-teszt segít megtekinteni az egyes szándékok és entitások pontosságát az alkalmazásban, és segít összehasonlítani az idő múlásával a fejlesztés során.  
Ez a fajta tesztelés megegyezik a [kötegelt teszteléssel](./luis-how-to-batch-test.md) , amelyet interaktív módon végezhet el a Luis-portálon.

A projekt elejéről is alkalmazhat egységes tesztelést. A Batch-tesztelés csak akkor igazán értékes, ha a LUIS-alkalmazás sémáját fejlesztette, és dolgozik a pontosságának javításán.

Az egységek és a kötegelt tesztek esetében ellenőrizze, hogy a tesztelési hosszúságú kimondott szöveg elkülönítve maradnak-e a betanítási hosszúságú kimondott szöveg. Ha ugyanazokat az adatokkal teszteli, mint a betanítás során, akkor a hamis benyomást kapja, ha az alkalmazás jól illeszkedik a tesztelési adatokhoz. A modellnek láthatatlannak kell lennie, hogy tesztelje, mennyire jól van általánosítva.

### <a name="writing-tests"></a>Tesztek írása

Ha teszteket ír, minden teszthez meg kell határoznia a következőket:

* Teszt kimondása
* Várt szándék
* Várt entitások.

A LUIS [batch-fájl szintaxisával](./luis-how-to-batch-test.md#batch-syntax-template-for-intents-with-entities) meghatározhatja a tesztek egy csoportját egy JSON-formátumú fájlban. Például:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities":
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

Egyes tesztelési eszközök, például a [NLU. A DevOps](https://github.com/microsoft/NLU.DevOps) támogatja a LUDown-formázott tesztelési fájlokat is.

#### <a name="designing-unit-tests"></a>Egységek tesztelésének tervezése

Az egységek tesztelését úgy kell kialakítani, hogy tesztelje a LUIS-alkalmazás alapvető funkcióit. Az alkalmazások fejlesztésének minden iterációjában vagy sprinten elegendő számú tesztet kell írnia annak ellenőrzéséhez, hogy az iteráció során megvalósított legfontosabb funkciók megfelelően működnek-e.

Az egyes egységeken végzett tesztekben a következőket teheti:

* A megfelelő szándék visszaadásának tesztelése
* Tesztelje, hogy a "Key" entitások – amelyek kritikus fontosságúak a megoldásban – a rendszer visszaadja.
* Ellenőrizze, hogy a szándék és az entitások [előrejelzési pontszáma](./luis-concept-prediction-score.md) meghaladja-e az Ön által meghatározott küszöbértéket. Dönthet például úgy, hogy csak akkor veszi figyelembe a tesztet, ha a szándék és a kulcsfontosságú entitások előrejelzési pontszáma meghaladja a 0,75-ot.

Az egységbeli tesztek során érdemes tesztelni, hogy a kulcsfontosságú entitások visszakerültek-e az előrejelzési válaszba, de figyelmen kívül hagyja a hamis pozitív értékeket. A *hamis pozitív érték* az előrejelzési válaszban található entitások, amelyek nincsenek meghatározva a teszt várt eredményei között. Ha figyelmen kívül hagyja a hamis pozitív értékeket, kevésbé terheli az egység-tesztek elkészítését, miközben továbbra is lehetővé teszi, hogy a megoldáshoz tartozó, a megoldáshoz tartozó adatmennyiséget előrejelzési válaszként adja vissza.

> [!TIP]
> A [NLU. A DevOps](https://github.com/microsoft/NLU.DevOps) eszköz támogatja a Luis tesztelési igényeket. Az `compare` [egység tesztelési módjában](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) használt parancs azt állítja be, hogy az összes teszt sikeres lesz, és figyelmen kívül hagyja a hamis pozitív eredményeket azoknál az entitásokon, amelyek nem a várt eredményekben vannak címkézve.

#### <a name="designing-batch-tests"></a>Batch-tesztek tervezése

A Batch-tesztek nagy számú tesztelési esetet tartalmaznak, amelyek az összes cél és a LUIS-alkalmazás összes entitásának tesztelésére szolgálnak. A Batch-tesztkörnyezet definiálásával kapcsolatos információkért lásd: [Batch-tesztelés a Luis portálon](./luis-how-to-batch-test.md) .

### <a name="running-tests"></a>Tesztek futtatása

A LUIS-portál olyan funkciókat kínál, amelyek segítenek az interaktív tesztelésben:

* Az [**interaktív tesztelés**](./luis-concept-test.md) lehetővé teszi, hogy beküldjön egy mintát, és választ KAPJON a Luis által felismert szándékokról és entitásokról. A teszt sikerességét a vizuális vizsgálat ellenőrzi.

* A [**Batch**](./luis-how-to-batch-test.md) Testing egy batch-tesztoldalt használ bemenetként, hogy érvényesítse az aktív betanított verziót az előrejelzési pontosság méréséhez. A Batch-tesztek segítségével megtekintheti az egyes szándékok és entitások pontosságát az aktív verzióban, és megjelenítheti az eredményeket egy diagrammal.

#### <a name="running-tests-in-an-automated-build-workflow"></a>Tesztek futtatása automatizált Build-munkafolyamatokban

A LUIS-portál interaktív tesztelési funkciói hasznosak, de a DevOps esetében a CI/CD-munkafolyamatokban elvégzett automatizált tesztelés bizonyos követelményeket támaszt:

* A tesztelési eszközöket a Build-kiszolgáló munkafolyamat-lépésében kell futtatni. Ez azt jelenti, hogy az eszközöknek képesnek kell lenniük futni a parancssorban.
* A tesztelési eszközöknek képesnek kell lenniük a tesztek egy csoportjának végrehajtására egy végponton, és automatikusan ellenőriznie kell a várt eredményeket a tényleges eredmények alapján.
* Ha a tesztek sikertelenek, a tesztelési eszközöknek állapotkódot kell visszaadniuk a munkafolyamat leállításához és a "sikertelen" létrehozáshoz.

A LUIS nem tartalmaz parancssori eszközt vagy magas szintű API-t, amely ezeket a funkciókat kínálja. Javasoljuk, hogy használja a [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) eszköz a tesztek futtatásához és az eredmények ellenőrzéséhez mind a parancssorban, mind a CI/CD-munkafolyamaton belüli automatizált tesztelés során.

A LUIS-portálon elérhető tesztelési képességek nem igényelnek közzétett végpontot, és a LUIS authoring képességek részét képezik. Ha automatikus Build-munkafolyamatban hajtja végre a tesztelést, közzé kell tennie a LUIS-alkalmazás verzióját, hogy tesztelve legyen egy végponton, hogy tesztelje az eszközöket, például a NLU. A DevOps az előrejelzési kérelmeket a tesztelés részeként küldheti el.

> [!TIP]
> * Ha saját tesztelési megoldást használ, és kódot ír a hosszúságú kimondott szöveg egy végpontra való küldéséhez, ne feledje, hogy ha a LUIS authoring Key-t használja, akkor az engedélyezett tranzakciós sebesség a 5TPS korlátozódik. A küldési arány szabályozása vagy az előrejelzési kulcs használata.
> * Ha tesztelési lekérdezéseket küld egy végpontra, ne felejtse el használni az `log=false` előrejelzési kérelem lekérdezési karakterláncában. Ez biztosítja, hogy a teszt hosszúságú kimondott szöveg ne jelentkezzen be a LUIS, és fejezze be a LUIS [Active learning](./luis-concept-review-endpoint-utterances.md) funkció által bemutatott Endpoint hosszúságú kimondott szöveg felülvizsgálati listát, így véletlenül bekerül az alkalmazás betanítási hosszúságú kimondott szöveg.

#### <a name="running-unit-tests-at-the-command-line-and-in-cicd-workflows"></a>Az egységnyi tesztek futtatása a parancssorban és a CI/CD-munkafolyamatokban

Használhatja a [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) -csomag tesztek futtatásához a parancssorban:

* Használja a NLU. A DevOps teszt [parancs](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) használatával teszteket küldhet egy adott fájlból egy végpontba, és rögzítheti a tényleges előrejelzési eredményeket egy fájlban.
* Használja a NLU. A DevOps [összehasonlítása paranccsal](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) összehasonlíthatja a tényleges eredményeket a bemeneti tesztelési fájlban definiált várt eredményekkel. A `compare` parancs NUnit generál, és ha az [egység tesztelési módban](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) használja a `--unit-test` jelzőt, a a tesztek mindegyikét megadja.

### <a name="running-batch-tests-at-the-command-line-and-in-cicd-workflows"></a>Batch-tesztek futtatása a parancssorban és a CI/CD-munkafolyamatokban

Használhatja a NLU is. DevOps-csomag a Batch-tesztek parancssorból való futtatásához.

* Használja a NLU. A DevOps [teszt paranccsal](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) teszteket küldhet egy adott végpontnak, és a tényleges előrejelzési eredményeket rögzítheti egy fájlban, ugyanúgy, mint az egységes tesztek esetében.
* Használja a NLU. Az alkalmazás teljesítményének méréséhez DevOps- [összehasonlító parancsot kell összehasonlítania](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) a teljesítmény- [tesztelési módban](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#performance-test-mode) , például az alkalmazás teljesítményének összehasonlítására az alapteljesítményre vonatkozó teljesítményteszt alapján, például a legutóbbi véglegesítés a Main vagy a jelenlegi verzióra való bevezetésének eredményét. A teljesítmény tesztelése módban a `compare` parancs a NUnit teszt kimenetét és a [Batch-teszt eredményét](./luis-glossary.md#batch-test) JSON formátumban hozza létre.

## <a name="luis-non-deterministic-training-and-the-effect-on-testing"></a>LUIS nem determinisztikus képzés és a tesztelés hatása

Ha a LUIS egy modellt, például egy szándékot használ, pozitív adatokra is szüksége van – a címkézett betanítási hosszúságú kimondott szöveg, amelyet az alkalmazás a modellhez való betanításához, illetve negatív adatadatokhoz adott meg, amelyek *nem* érvényesek példák a modell használatára. A betanítás során LUIS létrehoz egy modell negatív adatait a többi modellhez megadott pozitív adatokból, de bizonyos esetekben adategyensúlyhiányt eredményezhet. Az egyensúlyhiány elkerüléséhez LUIS a negatív adat egy részhalmazát a nem determinisztikus módon, a jobb teljesítmény és a gyorsabb képzés érdekében optimalizálja.

Ennek a nem determinisztikus-képzésnek az eredménye az, hogy a [különböző betanítási eseményekhez némileg eltérő előrejelzési választ](./luis-concept-prediction-score.md)kaphat, általában olyan szándékok és/vagy entitások esetén, ahol az [előrejelzési pontszám](./luis-concept-prediction-score.md) nem magas.

Ha szeretné letiltani a nem determinisztikus betanítást azon LUIS-alkalmazások esetében, amelyek tesztelés céljából készülnek, használja a [Version Settings API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) -t a `UseAllTrainingData` beállítás értékeként `true` .

## <a name="next-steps"></a>Következő lépések

* Tudnivalók a [CI/CD-munkafolyamatok megvalósításáról](luis-concept-devops-automation.md)
* Ismerje meg, hogyan valósítható meg a [DevOps for Luis a GitHub](luis-how-to-devops-with-github.md) használatával