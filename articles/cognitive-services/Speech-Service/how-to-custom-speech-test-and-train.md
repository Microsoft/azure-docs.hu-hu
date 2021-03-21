---
title: Az Custom Speech-Speech szolgáltatáshoz tartozó adatelőkészítés
titleSuffix: Azure Cognitive Services
description: A Microsoft beszédfelismerés pontosságának tesztelésekor vagy egyéni modelljeinek betanításakor a hang-és szöveges adatokra lesz szüksége. Ezen az oldalon bemutatjuk az adattípusokat, a használatot és a felügyeletet.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.openlocfilehash: 2c98546d20e9f977a605ccbac21010aa9b1dbadc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232494"
---
# <a name="prepare-data-for-custom-speech"></a>Adatok előkészítése a Custom Speech szolgáltatáshoz

A Microsoft beszédfelismerés pontosságának tesztelésekor vagy egyéni modelljeinek betanításakor a hang-és szöveges adatokra lesz szüksége. Ezen az oldalon az egyéni beszédfelismerési modell által igényelt adattípusokat fedi le.

## <a name="data-diversity"></a>Az adatok sokfélesége

Az egyéni modell tesztelésére és betanítására használt szöveg és hang a különböző hangszórókból származó mintákat, valamint a modell felismeréséhez szükséges forgatókönyveket kell tartalmaznia.
Vegye figyelembe ezeket a tényezőket az egyéni modellek tesztelésére és betanítására szolgáló adatok összegyűjtésekor:

* A szöveges és a beszédfelismerési hangadatoknak le kell fedniük a felhasználók által a modellel való interakció során végrehajtandó szóbeli utasítások fajtáit. Például egy olyan modell, amely felváltja és csökkenti a hőmérsékletet, a nyilatkozatokban szereplő személyeknek az ilyen jellegű módosításokat kell elvégezniük.
* Az adatainak tartalmazniuk kell az összes olyan beszédfelismerési eltérést, amelyet a modellnek fel kell ismernie. Számos tényező eltérő lehet a beszéd, például az ékezetek, a dialektusok, a nyelvtanulás, a kor, a nemek, a hangfelvételi szint, a stressz szintje és a napszak.
* Különböző környezetekben (beltéri, kültéri, közúti zaj) lévő mintákat kell tartalmaznia, ahol a modellt használni fogja.
* A hanganyagot az éles rendszer által használt hardvereszközök használatával kell összegyűjteni. Ha a modellnek azonosítania kell a különböző minőségű eszközökön rögzített hanganyagot, a modell betanításához megadott hangadatoknak is ezeket a különböző forgatókönyveket kell tartalmazniuk.
* Később további adatokat adhat hozzá a modellhez, de gondoskodhat arról, hogy az adatkészlet sokrétű és a projekt igényeinek megfelelően legyen látható.
* Az egyéni modell-felismerési igényekhez *nem* tartozó olyan adatmennyiségeket is tartalmazhat, amelyek a modell által nem igényelt adatmennyiséget befolyásolják.

A forgatókönyvek egy részhalmazára betanított modell csak jól tudja elvégezni ezeket a forgatókönyveket. Gondosan válassza ki azokat az adattípusokat, amelyek az egyéni modell felismeréséhez szükséges forgatókönyvek teljes körét jelölik.

> [!TIP]
> Kezdjen el kis mennyiségű mintaadatok közül, amelyek megfelelnek a modellnek.
> Például rögzítheti egy kis, de reprezentatív hangmintát ugyanazon a hardveren, és ugyanabban az akusztikai környezetben, amelyet a modell éles környezetben talál.
> A reprezentatív adat kis adatkészletei problémákat okozhatnak, mielőtt befektetett egy sokkal nagyobb adatkészletbe a képzéshez.
>
> A gyors kezdéshez vegye fontolóra a mintaadatok használatát. Tekintse meg a GitHub-tárházat a <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">minta Custom Speech </a>

## <a name="data-types"></a>Adattípusok

Ez a táblázat felsorolja az elfogadott adattípusokat, valamint az egyes adattípusok használatát, valamint az ajánlott mennyiséget. Nem minden adattípus szükséges a modell létrehozásához. Az adatkövetelmények attól függően változnak, hogy szeretne-e tesztet létrehozni vagy egy modellt betanítani.

| Adattípus | Teszteléshez használatos | Ajánlott mennyiség | Képzéshez használatos | Ajánlott mennyiség |
|-----------|-----------------|----------|-------------------|----------|
| [Audió](#audio-data-for-testing) | Yes<br>Vizuális vizsgálathoz használatos | 5 + hangfájl | No | N/A |
| [Hang + emberi – címkézett átiratok](#audio--human-labeled-transcript-data-for-testingtraining) | Yes<br>A pontosság kiértékeléséhez használatos | 0,5-5 órányi hang | Yes | 1-20 órányi hang |
| [Kapcsolódó szöveg](#related-text-data-for-training) | No | N/a | Yes | 1-200 MB kapcsolódó szöveg |

A fájlokat típus szerint kell csoportosítani egy adatkészletbe, és. zip fájlként kell feltölteni. Az egyes adatkészletek csak egyetlen adattípust tartalmazhatnak.

> [!TIP]
> Új modell betanításakor Kezdje a [kapcsolódó szöveggel](#related-text-data-for-training). Ezek az adatmennyiségek már javítják a speciális kifejezések és kifejezések felismerését. A szöveggel való képzés sokkal gyorsabb, mint a hanggal való képzés (perc és nap).

> [!NOTE]
> Nem minden alapmodell támogatja a hangfelvételt. Ha egy alapmodell nem támogatja azt, a beszédfelismerési szolgáltatás csak az átiratokból származó szöveget fogja használni, és figyelmen kívül hagyja a hangot. A hangadatokkal való képzést támogató alapmodellek listáját a [nyelvi támogatásban](language-support.md#speech-to-text) találhatja meg. Még ha az alapmodell is támogatja a hangadatokkal való képzést, a szolgáltatás csak a hang egy részét használja. Továbbra is az összes átiratot fogja használni.
>
> Abban az esetben, ha megváltoztatja a betanításhoz használt alapmodellt, és a betanítási adatkészletben van hang, *mindig* győződjön meg arról, hogy az új kiválasztott alapmodell támogatja-e a [hangadatokkal való képzést](language-support.md#speech-to-text). Ha a korábban használt alapmodell nem támogatja a hangadatokkal való betanítást, és a betanítási adatkészlet hang-és betanítási időt tartalmaz, az új alapmodellel **jelentősen** növekedni fog, és könnyen elvégezhető több óra és több nap között. Ez különösen akkor igaz, ha a beszédfelismerési szolgáltatás előfizetése **nincs** olyan [régióban, ahol a dedikált hardvert](custom-speech-overview.md#set-up-your-azure-account) betanítják.
>
> Ha a fenti bekezdésben ismertetett probléma merül fel, gyorsan csökkentheti a betanítási időt, ha csökkenti az adatkészletben lévő hang mennyiségét, vagy teljesen eltávolítja, és csak a szöveget hagyja. Az utóbbi lehetőség kifejezetten ajánlott, ha a beszédfelismerési szolgáltatás előfizetése **nincs** olyan [régióban, ahol a dedikált hardver](custom-speech-overview.md#set-up-your-azure-account) betanítása történik.
>
> A dedikált hardverrel rendelkező régiókban a beszédfelismerési szolgáltatás akár 20 órányi hang használatát is felhasználhatja a képzéshez. Más régiókban a szolgáltatás legfeljebb 8 órányi hang használatát fogja használni.

## <a name="upload-data"></a>Adatok feltöltése

Az adatok feltöltéséhez navigáljon a <a href="https://speech.microsoft.com/customspeech" target="_blank">Speech studióhoz </a>. A portálon kattintson az **adatok feltöltése** elemre a varázsló indításához és az első adatkészlet létrehozásához. Az adatok feltöltésének engedélyezése előtt meg kell adnia, hogy ki kell választania egy beszédfelismerési adattípust az adatkészlethez.

![Képernyőfelvétel a hangfeltöltési lehetőségről a beszédfelismerési portálról.](./media/custom-speech/custom-speech-select-audio.png)

A feltöltött adatkészleteknek meg kell felelniük a választott adattípusra vonatkozó követelményeknek. Az adatait a feltöltés előtt helyesen kell formázni. A helyesen formázott adat biztosítja, hogy a Custom Speech szolgáltatás pontosan dolgozza fel azokat. A követelmények a következő részekben találhatók.

Az adatkészlet feltöltése után néhány lehetőség közül választhat:

* Navigáljon a **Testing (tesztelés** ) lapra, és vizuálisan vizsgálja meg a csak hang-és hang-és emberi-címkézett átírási adatok.
* Az egyéni modell betanításához navigáljon a **betanítás** lapra, és használja a hang + emberi átírási adatok vagy a kapcsolódó szöveges adatok használatát.

## <a name="audio-data-for-testing"></a>Hangadatok teszteléshez

A hangadatok optimálisan tesztelik a Microsoft alapvető beszédfelismerési modelljét vagy egyéni modelljét. Ne feledje, hogy a hangadatok segítségével megvizsgálhatja a beszéd pontosságát egy adott modell teljesítményének tekintetében. Ha számszerűsíteni szeretné a modell pontosságát, használja a [hang + emberi feliratú átírási adatok](#audio--human-labeled-transcript-data-for-testingtraining)lehetőséget.

Ezzel a táblázattal ellenőrizheti, hogy a hangfájlok formátuma helyesen van-e formázva a Custom Speech használatával:

| Tulajdonság                 | Érték                 |
|--------------------------|-----------------------|
| Fájlformátum              | RIFF (WAV)            |
| Mintavételezési arány              | 8 000 Hz vagy 16 000 Hz |
| Csatornák                 | 1 (mono)              |
| Maximális hossz/hang | 2 óra               |
| Minták formátuma            | PCM, 16 bites           |
| Archív formátum           | .zip                  |
| Archiválás maximális mérete     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> A képzés és a tesztelési célú adatfeldolgozás során a. zip fájl mérete nem haladhatja meg a 2 GB-ot. Ha további adatokra van szüksége a betanításhoz, Ossza szét azt több. zip-fájlba, és töltse fel őket külön. Később is dönthet úgy, hogy *több* adatkészletből is betanít. Azonban csak *egyetlen* adatkészletből lehet tesztelni.

A <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">Sox </a> használatával ellenőrizze a hangtulajdonságokat, vagy alakítsa át a meglévő hangokat a megfelelő formátumokba. Az alábbiakban néhány példát láthat arra, hogyan végezheti el ezeket a tevékenységeket a SoX parancssorban:

| Tevékenység | Description | SoX-parancs |
|----------|-------------|-------------|
| Hangformátum keresése | Ezzel a paranccsal ellenőrizhető<br>a hangfájl formátuma. | `sox --i <filename>` |
| Hangformátum konvertálása | A parancs használata a konvertáláshoz<br>a hangfájl egyetlen csatornára, 16 bites, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Hang + emberi-feliratú átirati adatok tesztelési/betanítási célokra

Ha a Microsoft beszéd-szöveg pontosságát szeretné mérni a hangfájlok feldolgozásakor, az összehasonlításhoz meg kell adnia az emberi címkével ellátott átírásokat (Word-by-Word). Míg az emberi címkével ellátott átírás gyakran időigényes, a pontosság kiértékelése és a modell betanítása szükséges a használati esetekhez. Ne feledje, hogy az elismerés fejlesztése csak a megadott adatszolgáltatások esetében lesz megfelelő. Ezért fontos, hogy csak a kiváló minőségű átiratok legyenek feltöltve.

A hangfájlok a rögzítés elején és végén is lehetnek csendben. Ha lehetséges, az egyes mintaadatok előtt és után legalább egy fél másodperces csendet adjon meg. Míg az alacsony rögzítésű vagy zavaró háttérzajú hang nem hasznos, nem árt az egyéni modellnek. A hangminták összegyűjtése előtt mindig vegye fontolóra a mikrofonok és a jelfeldolgozási hardverek frissítését.

| Tulajdonság                 | Érték                               |
|--------------------------|-------------------------------------|
| Fájlformátum              | RIFF (WAV)                          |
| Mintavételezési arány              | 8 000 Hz vagy 16 000 Hz               |
| Csatornák                 | 1 (mono)                            |
| Maximális hossz/hang | 2 óra (tesztelés)/60 s (képzés) |
| Minták formátuma            | PCM, 16 bites                         |
| Archív formátum           | .zip                                |
| Maximális zip-méret         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> A képzés és a tesztelési célú adatfeldolgozás során a. zip fájl mérete nem haladhatja meg a 2 GB-ot. Csak *egyetlen* adatkészletből lehet tesztelni, ügyeljen arra, hogy a fájl a megfelelő fájlméreten belül maradjon. Emellett az egyes betanítási fájlok nem haladhatják meg a 60 másodpercet, ellenkező esetben hiba történik.

A Word törlési vagy helyettesítési problémák megoldásához jelentős mennyiségű adattal kell foglalkoznia az elismerés javítása érdekében. Általánosságban azt javasoljuk, hogy a Word-by-Word típusú átírásokat 1 – 20 órányi hanganyagként adja meg. Azonban akár 30 percet is igénybe vehet, így javíthatja az eredmények felismerését. Az összes WAV fájl átiratát egyetlen, egyszerű szöveges fájlnak kell tartalmaznia. Az átiratfájl minden sorának egy hangfájl nevét és az annak megfelelő átiratot kell tartalmaznia. A fájlnevet és az átiratot tabulátorral (\t) kell elválasztani.

Például:

<!-- The following example contains tabs. Don't accidentally convert these into spaces. -->

```input
speech01.wav    speech recognition is awesome
speech02.wav    the quick brown fox jumped all over the place
speech03.wav    the lazy dog was not amused
```

> [!IMPORTANT]
> Az átiratnak UTF-8 BOM (bájtsorrendjelzés) kódolásúnak kell lennie.

Az átiratokat a rendszer normalizálja, hogy fel tudja őket dolgozni. Van azonban néhány fontos normalizálás, amelyeket el kell végezni, mielőtt feltölti az adatsort a Speech studióba. Az átírások előkészítésekor használandó megfelelő nyelvet lásd: [Human-címkével ellátott átirat létrehozása](how-to-custom-speech-human-labeled-transcriptions.md)

A hangfájlok és a megfelelő átírások összegyűjtése után egyetlen. zip-fájlként csomagolja őket, mielőtt feltölti őket a <a href="https://speech.microsoft.com/customspeech" target="_blank">Speech studióba </a>. Az alábbi példa egy három hangfájllal és egy emberi címkével ellátott átírási fájllal rendelkező adathalmazt mutat be:

> [!div class="mx-imgBorder"]
> ![Hang kiválasztása a Speech Portalon](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

Lásd: az [Azure-fiók beállítása](custom-speech-overview.md#set-up-your-azure-account) az ajánlott régiók listájához a Speech Service-előfizetésekhez. A beszédfelismerési előfizetések ezen régiók egyikében való beállítása csökkenti a modell betanításához szükséges időt. Ezekben a régiókban a képzés napi 10 órányi hangfeldolgozást végez, a többi régióban pedig napi 1 órát. Ha a modell betanítása egy héten belül nem hajtható végre, a modell sikertelenként lesz megjelölve.

Nem minden alapmodell támogatja a hangadatokkal való képzést. Ha az alapmodell nem támogatja azt, akkor a szolgáltatás figyelmen kívül hagyja a hangot, és csak az átiratok szövegét fogja betanítani. Ebben az esetben a képzés a kapcsolódó szöveggel megegyező módon fog megjelenni. A hangadatokkal való képzést támogató alapmodellek listáját a [nyelvi támogatásban](language-support.md#speech-to-text) találhatja meg.

## <a name="related-text-data-for-training"></a>Kapcsolódó szöveges adat a betanításhoz

Az egyedi terméknév vagy szolgáltatások esetében a betanításhoz kapcsolódó szöveges adatnak kell szerepelnie. A kapcsolódó szövegek segítenek biztosítani a helyes felismerést. A következő két típusú kapcsolódó szöveges adat adható meg az elismerés javítása érdekében:

| Adattípus | Az adatfelismerés javítása |
|-----------|------------------------------------|
| Mondatok (hosszúságú kimondott szöveg) | A termékek nevének felismerése, illetve az iparágra jellemző Szószedet használata a mondatok kontextusában. |
| Kiejtés | A nem definiált kiejtésekkel kapcsolatos gyakori kifejezések, mozaikszavak és egyéb szavak kiejtésének javítása. |

A mondatok egyetlen szövegfájlként vagy több szövegfájlként is megadhatók. A pontosság javítása érdekében a várt hosszúságú kimondott szöveg közelebbi szöveges adatok használatával. A kiejtéseket egyetlen szövegfájlként kell megadni. Minden egyes zip-fájlként becsomagolható, és a <a href="https://speech.microsoft.com/customspeech" target="_blank">Speech studióba </a>tölthető fel.

A kapcsolódó szöveggel végzett képzések általában néhány percen belül befejeződik.

### <a name="guidelines-to-create-a-sentences-file"></a>Irányelvek a mondatok létrehozásához

Egyéni modell mondatok használatával történő létrehozásához meg kell adnia a minta hosszúságú kimondott szöveg listáját. A hosszúságú kimondott szöveg _nem_ kell teljesnek vagy programozottan helyesnek lenniük, de pontosan tükröznie kell az éles környezetben várhatóan megjelenő adatokat. Ha azt szeretné, hogy bizonyos kifejezések nagyobb súlyt adjanak, adjon hozzá több mondatot is, amelyek tartalmazzák ezeket az adott feltételeket.

Általános útmutatásként a modellhez való alkalmazkodás a leghatékonyabb, ha a betanítási szöveg a lehető leghamarabb az éles környezetben várt valós szöveghez van lezárva. A fejlesztésre célzott, tartományra jellemző zsargont és kifejezéseket a betanítási szövegbe kell foglalni. Ha lehetséges, próbáljon meg egy mondatot vagy kulcsszót külön sorban vezérelni. Az Ön számára fontos kulcsszavakat és kifejezéseket (például a terméknévokat) néhány alkalommal másolhatja. Ne feledje azonban, hogy ne másoljon túl sokat – ez befolyásolhatja a teljes felismerési arányt.

Ezzel a táblázattal ellenőrizheti, hogy a hosszúságú kimondott szöveg kapcsolódó adatfájlja helyesen van-e formázva:

| Tulajdonság | Érték |
|----------|-------|
| Szövegkódolás | UTF-8 BOM |
| Kimondott szövegek száma soronként | 1 |
| Maximális fájlméret | 200 MB |

Emellett a következő korlátozásokat is érdemes figyelembe vennie:

* Ne ismételje meg az ismétlődő karaktereket, szavakat vagy szavakat három alkalommal. Tegyük fel például, hogy az "AAAA", az "igen igen igen igen", vagy "Ez az, ami ennyire igaz". A beszédfelismerési szolgáltatás túl sok ismétlődési vonalat tartalmazhat.
* Ne használjon a fentiekben speciális karaktereket vagy UTF-8 karaktereket `U+00A1` .
* Az URI-k el lesznek utasítva.

### <a name="guidelines-to-create-a-pronunciation-file"></a>A kiejtési fájl létrehozásához szükséges irányelvek

Ha nem általános feltételek vannak a felhasználók által tapasztalt vagy használt általános kiejtés nélkül, megadhat egy egyéni kiejtési fájlt, amely javítja az elismerést.

> [!IMPORTANT]
> Nem ajánlott egyéni kiejtési fájlokat használni a gyakori szavak kiejtésének módosításához.

Ide tartoznak a kimondott kifejezésekre vonatkozó példák, valamint a következő egyéni kiejtések:

| Felismert/megjelenített űrlap | Szóbeli űrlap |
|--------------|--------------------------|
| 3CPO | három c p o |
| CNTK | c n t k |
| IEEE | i Triple e |

A kimondott űrlap a fonetikus sorszám. Betűből, szóból, szótagból vagy mindhárom kombinációból állhat.

A testreszabott kiejtés angol ( `en-US` ) és német () nyelven érhető el `de-DE` . Ez a táblázat a támogatott karaktereket mutatja nyelv szerint:

| Nyelv | Területi beállítás | Karaktereket |
|----------|--------|------------|
| Angol | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Német | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

A következő táblázat segítségével biztosíthatja, hogy a kiejtésekhez kapcsolódó adatfájl megfelelően legyen formázva. A kiejtési fájlok kicsik, és csak néhány kilobájt méretűek lehetnek.

| Tulajdonság | Érték |
|----------|-------|
| Szövegkódolás | UTF-8 AJ (ANSI is támogatott angol nyelven) |
| kiejtések száma soronként | 1 |
| Maximális fájlméret | 1 MB (1 KB ingyenes szinten) |

## <a name="next-steps"></a>Következő lépések

* [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)
* [Az adatai kiértékelése](how-to-custom-speech-evaluate-data.md)
* [A modell betanítása](how-to-custom-speech-train-model.md)
* [A modell üzembe helyezése](./how-to-custom-speech-train-model.md)