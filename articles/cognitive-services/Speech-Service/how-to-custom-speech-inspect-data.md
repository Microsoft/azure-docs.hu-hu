---
title: Az Custom Speech-Speech szolgáltatás adatminőségének vizsgálata
titleSuffix: Azure Cognitive Services
description: A Custom Speech olyan eszközöket biztosít, amelyekkel vizuálisan ellenőrizheti a modell felismerési minőségét a hangadatoknak a megfelelő felismerési eredménnyel való összehasonlításával. Lejátszhatja a feltöltött hangot, és megállapíthatja, hogy a megadott felismerési eredmény helyes-e.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: erhopf
ms.openlocfilehash: 9ce0d3a06846cbc3aa37ab836564150e6f2c34ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100362811"
---
# <a name="inspect-custom-speech-data"></a>Custom Speech-adatok vizsgálata

> [!NOTE]
> Ez az oldal azt feltételezi, hogy elolvasta a [Custom Speechi tesztelési adatokat](./how-to-custom-speech-test-and-train.md) , és feltöltött egy adatkészletet a vizsgálathoz.

A Custom Speech olyan eszközöket biztosít, amelyekkel vizuálisan ellenőrizheti a modell felismerési minőségét a hangadatoknak a megfelelő felismerési eredménnyel való összehasonlításával. A [Speech studióból](https://speech.microsoft.com/customspeech)lejátszhatja a feltöltött hangot, és megállapíthatja, hogy a megadott felismerési eredmény helyes-e. Ez az eszköz segít megvizsgálni a Microsoft alapszintű beszéd-szöveg modelljét, megvizsgálja a betanított egyéni modellt, vagy összehasonlítja a két modellből való átírást.

Ebből a dokumentumból megtudhatja, hogyan vizsgálhatja meg a Microsoft alapvető beszédfelismerési modelljét és/vagy a betanított egyéni modelleket. Azt is megtudhatja, hogyan hozhat létre és pontosíthat címkézett hang-adatkészleteket az online transzkripció Editor használatával.

## <a name="create-a-test"></a>Teszt létrehozása

Teszt létrehozásához kövesse az alábbi utasításokat:

1. Jelentkezzen be a [Speech studióba](https://speech.microsoft.com/customspeech).
2. Navigáljon a **beszéd – szöveg > Custom Speech > [a projekt neve] > tesztelése** elemre.
3. Kattintson a **teszt hozzáadása** gombra.
4. Válassza a **minőség vizsgálata (csak hangalapú adatok)** lehetőséget. Adja meg a teszt nevét, leírását, és válassza ki a hangkészletet.
5. Válasszon legfeljebb két modellt, amelyeket szeretne tesztelni.
6. Kattintson a **Létrehozás** lehetőségre.

Egy teszt sikeres létrehozása után megtekintheti, hogy egy modell hogyan írja át a megadott hangkészletet, vagy összehasonlítja a két modell eredményeit egymás mellett.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Párhuzamos modell-összehasonlítások

Ha a teszt állapota _sikeres_, kattintson a teszt elem nevére a teszt részleteinek megtekintéséhez. Ez a részletes lap felsorolja az adatkészlet összes hosszúságú kimondott szöveg, és megjeleníti az összehasonlított két modell felismerési eredményeit.

Az egymás melletti összehasonlítások megvizsgálása érdekében különböző típusú hibákat válthat ki, beleértve a beszúrást, a törlést és a helyettesítést is. Ha az egyes oszlopokban figyeli a hangot, és összehasonlítja a felismerés eredményét (megjelenítve az emberi címkével ellátott átírást és a két beszéd – szöveg modell eredményét), eldöntheti, hogy melyik modell megfelel az igényeinek, és hogy hol szükségesek a tökéletesítések.

A párhuzamos modell tesztelése hasznos annak ellenőrzéséhez, hogy melyik beszédfelismerési modell a legmegfelelőbb az alkalmazásokhoz. Az átmásolt hangra vonatkozó objektív pontosság érdekében kövesse a [pontosság kiértékelése](how-to-custom-speech-evaluate-data.md)című részben található utasításokat.

## <a name="online-transcription-editor"></a>Online átírás szerkesztő

Az online átírás-szerkesztővel egyszerűen dolgozhat a Custom Speech hangátiratokkal. A szerkesztő fő felhasználási esetei a következők: 

* Csak hangadatokkal rendelkezik, de a modell betanítása során az előzményekből származó pontos hang-és emberi címkézett adatkészleteket is létre kívánja készíteni.
* Már rendelkezik hang + emberi címkével rendelkező adatkészletekkel, de hibák vagy hibák történnek az átírásban. A szerkesztővel gyorsan módosíthatja az átírásokat a legjobb betanítási pontosság eléréséhez.

Az átírási szerkesztő használatának egyetlen követelménye az, hogy a hangadatokat fel kell tölteni (vagy csak a hang vagy a hang + átirat).

### <a name="import-datasets-to-editor"></a>Adatkészletek importálása a szerkesztőbe

Ha az adatait a szerkesztőbe szeretné importálni, először navigáljon **Custom Speech > [a projekt] >-szerkesztőbe**.

![Szerkesztői lap](media/custom-speech/custom-speech-editor-detail.png)

Ezután kövesse az alábbi lépéseket az adatimportáláshoz.

1. Kattintson **az adatimportálás** elemre.
1. Hozzon létre egy új adatkészlet (eke) t, és adjon neki Leírást
1. Válassza az adatkészletek lehetőséget. Több kijelölés is támogatott, és a csak hangadatokat, valamint a hang-és emberi címkézett adatok közül választhat.
1. A csak hang típusú adatok esetében az alapértelmezett modellek használatával automatikusan előállíthatja a gépi átírást a szerkesztőbe való importálás után.
1. Kattintson az **Importálás** gombra

Az adat sikeres importálása után rákattinthat az adatkészletekre, és megkezdheti a szerkesztést.

> [!TIP]
> Az adatkészleteket közvetlenül is importálhatja a szerkesztőbe az adatkészletek elem kiválasztásával, majd az **Exportálás a szerkesztőbe** lehetőségre kattintva

### <a name="edit-transcription-by-listening-to-audio"></a>Átirat szerkesztése hang figyelésével

Miután az adatok feltöltése sikeres volt, kattintson az egyes elemek nevére az adatok részleteinek megtekintéséhez. Az egyes fájlok közötti váltáshoz használhatja az **előző** és a **tovább** lehetőséget is.

A Részletek lap felsorolja az egyes hangfájlok összes szegmensét, és rákattinthat a kívánt kifejezésre. Minden Kimondás esetén visszaállíthatja a hangot, és megvizsgálhatja az átiratokat, és szerkesztheti az átírásokat, ha bármilyen beszúrási, törlési vagy helyettesítési hibát talál. A hibákkal kapcsolatos további részletekért tekintse meg az [adatok kiértékelését](how-to-custom-speech-evaluate-data.md) ismertető témakört.

![Szerkesztő lap](media/custom-speech/custom-speech-editor.png)

Miután végzett a szerkesztéssel, kattintson a **Save (Mentés** ) gombra.

### <a name="export-datasets-from-the-editor"></a>Adatkészletek exportálása a szerkesztőből

Az adatkészletek az **adatok** lapra való exportálásához navigáljon az adatok részletei lapra, és kattintson az **Exportálás** gombra az összes fájl új adatkészletként való exportálásához. A fájlokat az utolsó módosítás időpontja, a hang időtartama stb. alapján is szűrheti, hogy részben kiválassza a kívánt fájlokat. 

![Adatok exportálása](media/custom-speech/custom-speech-editor-export.png)

Az adatba exportált fájlok teljesen új adatkészletként lesznek felhasználva, és nem érintik a meglévő adat/képzés/tesztelési entitásokat.

## <a name="next-steps"></a>Következő lépések

- [Az adatai kiértékelése](how-to-custom-speech-evaluate-data.md)
- [A modell betanítása](how-to-custom-speech-train-model.md)
- [Modell továbbfejlesztése](./how-to-custom-speech-evaluate-data.md)
- [A modell üzembe helyezése](./how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>További források

- [Custom Speech tesztelési célú adatfeldolgozása](./how-to-custom-speech-test-and-train.md)