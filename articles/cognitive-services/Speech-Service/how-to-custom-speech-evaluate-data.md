---
title: Custom Speech pontosságának kiértékelése és javítása – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Ebből a dokumentumból megtudhatja, hogyan mérhető és javítható a beszédfelismerési modell és az egyéni modell minősége. A hang-és az emberi-címkével ellátott átírási adatoknak a pontosság teszteléséhez, valamint 30 perc és 5 órányi reprezentatív hang megadása szükséges.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.openlocfilehash: b7e4ea586098ea3eb0dfd684650f798d7988e18b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100634583"
---
# <a name="evaluate-and-improve-custom-speech-accuracy"></a>A Custom Speech pontosságának értékelése és javítása

Ebből a cikkből megtudhatja, hogyan mérjük és javíthatja a Microsoft beszéd-szöveg modelljeinek és saját egyéni modelljeinek pontosságát. A hang-és az emberi-címkével ellátott átírási adatoknak a pontosság teszteléséhez, valamint 30 perc és 5 órányi reprezentatív hang megadása szükséges.

## <a name="evaluate-custom-speech-accuracy"></a>A Custom Speech pontosságának értékelése

A modell pontosságának méréséhez használt iparági szabvány a [Word Error Rate](https://en.wikipedia.org/wiki/Word_error_rate) (WER). A WER megszámolja az elismerés során azonosított helytelen szavak számát, majd az emberi címkével ellátott átiratban szereplő szavak teljes számát (N). Végezetül ezt a számot 100%-kal kell megszorozni a WER kiszámításához.

![WER-képlet](./media/custom-speech/custom-speech-wer-formula.png)

A helytelenül azonosított szavak három kategóriába sorolhatók:

* Beszúrás (I): helytelenül hozzáadott szavak a hipotézis átiratában
* Törlés (D): a hipotézis átiratában nem észlelhető szavak
* Helyettesítő (k): a hivatkozás és a hipotézis között helyettesített szavak

Bemutatunk egy példát:

![Helytelenül azonosított szavak – példa](./media/custom-speech/custom-speech-dis-words.png)

Ha helyileg szeretné replikálni a WER-méréseket, használhatja a sclite-t a [SCTK](https://github.com/usnistgov/SCTK)-ből.

## <a name="resolve-errors-and-improve-wer"></a>Hibák elhárítása és a WER fejlesztése

Az alkalmazással, eszközzel vagy termékkel használt modell minőségének kiértékeléséhez a számítógép-felismerési eredményekből használhatja a WERt. Az 5%-10%-os WER jó minőségűnek számít, és készen áll a használatra. 20%-os WER elfogadható, azonban érdemes lehet további képzést is megfontolni. A WER 30%-os vagy újabb gyenge minőséggel rendelkezik, és testreszabást és képzést igényel.

Fontos a hibák elosztása. Ha a rendszer sok törlési hibát észlel, általában a gyenge hangjelek erőssége miatt. A probléma megoldásához a hangadatokat közelebb kell gyűjteni a forráshoz. A beillesztési hibák azt jelentik, hogy a hang zajos környezetben lett rögzítve, és lehetséges, hogy az áthallási problémákat okoz. A helyettesítési hibák akkor fordulnak elő, ha a tartományhoz tartozó feltételek nem elégséges mintája emberi feliratú átiratként vagy kapcsolódó szövegként van megadva.

Az egyes fájlok elemzésével meghatározhatja, hogy milyen típusú hibák léteznek, és mely hibák egyediek egy adott fájlra vonatkozóan. A fájlok szintjén megjelenő problémák segítenek megcélozni a fejlesztési funkciókat.

## <a name="create-a-test"></a>Teszt létrehozása

Ha szeretné kipróbálni a Microsoft beszéd-szöveg típusú alapmodelljét vagy egy Ön által betanított egyéni modellt, összehasonlíthatja a két modellt egymás mellett, hogy kiértékelje a pontosságot. Az összehasonlítás magában foglalja a WER és a felismerés eredményeit. Az egyéni modelleket általában a Microsoft alapmodellje hasonlítja össze.

Modellek kiértékelése egymás mellett:

1. Jelentkezzen be a [Custom Speech portálra](https://speech.microsoft.com/customspeech).
2. Navigáljon a **beszéd – szöveg > Custom Speech > [a projekt neve] > tesztelése** elemre.
3. Kattintson a **teszt hozzáadása** gombra.
4. Válassza ki a **pontosság kiértékelése** elemet. Adja meg a teszt nevét, leírását, és válassza ki a hang + emberi-címkével ellátott átírási adatkészletet.
5. Válasszon legfeljebb két modellt, amelyeket szeretne tesztelni.
6. Kattintson a **Létrehozás** lehetőségre.

A teszt sikeres létrehozása után összehasonlíthatja az eredményeket egymás mellett.

### <a name="side-by-side-comparison"></a>Párhuzamos összehasonlítás

A teszt befejezése után az állapot változása *sikeres* volt, a tesztben szereplő mindkét modellhez meg kell adni egy wer-számot. Kattintson a teszt nevére a tesztelési részletek oldal megtekintéséhez. Ez a részletes lap felsorolja az adatkészlet összes hosszúságú kimondott szöveg, amely a két modell felismerési eredményét jelzi a beküldött adatkészlet átírása mellett. Az egymás melletti összehasonlítások megvizsgálása érdekében különböző típusú hibákat válthat ki, beleértve a beszúrást, a törlést és a helyettesítést is. Ha az egyes oszlopokban figyeli a hangot, és összehasonlítja a felismerés eredményét, amely az emberi címkével ellátott átírást és az eredményeket mutatja két beszéd – szöveg modell esetében, eldöntheti, hogy melyik modell megfelel az igényeinek, és további képzésre és tökéletesítésekre van szükség.

## <a name="improve-custom-speech-accuracy"></a>A Custom Speech pontosságának javítása

A beszédfelismerési forgatókönyvek a hangminőség és a nyelv (szókincs és beszéd stílus) szerint változnak. A következő táblázat a négy gyakori forgatókönyvet vizsgálja:

| Eset | Hangminőség | Szókincs | Beszéd stílusa |
|----------|---------------|------------|----------------|
| Call Center | Az alacsony, 8 kHz-es lehet 2 ember 1 hangcsatornán, tömörítve | Keskeny, egyedi tartományhoz és termékekhez | Társalgási, lazán strukturált |
| Hangsegéd (például Cortana vagy áteresztő ablak) | Magas, 16 kHz | Entitás – nehéz (Song titles, Products, locations) | Egyértelműen megadott szavak és kifejezések |
| Diktálás (azonnali üzenet, megjegyzések, keresés) | Magas, 16 kHz | Változatos | Megjegyzés – bevétel |
| Videós kódolt feliratok | Változatos, beleértve a különböző mikrofon-használatot, zenéket is hozzáadva | Változatos, a megbeszélések, a beszéd, a musical lyrics | Olvasás, előkészített vagy lazán strukturált |

A különböző forgatókönyvek eltérő minőségi eredményeket hoznak létre. Az alábbi táblázat azt vizsgálja, hogy a négy forgatókönyv tartalma milyen arányban szerepel a [Word Error Rate (WER)](how-to-custom-speech-evaluate-data.md)alapján. A táblázat az egyes forgatókönyvekben leggyakrabban használt hibákat mutatja.

| Eset | Beszédfelismerési minőség | Beszúrási hibák | Törlési hibák | Helyettesítési hibák |
|----------|----------------------------|------------------|-----------------|---------------------|
| Call Center | Közepes (< 30% WER) | Alacsony, kivéve, ha mások a háttérben beszélgetnek | Magas lehet. A Call Centers lehet zajos, az átfedésben lévő hangszórók pedig megzavarják a modellt | Közepes. A termékek és a személyek nevei okozhatják ezeket a hibákat |
| Hangvezérelt asszisztens | Magas (lehet < 10% WER) | Alacsony | Alacsony | Közepes, a dalok címei, terméknév vagy helyei miatt |
| Diktálás | Magas (lehet < 10% WER) | Alacsony | Alacsony | Magas |
| Videós kódolt feliratok | A videó típusától függ (lehet < 50% WER) | Alacsony | Zene, zajok, mikrofon minősége miatt magas lehet | A szakzsargon okozhatják ezeket a hibákat |

A WER összetevőinek (a Beszúrás, a törlés és a behelyettesítési hibák száma) meghatározása segít meghatározni, hogy milyen típusú adattípust kell hozzáadni a modell tökéletesítéséhez. Az alapmodellek minőségének megtekintéséhez használja a [Custom Speech portált](https://speech.microsoft.com/customspeech) . A portál a WER minőségi díjszabásában összevont beszúrási, helyettesítési és törlési hibák arányát jelenti.

## <a name="improve-model-recognition"></a>A modell felismerésének javítása

A [Custom Speech-portálon](https://speech.microsoft.com/customspeech)betanítási adatmennyiségek hozzáadásával csökkentheti a felismerési hibákat. 

Tervezze meg az egyéni modell karbantartását úgy, hogy rendszeresen hozzáadja a forrás-anyagokat. Az egyéni modellnek további képzésre van szüksége ahhoz, hogy tisztában legyen az entitások módosításaival. Előfordulhat például, hogy frissítenie kell a terméknév, a zeneszám neve vagy az új szolgáltatási helyet.

A következő szakaszok azt ismertetik, hogy a további betanítási információk milyen módon csökkenthetők a hibákkal.

### <a name="add-related-text-sentences"></a>Kapcsolódó szöveges mondatok hozzáadása

Új egyéni modell betanításakor Kezdje a kapcsolódó szöveg hozzáadásával, hogy javítsa a tartományra vonatkozó szavak és kifejezések felismerését. A kapcsolódó szöveges mondatok elsősorban csökkentik a gyakori szavak és a tartományra vonatkozó szavak felismerésével kapcsolatos helyettesítési hibákat, ha azok kontextusban jelennek meg. A tartományra vonatkozó szavak nem lehetnek gyakoriak vagy felkészített szavak, de a kiejtésnek Egyértelműnek kell lennie.

> [!NOTE]
> Kerülje a kapcsolódó szöveges mondatokat, például a nem felismerhető karaktereket vagy szavakat.

### <a name="add-audio-with-human-labeled-transcripts"></a>Hang hozzáadása emberi címkével ellátott átiratokkal

Az emberi címkével ellátott átiratokkal rendelkező hang a lehető legnagyobb pontosságot nyújtja, ha a hang a cél használati esetből származik. A mintáknak a beszéd teljes körére kell kiterjedniük. A kiskereskedelmi tárolóhoz tartozó Call Center például a nyári hónapokban a fürdőruhával és a napszemüveggel kapcsolatos hívásokat fogja kérni. Gondoskodjon arról, hogy a minta tartalmazza a felderíteni kívánt beszédfelismerés teljes körét.

Tekintse meg az alábbi adatokat:

* A hanggal való képzés a legtöbb előnyt biztosítja, ha a hang az emberek számára is nehezen érthető. A legtöbb esetben a betanítást csak a kapcsolódó szöveg használatával kezdheti meg.
* Ha a leggyakrabban használt nyelveket használja, például az USA-angol nyelvet, jó esély van arra, hogy a hangadatokkal nem kell betanítani. Ilyen nyelveken az alapmodellek a legtöbb esetben már nagyon jó felismerési eredményeket kínálnak. valószínűleg elég a kapcsolódó szöveggel betanítani.
* A Custom Speech csak a szavak kontextusát tudja rögzíteni a helyettesítési hibák, a beszúrási és a törlési hibák csökkentése érdekében.
* Kerülje az átírási hibákat tartalmazó mintákat, de a hangminőség sokféleségét is.
* Kerülje azokat a mondatokat, amelyek nem kapcsolódnak a problémás tartományhoz. A nem kapcsolódó mondatok károsíthatják a modellt.
* Ha az átiratok minősége változó, a kivételesen jó mondatok (például a kiváló átiratok, amelyek tartalmazzák a kulcsfontosságú kifejezéseket) megismétlődnek a súlyozásuk növeléséhez.
* A beszédfelismerési szolgáltatás automatikusan az átiratokkal javítja a tartományra vonatkozó szavak és kifejezések felismerését, mintha a hozzá kapcsolódó szövegként lett hozzáadva.
* A betanítási művelet elvégzése több napot is igénybe vehet. A képzés sebességének növelése érdekében mindenképpen hozzon létre egy olyan régióban a Speech Service-előfizetést, [amely a dedikált hardverrel](custom-speech-overview.md#set-up-your-azure-account) rendelkezik.

> [!NOTE]
> Nem minden alapmodell támogatja a hangfelvételt. Ha egy alapmodell nem támogatja azt, a beszédfelismerési szolgáltatás csak az átiratokból származó szöveget fogja használni, és figyelmen kívül hagyja a hangot. A hangadatokkal való képzést támogató alapmodellek listáját a [nyelvi támogatásban](language-support.md#speech-to-text) találhatja meg. Még ha az alapmodell is támogatja a hangadatokkal való képzést, a szolgáltatás csak a hang egy részét használja. Továbbra is az összes átiratot fogja használni.

> [!NOTE]
> Abban az esetben, ha megváltoztatja a betanításhoz használt alapmodellt, és a betanítási adatkészletben van hang, *mindig* győződjön meg arról, hogy az új kiválasztott alapmodell támogatja-e a [hangadatokkal való képzést](language-support.md#speech-to-text). Ha a korábban használt alapmodell nem támogatja a hangadatokkal való betanítást, és a betanítási adatkészlet hang-és betanítási időt tartalmaz, az új alapmodellel **jelentősen** növekedni fog, és könnyen elvégezhető több óra és több nap között. Ez különösen akkor igaz, ha a beszédfelismerési szolgáltatás előfizetése **nincs** olyan [régióban, ahol a dedikált hardvert](custom-speech-overview.md#set-up-your-azure-account) betanítják.
>
> Ha a fenti bekezdésben ismertetett probléma merül fel, gyorsan csökkentheti a betanítási időt, ha csökkenti az adatkészletben lévő hang mennyiségét, vagy teljesen eltávolítja, és csak a szöveget hagyja. Az utóbbi lehetőség kifejezetten ajánlott, ha a beszédfelismerési szolgáltatás előfizetése **nincs** olyan [régióban, ahol a dedikált hardver](custom-speech-overview.md#set-up-your-azure-account) betanítása történik.

### <a name="add-new-words-with-pronunciation"></a>Új szavak hozzáadása a kiejtéssel

A létrehozott vagy különösen specializált szavak egyedi kiejtésekkel rendelkezhetnek. Ezeket a szavakat fel lehet ismerni, ha a szó kisebb szavakra bontható. Ha például fel szeretné ismerni az **xboxot**, "as **X Box**". Ez a megközelítés nem növeli az általános pontosságot, de a kulcsszavak felismerését is növelheti.

> [!NOTE]
> Ez a technika jelenleg csak bizonyos nyelvekhez érhető el. A részletekért tekintse meg a kiejtés testreszabását [a beszédfelismerési táblázatban](language-support.md) .

## <a name="sources-by-scenario"></a>Források forgatókönyv szerint

A következő táblázat a hangfelismerési forgatókönyveket mutatja be, és felsorolja azokat a forrás anyagokat, amelyeket figyelembe kell venni a fent felsorolt három képzési tartalom kategóriáján belül.

| Eset | Kapcsolódó szöveges mondatok | Hang + emberi – címkézett átiratok | Új szavak a kiejtéssel |
|----------|------------------------|------------------------------|------------------------------|
| Call Center             | marketing dokumentumok, webhely, a Call Center tevékenységgel kapcsolatos termékekkel kapcsolatos felülvizsgálatok | az emberek által átadott Call Center-hívások | nem egyértelmű kiejtésekkel rendelkező kifejezések (lásd a fenti Xbox-t) |
| Hangvezérelt asszisztens         | mondatok listázása a parancsok és entitások összes kombinációjának használatával | hangokat rögzíthet az eszközön, és szöveget is leír | egyedi kiejtésekkel rendelkező nevek (filmek, dalok, termékek) |
| Diktálás               | írásos bevitel, például azonnali üzenetek vagy e-mailek | a fentiekhez hasonlóan | a fentiekhez hasonlóan |
| Videós kódolt feliratok | TV show-szkriptek, filmek, marketing-tartalmak, videó-összefoglalók | videók pontos átiratai | a fentiekhez hasonlóan |

## <a name="next-steps"></a>Következő lépések

* [Modell betanítása és üzembe helyezése](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>További források

* [Az adatfeldolgozás előkészítése és tesztelése](./how-to-custom-speech-test-and-train.md)
* [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)