---
title: A modell javítása – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan javíthatja a modell minőségét a Custom Vision szolgáltatásban, hogy az adatmennyiség, a minőség és a különböző adatmennyiség milyen minőségű lehet.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 328bfe57c675d49aa951388e2808fcecfe8da8b5
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100096531"
---
# <a name="how-to-improve-your-custom-vision-model"></a>A Custom Vision-modell javítása

Ebből az útmutatóból megtudhatja, hogyan javíthatja a Custom Vision Service modell minőségét. Az [osztályozó](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) vagy az [objektum-Kiderítő](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector) minősége függ az Ön által megadott címkézett adatok mennyiségétől, minőségétől és számától, valamint a teljes adatkészlet arányos mennyiségével. A megfelelő modellnek van egy kiegyensúlyozott betanítási adatkészlete, amely arra szolgál, hogy mi lesz elküldve. Egy ilyen modell kiépítésének folyamata iterációs jellegű. gyakori, hogy eltarthat néhány kört a képzéstől a várt eredmények eléréséhez.

A következő általános minta a pontosabb modellek betanításához nyújt segítséget:

1. Első köros képzés
1. További lemezképek és egyenlegek hozzáadása; újratanítása
1. Különböző háttérrel, világítással, objektum méretével, kamera látószögével és stílusával adhat hozzá képeket. újratanítása
1. Új rendszerkép (ek) használata az előrejelzés teszteléséhez
1. Meglévő betanítási adatmennyiség módosítása az előrejelzési eredmények alapján

## <a name="prevent-overfitting"></a>Túlilleszkedés megakadályozása

Előfordulhat, hogy egy modell megtanulja, hogy az előrejelzéseket a rendszerképek közös jellemzői alapján hozza meg. Ha például az alma és a Citrus számára hoz létre egy osztályozó, és az alma a kezében és a Citrus on fehér lemezeken is használta a képeket, az osztályozó az alma és a Citrus helyett aránytalanul nagy jelentőséget biztosíthat a kezek és a lemezek számára.

![Nem várt besorolású rendszerkép](./media/getting-started-improving-your-classifier/unexpected.png)

A probléma megoldásához különböző nézőpontokkal, háttérrel, objektum mérettel, csoportokkal és egyéb változatokkal rendelkező képeket adjon meg. Az alábbi fejezetek ezen fogalmakra épülnek.

## <a name="data-quantity"></a>Adatmennyiség

A betanítási lemezképek száma az adatkészlet legfontosabb tényezője. Javasolt kiindulási pontként legalább 50 lemezképet használni címkén. Kevesebb képpel nagyobb a terhelés, és míg a teljesítménybeli számok jó minőségűek lehetnek, a modell valós adatokkal járhat. 

## <a name="data-balance"></a>Adategyensúly

Fontos figyelembe venni a betanítási adataihoz tartozó relatív mennyiségeket is. Például a 500 lemezképek használata egy címkéhez és a 50-lemezképek egy másik címkéhez egy kiegyensúlyozatlan betanítási adatkészletet tesz lehetővé. Ez azt eredményezi, hogy a modell pontosabban fog megjelenni egy címke előrejelzésében. Valószínűleg jobb eredményeket tapasztal, ha legalább 1:2 arányban tartja a címkét a legkevesebb képpel és a legtöbb képpel rendelkező címkével. Ha például a legtöbb lemezképtel rendelkező címke 500-es képet tartalmaz, a legkevésbé képképekkel rendelkező címkének legalább 250 lemezképet kell tartalmaznia a betanításhoz.

## <a name="data-variety"></a>Adatválaszték

Ügyeljen arra, hogy a normál használat során az osztályozó által beküldött képeket is használják. Ellenkező esetben a modell megtudhatja, hogy az előrejelzések a képek által közösen használt sajátosságok alapján legyenek elérhetők. Ha például az alma és a Citrus számára hoz létre egy osztályozó, és az alma a kezében és a Citrus on fehér lemezeken is használta a képeket, az osztályozó az alma és a Citrus helyett aránytalanul nagy jelentőséget biztosíthat a kezek és a lemezek számára.

![Nem várt besorolású rendszerkép](./media/getting-started-improving-your-classifier/unexpected.png)

A probléma megoldásához számos lemezképet kell tartalmaznia, hogy a modell általánosítható legyen. Az alábbi módokon teheti meg, hogy a képzési lehetőségek sokrétűek legyenek:

* __Háttér:__ A különböző hátterek előtt adja meg az objektum képeit. A természetes környezetekben található fényképek jobbak, mint a semleges hátterű fényképek, mivel további információkat biztosítanak az osztályozó számára.

    ![Háttérbeli minták képe](./media/getting-started-improving-your-classifier/background.png)

* __Megvilágítás:__ A képeket különböző világítással (a Flash, a magas expozícióval stb.), különösen akkor, ha az előrejelzéshez használt képek eltérő megvilágítással rendelkeznek. Az is hasznos, ha a képeket különböző telítettséggel, árnyalattal és fényerővel szeretné használni.

    ![Világító minták képe](./media/getting-started-improving-your-classifier/lighting.png)

* __Objektum mérete:__ Adja meg azokat a lemezképeket, amelyekben az objektumok mérete és száma (például a banán fürtökből származó képek és egy egyetlen banánból álló Vértes) változó. A különböző méretezés segíti az osztályozó általánosítás jobb kiválasztását.

    ![Méret minták képe](./media/getting-started-improving-your-classifier/size.png)

* __Kamera szöge:__ Különböző kamera-szögek által készített lemezképek megadása. Ha az összes fényképet rögzített kamerákkal (például a térfigyelő kamerákkal) kell elvégezni, ügyeljen arra, hogy egy másik címkét rendeljen minden rendszeresen előforduló objektumhoz, hogy elkerülje &mdash; a nem kapcsolódó objektumok (például a lámpaoszlopok) a fő szolgáltatásként való értelmezését.

    ![Szög minták képe](./media/getting-started-improving-your-classifier/angle.png)

* __Stílus:__ Adja meg ugyanazt az osztály különböző stílusainak képeit (például az azonos gyümölcs különböző fajtáit). Ha azonban a különböző stílusok (például a Mickey Mouse és a valós egér) objektumai vannak, akkor azt javasoljuk, hogy külön osztályokként címkézse őket, hogy jobban képviseljék a különböző funkcióit.

    ![Stílusú minták képe](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images-classifiers-only"></a>Negatív képek (csak osztályozók)

Képosztályozó használata esetén előfordulhat, hogy _negatív mintákat_ kell hozzáadnia, hogy az osztályozó pontosabb legyen. A negatív minták olyan képek, amelyek nem egyeznek a többi címkével. Ha feltölti ezeket a képeket, alkalmazza rájuk a speciális **negatív** címkét.

Az objektum-érzékelők automatikusan kezelik a negatív mintákat, mert a rajzolt határoló mezőkön kívüli bármely képterület negatívnak minősül.

> [!NOTE]
> A Custom Vision Service támogatja a negatív rendszerképek automatikus kezelését. Ha például a szőlő és a banán besorolását készíti elő, és a cipőt az előrejelzéshez beküldi, akkor az osztályozó az adott képet a szőlő és a banán esetében is a 0%-os értékkel közelíti meg.
> 
> Ha azonban a negatív rendszerképek csak a betanítás során használt rendszerképek variációi, akkor valószínű, hogy a modell a nagy hasonlóságok miatt a negatív képeket címkézett osztályként fogja osztályozni. Ha például egy narancssárga és a grapefruit besorolással rendelkezik, és egy clementine-képet is megadsz, akkor a clementine narancssárgaként jelenhet meg, mivel a clementine számos funkciója hasonlít a narancssárga értékekre. Ha a negatív képek ilyen jellegűek, javasoljuk, hogy hozzon létre egy vagy több további címkét (például a **többiet**), és címkézze a negatív képeket ezzel a címkével a képzés során, hogy a modell jobban megkülönböztetse ezeket az osztályokat.

## <a name="consider-occlusion-and-truncation-object-detectors-only"></a>Az elzáródás és a csonkítás megvizsgálása (csak az objektum-felismerők esetében)

Ha azt szeretné, hogy az objektum detektora felderítse a csonkolt objektumokat (az objektum részben kikerül a lemezképből) vagy a bezárt objektumokat (az objektumot részben blokkolja a rendszerkép egy másik objektuma), akkor az ilyen eseteket lefedő betanítási lemezképeket is meg kell adnia.

> [!NOTE]
> A más objektumok által letiltott objektumok problémája nem tévesztendő össze az **átfedési küszöbértékkel**, a minősítési modell teljesítményének paraméterével. A [Custom Vision webhely](https://customvision.ai) **átfedéses küszöbértékének** csúszkája azt mutatja, hogy Mennyibe kerül egy előre jelzett határolókeret átfedése a True határolókeret használatával, hogy helyesnek lehessen tekinteni.

## <a name="use-prediction-images-for-further-training"></a>Előrejelzési lemezképek használata a további képzéshez

A modell használatakor vagy tesztelésekor a lemezképeket az előrejelzési végpontba küldi el, a Custom Vision szolgáltatás tárolja ezeket a lemezképeket. Ezután a modell fejlesztéséhez használhatja őket.

1. Ha meg szeretné tekinteni a modellnek küldött képeket, nyissa meg a [Custom Vision weblapot](https://customvision.ai), lépjen a projektbe, és válassza a __jóslatok__ fület. Az alapértelmezett nézet az aktuális iteráció képeit jeleníti meg. A korábbi iterációk során elküldött képek megtekintéséhez használhatja az __iteráció__ legördülő menüt.

    ![képernyőkép a jóslatok lapról a képek nézetben](./media/getting-started-improving-your-classifier/predictions.png)

2. Vigye az egérmutatót egy képre a modell által előre jelzett címkék megtekintéséhez. A képek rendezése úgy történik, hogy azok, amelyek a modell legtöbb tökéletesítését lehetővé teszik, megjelennek a tetején. Ha másik rendezési módszert szeretne használni, válasszon ki egy kijelölést a __Rendezés__ szakaszban. 

    Ha képet szeretne adni a meglévő betanítási adatokhoz, válassza ki a képet, állítsa be a megfelelő címkét, majd kattintson a __Mentés és bezárás__ gombra. A rendszer eltávolítja a lemezképet az __előrejelzések__ közül, és hozzáadja a betanítási lemezképek készletéhez. A __betanítási képek__ lapon tekintheti meg.

    ![A címkézési lap képe](./media/getting-started-improving-your-classifier/tag.png)

3. Ezután a __vonat__ gomb használatával végezze el a modell újratanítását.

## <a name="visually-inspect-predictions"></a>Előrejelzések vizuális vizsgálata

A képelőrejelzések vizsgálatához lépjen a __betanítási képek__ lapra, válassza ki az előző tanítási iterációt az **iteráció** legördülő menüben, és ellenőrizze a **címkék** szakaszban található címkéket. A nézetnek ekkor egy piros téglalapot kell megjelenítenie minden olyan rendszerkép körül, amelyhez a modell nem tudta megfelelően megjósolni a megadott címkét.

![Az iteráció előzményeinek képe](./media/getting-started-improving-your-classifier/iteration.png)

Előfordulhat, hogy a vizuális vizsgálat olyan mintákat azonosít, amelyeket később további betanítási adatok hozzáadásával vagy a meglévő betanítási adatok módosításával lehet kijavítani. Például az Apples és a Limes-osztályozók nem megfelelően címkézik az összes zöld almát. Ezt a problémát a zöld alma címkézett képeit tartalmazó betanítási adatok hozzáadásával és biztosításával orvosolhatja.

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban számos technikát tanult meg, hogy az egyéni képbesorolási modell vagy az objektum-érzékelő modellje pontosabb legyen. Következő lépésként megtudhatja, hogyan tesztelheti a képeket programozott módon az előrejelzési API-ba való küldéssel.

> [!div class="nextstepaction"]
> [Előrejelzési API használata](use-prediction-api.md)
