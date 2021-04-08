---
title: 'Oktatóanyag: űrlap-feldolgozó alkalmazás létrehozása AI Builder-Form felismerővel'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban a AI Builder használatával létrehozhatja és betaníthatja az űrlap-feldolgozó alkalmazást.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: lajanuar
ms.openlocfilehash: 30c44ac60783132344cc018135d2c248f91f46bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467119"
---
# <a name="tutorial-create-a-form-processing-app-with-ai-builder"></a>Oktatóanyag: űrlap-feldolgozó alkalmazás létrehozása AI-szerkesztővel

Az [AI Builder](/ai-builder/overview) egy olyan energiagazdálkodási képesség, amely lehetővé teszi a folyamatok automatizálását és előrejelzését az üzleti teljesítmény javítása érdekében. A mesterséges intelligenciát használó űrlapok feldolgozásával olyan AI-modelleket hozhat létre, amelyek azonosítják és kinyerik a kulcs-érték párokat és a táblák adatait az űrlap dokumentumaiból.

> [!NOTE]
> Ez a projekt [Microsoft Learn modulként](/learn/modules/get-started-with-form-processing/)is elérhető.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Űrlap-feldolgozási AI-modell létrehozása
> * A modell betanítása
> * Az Azure Power Appsben vagy a Power automatizálva használható modell közzététele

## <a name="prerequisites"></a>Előfeltételek

* Legalább öt, azonos típusú, a képzéshez/teszteléshez használt űrlapból álló készlet. A betanítási adatkészletek összeállításával kapcsolatos tippekért és lehetőségekkel kapcsolatban lásd: [képzési adatkészlet létrehozása](./build-training-data-set.md) . Ebben a rövid útmutatóban használhatja a [minta adatkészletének](https://go.microsoft.com/fwlink/?linkid=2128080) **vonattal** mappában található fájlokat.
* Power apps vagy Power automatizáló licenc – tekintse meg a [licencelési útmutatót](https://go.microsoft.com/fwlink/?linkid=2085130). A licencnek tartalmaznia kell [Common Data Service](https://powerplatform.microsoft.com/common-data-service/).
* AI Builder [-bővítmény vagy próbaverzió](https://go.microsoft.com/fwlink/?LinkId=2113956&clcid=0x409).


## <a name="create-a-form-processing-project"></a>Űrlap-feldolgozási projekt létrehozása

1. Lépjen a [Power apps](https://make.powerapps.com/) vagy a [Power automatizálás](https://flow.microsoft.com/signin)szolgáltatáshoz, és jelentkezzen be a szervezeti fiókjával.
1. A bal oldali panelen válassza az **AI Builder**  >  **Build** elemet.
1. Válassza ki az **űrlap feldolgozására** szolgáló kártyát.
1. Adja meg a modell nevét.
1. Válassza a **Létrehozás** lehetőséget.

## <a name="upload-and-analyze-documents"></a>Dokumentumok feltöltése és elemzése

A **dokumentumok hozzáadása** lapon minta dokumentumokat kell megadnia ahhoz, hogy betanítsa a modellt azon típusú űrlapra, amelyről adatokat szeretne kinyerni. A dokumentumok feltöltése után az AI Build elemzi őket, hogy ellenőrizzék, hogy elegendő-e a modell betanításához.

> [!NOTE]
> Az AI Builder jelenleg nem támogatja az alábbi bemeneti adatokat az űrlapfeldolgozáshoz:
>
> - Összetett táblák (beágyazott táblák, egyesített fejlécek vagy cellák és hasonlók)
> - Jelölőnégyzetek vagy választógombok
> - 50 oldalnál hosszabb PDF-dokumentumok
> - Kitölthető PDF-fájlok
>
> A bemeneti dokumentumokra vonatkozó követelményekkel kapcsolatos további információkért lásd a [bemeneti követelmények](./overview.md#input-requirements)című témakört.

### <a name="upload-your-documents"></a>Dokumentumok feltöltése

1. Válassza a **Dokumentumok hozzáadása** lehetőséget, jelöljön ki legalább öt dokumentumot, majd kattintson a **Feltöltés** gombra.
1. A feltöltés befejezése után válassza a **Bezárás** lehetőséget.
1. Ezután válassza az **elemzés** lehetőséget.

> [!NOTE] 
> Miután felöltötte ezeket a dokumentumokat, eltávolíthat közülük néhányat, vagy feltölthet továbbiakat.

> [!div class="mx-imgBorder"]
> ![Képernyőkép a dokumentumok hozzáadása gomb kiemeléséhez.](./media/tutorial-ai-builder/add-documents-page.png)

### <a name="analyze-your-documents"></a>Dokumentumok elemzése

Az elemzési lépés során a mesterséges intelligencia-szerkesztő megvizsgálja a feltöltött dokumentumokat, és észleli a mezőket és a táblákat. A művelet végrehajtásához szükséges idő a megadott dokumentumok számától függ, de a legtöbb esetben csak néhány percet vesz igénybe.

Az elemzés befejezését követően válassza ki a miniatűrt a mező kiválasztási élményének megnyitásához.

> [!IMPORTANT]
> Ha az elemzés nem sikerült, akkor valószínű, hogy az AI Build nem tudta felderíteni a dokumentumok strukturált szövegét. Ellenőrizze, hogy a frissített dokumentumok követik-e a [bemeneti követelményeket](./overview.md#input-requirements).

## <a name="select-your-form-fields"></a>Űrlapmezők kiválasztása

A mező kiválasztása lapon válassza ki a kívánt mezőket:

1. Egy mező kiválasztásához kattintson egy olyan négyszögre, amely az észlelt mezőt jelzi a dokumentumban, vagy válasszon több mezőt a kattintással és húzással. A jobb oldali ablaktáblán a mezőket is kiválaszthatja.
1. Kattintson a kijelölt mező nevére, ha átnevezni szeretné az igényeinek megfelelően, vagy normalizálja a kinyert címkéket.

    Ha egy észlelt mezőre kattint, az alábbi információk jelennek meg:

    - **Mezőnév**: az észlelt mező feliratának neve.
    - **Mező értéke**: az észlelt mező értéke.

> [!div class="mx-imgBorder"]
> ![címkézés lap](./media/tutorial-ai-builder/select-fields-page.png)

### <a name="label-undetected-fields"></a>Felderítetlen mezők felirata

Ha nem a modell automatikusan észlelte a címkézni kívánt mezőt, rajzoljon egy téglalapot az érték körül, és írja be a felirat nevét a megjelenő párbeszédpanelen.

## <a name="train-your-model"></a>A modell betanítása

1. A kiválasztott űrlapmezők ellenőrzéséhez válassza a **Tovább** lehetőséget. Ha minden rendben, válassza a **Betanítás** lehetőséget a modell betanításához.

    > [!div class="mx-imgBorder"]
    > ![betanítás lap](./media/tutorial-ai-builder/summary-train-page.png)
1. A betanítás után válassza a **Ugrás a Részletek lapra** lehetőséget a **betanítás végét jelző** képernyőn.
## <a name="quick-test-your-model"></a>A modell gyors tesztelése

A Részletek lapon tesztelheti a modellt a közzététel vagy használat előtt:

1. A Részletek lapon válassza a **Gyorsteszt** lehetőséget.
2. Egy dokumentumot áthúzhat, vagy kiválaszthatja a **feltöltés az eszközről** lehetőséget a tesztoldal feltöltéséhez. A gyorsteszt néhány másodperc után megjeleníti az eredményeket.
3. Kiválaszthatja az **Indítás** lehetőséget egy másik teszt futtatásához, vagy ha elkészült, akkor a **Bezárás** gombra.

### <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

Ha bizonyos mezőkhöz rossz eredményeket vagy alacsony megbízhatósági pontszámokat szeretne kapni, próbálkozzon az alábbi tippekkel:

- Az egyes mezőkben különböző értékekkel rendelkező űrlapok használatával történő újratanítás.
- Újratanítás a betanítási dokumentumok nagyobb készletének használatával. Minél több dokumentum van felcímkézve, annál több AI-szerkesztő fogja megtanulni, hogyan lehet jobban felismerni a mezőket.
- A PDF-fájlok optimalizálható úgy, hogy csak bizonyos lapokat válasszon ki a betanításhoz. **A nyomtatás**  >  **PDF-fájlként** lehetőséggel kiválaszthatja a dokumentum egyes lapjait.

## <a name="publish-your-model"></a>A modell közzététele

Ha elégedett a modellel, válassza a **Közzététel**  lehetőséget a közzétételhez. A közzététel befejezésekor a rendszer **Közzétett** állapotúként jeleníti meg a modellt, amely innentől kezdve használatba vehető.

> [!div class="mx-imgBorder"]
> ![modell közzététele lap](./media/tutorial-ai-builder/model-page.png)

Miután közzétette az űrlap-feldolgozási modellt, használhatja azt egy [Power apps vászon alkalmazásban](/ai-builder/form-processor-component-in-powerapps) vagy a [Power gyorsbüféban](/ai-builder/form-processing-model-in-flow).

## <a name="next-steps"></a>Következő lépések

Az űrlap-feldolgozási modell használatához kövesse a mesterséges intelligencia-szerkesztő dokumentációját.

* [Az űrlap-processzor összetevő használata a Power Appsben](/ai-builder/form-processor-component-in-powerapps)
* [Űrlap-feldolgozási modell használata a Power Gyorsbüféban](/ai-builder/form-processing-model-in-flow)