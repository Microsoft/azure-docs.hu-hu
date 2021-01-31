---
title: 'Gyors útmutató: osztályozó létrehozása a Custom Vision webhellyel'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre, taníthat és tesztelheti a képbesorolási modellt a Custom Vision webhelyről.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: képek felismerése, képfelismerési alkalmazás, egyéni jövőkép
ms.openlocfilehash: d25b226f5dfff34aa572b40d1e4de142cf8e0b7b
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221244"
---
# <a name="quickstart-build-a-classifier-with-the-custom-vision-website"></a>Gyors útmutató: osztályozó létrehozása a Custom Vision webhellyel

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy rendszerkép-besorolási modellt a Custom Vision webhellyel. A modell létrehozása után tesztelheti az új rendszerképekkel, és végül integrálhatja azt a saját rendszerkép-felismerő alkalmazásba.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Előfeltételek

- Azon lemezképek összessége, amelyekkel betaníthatja az osztályozó. A képek kiválasztására vonatkozó tippek alább találhatók.

## <a name="create-custom-vision-resources"></a>Custom Vision erőforrások létrehozása

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Új projekt létrehozása

A böngészőben nyissa meg a [Custom Vision weblapot](https://customvision.ai) , és válassza a __Bejelentkezés__ lehetőséget. Jelentkezzen be ugyanazzal a fiókkal, amelyet a Azure Portalba való bejelentkezéshez használt.

![A bejelentkezési oldal képe](./media/browser-home.png)


1. Az első projekt létrehozásához válassza az **új projekt** lehetőséget. Ekkor megjelenik az **új projekt létrehozása** párbeszédpanel.

    ![Az új projekt párbeszédpanelen a név, a leírás és a tartományok mezői láthatók.](./media/getting-started-build-a-classifier/new-project.png)

1. Adja meg a projekt nevét és leírását. Ezután válasszon ki egy erőforráscsoportot. Ha a bejelentkezett fiók egy Azure-fiókhoz van társítva, az erőforráscsoport legördülő lista megjeleníti az összes olyan Azure-erőforráscsoportot, amely Custom Vision Service erőforrást tartalmaz. 

   > [!NOTE]
   > Ha nincs elérhető erőforráscsoport, győződjön meg róla, hogy a [customvision.ai](https://customvision.ai) -ba jelentkezett be ugyanazzal a fiókkal, mint amikor a [Azure Portalba](https://portal.azure.com/)való bejelentkezéshez használt. Továbbá erősítse meg, hogy ugyanazt a "könyvtárat" választotta a Custom Vision webhelyén abban a Azure Portal könyvtárban, ahol a Custom Vision erőforrásai találhatók. Mindkét helyen kiválaszthatja a könyvtárat a képernyő jobb felső sarkában található legördülő menü fiók menüjéből. 

1. Válasszon __besorolást__ a __Project Types__ elemnél. Ezután a __besorolási típusok__ területen válassza a **többcímkés** vagy a **többosztályos** lehetőséget a használati esettől függően. A többcímkés besorolás tetszőleges számú címkét alkalmaz egy képre (nulla vagy több), míg a többosztályos besorolás a képeket egyetlen kategóriába rendezi (minden elküldött kép a legvalószínűbb címkére lesz rendezve). Ha szeretné, később módosíthatja a besorolási típust.

1. Ezután válassza ki a rendelkezésre álló tartományok egyikét. Az egyes tartományok a következő táblázatban leírtak szerint optimalizálja az adott típusú képek besorolását. Ha szeretné, később is megváltoztathatja a tartományt.

    |Tartomány|Cél|
    |---|---|
    |__Általános__| A képbesorolási feladatok széles körére optimalizált. Ha a többi tartomány egyike sem megfelelő, vagy nem biztos benne, hogy melyik tartományt szeretné kiválasztani, válassza ki az általános tartományt. |
    |__Élelmiszer__|Az ételek fényképeihez optimalizált, ahogy azt egy étterem menüjében láthatja. Ha egyéni gyümölcsökből vagy zöldségekből származó fényképeket szeretne osztályozni, használja az élelmiszer-tartományt.|
    |__Arcrészek__|Felismerhető tereptárgyak számára optimalizált, természetes és mesterséges is. Ez a tartomány akkor működik a legjobban, ha a tereptárgy jól látható a fényképben. Ez a tartomány akkor is működik, ha a tereptárgyat az előttük lévő személyek kis mértékben akadályozzák.|
    |__Retail__|A vásárlási katalógusban vagy a vásárlási webhelyen található rendszerképekre optimalizált. Ha a ruhák, nadrágok és ingek között nagy pontosságú osztályozást szeretne, használja ezt a tartományt.|
    |__Kompakt tartományok__| A mobileszközök valós idejű besorolásának korlátaira optimalizált. A kompakt tartományok által generált modellek helyileg is futtathatók.|

1. Végül válassza a __projekt létrehozása__ lehetőséget.

## <a name="choose-training-images"></a>Képzési lemezképek kiválasztása

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Ebben a szakaszban fel kell töltenie és manuálisan címkézheti a képeket az osztályozó betanításához. 

1. Képek hozzáadásához válassza a __képek hozzáadása__ lehetőséget, majd válassza a __helyi fájlok tallózása__ lehetőséget. Válassza a __Megnyitás__ lehetőséget a címkézésre való áttéréshez. A címke kiválasztása a feltöltésre kijelölt rendszerképek teljes csoportjára lesz alkalmazva, így az alkalmazott címkék szerint egyszerűbb a képek külön csoportba való feltöltése. Az egyes képek címkéit a feltöltésük után is módosíthatja.

    ![A képek hozzáadása vezérlő megjelenik a bal felső sarokban, és az alsó középen lévő gomb.](./media/getting-started-build-a-classifier/add-images01.png)


1. Címke létrehozásához írja be a szöveget a __saját címkék__ mezőbe, majd nyomja le az ENTER billentyűt. Ha a címke már létezik, akkor megjelenik egy legördülő menüben. Egy többcímkés projektben több címkét is hozzáadhat a képekhez, de többosztályos projektekben csak egyet adhat hozzá. A lemezképek feltöltésének befejezéséhez használja a __[Number] Files (feltöltés) fájlok__ gombot. 

    ![A címke és a feltöltési oldal képe](./media/getting-started-build-a-classifier/add-images03.png)

1. A képek feltöltése után válassza a __kész__ lehetőséget.

    ![A folyamatjelző sáv megjeleníti az összes befejezett feladatot.](./media/getting-started-build-a-classifier/add-images04.png)

Egy másik rendszerkép feltöltéséhez térjen vissza a szakasz elejére, és ismételje meg a lépéseket.

## <a name="train-the-classifier"></a>Az osztályozó betanítása

Az osztályozó betanításához kattintson a **vonat** gombra. Az osztályozó az összes aktuális képet használja egy olyan modell létrehozásához, amely az egyes címkék vizualizációs tulajdonságait azonosítja.

![A weblap fejlécének jobb felső részén található vonat gomb](./media/getting-started-build-a-classifier/train01.png)

A betanítási folyamat csak néhány percet vesz igénybe. Ebben az időszakban a betanítási folyamattal kapcsolatos információk a **teljesítmény** lapon jelennek meg.

![A böngészőablakban a főszakasz betanítási párbeszédablaka](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Osztályozó kiértékelése

A betanítás befejezése után a modell teljesítménye becsült és megjelenik. A Custom Vision Service a betanításhoz beküldött rendszerképeket használja a pontosság és visszahívás kiszámításához, a [k-Foldal Cross Validation](https://en.wikipedia.org/wiki/Cross-validation_(statistics))nevű folyamat használatával. A pontosság és a visszahívás két különböző mérési tényező az osztályozó hatékonyságának méréséhez:

- A **pontosság** a megfelelő azonosított besorolások töredékét jelzi. Ha például a modell 100 lemezképeket azonosított, és 99 közülük valójában kutyák voltak, akkor a pontosság 99% lenne.
- A **visszahívás** azt jelzi, hogy a tényleges besorolások hányada helyesen azonosítható. Ha például valóban 100-es Alma-lemezképek voltak, és a modellben a 80 as Alma szerepel, a visszahívás 80% lenne.

![A betanítási eredmények a teljes pontosságot és visszavonást, valamint az osztályozó egyes címkék pontosságát és felidézését mutatják be.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Valószínűségi határérték

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Tanítási ismétlések kezelése

Az osztályozó betanításakor minden alkalommal létre kell hoznia egy új _iterációt_ a saját frissített teljesítmény-metrikákkal. Az összes iterációt megtekintheti a **teljesítmény** lap bal oldali ablaktábláján. Emellett a **delete (Törlés** ) gomb is látható, amellyel törölhető egy iteráció, ha elavult. Ha töröl egy iterációt, akkor minden olyan rendszerképet töröl, amely egyedileg hozzá van rendelve.

A betanított modellek programozott módon való elérésének megismeréséhez tekintse meg [a modell használata az előrejelzési API-val](./use-prediction-api.md) című témakört.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre és taníthat képbesorolási modellt a Custom Vision webhelyén. A következő lépés a modell fejlesztésének iterációs folyamatával kapcsolatos további információk.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](test-your-model.md)

* [Mi a Custom Vision?](./overview.md)