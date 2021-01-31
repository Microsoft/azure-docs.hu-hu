---
title: 'Gyors útmutató: objektum-detektor létrehozása a Custom Vision webhellyel'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre, betaníthat és tesztelheti egy objektum-detektor modellt a Custom Vision webhelyről.
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
ms.openlocfilehash: 5ecd5fee565a8d31e0ff05f3b234771446242d02
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221295"
---
# <a name="quickstart-build-an-object-detector-with-the-custom-vision-website"></a>Gyors útmutató: objektum-detektor létrehozása a Custom Vision webhellyel

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy Object detektor-modellt a Custom Vision webhellyel. A modell létrehozása után tesztelheti az új rendszerképekkel, és végül integrálhatja azt a saját rendszerkép-felismerő alkalmazásba.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Előfeltételek

- Lemezképek készlete, amelyekkel betaníthatja a detektor modelljét. A GitHubon használhatja a [minta lemezképek](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images) készletét. Emellett az alábbi tippek alapján is kiválaszthatja saját képeit.

## <a name="create-custom-vision-resources"></a>Custom Vision erőforrások létrehozása

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Új projekt létrehozása

A böngészőben nyissa meg a [Custom Vision weblapot](https://customvision.ai) , és válassza a __Bejelentkezés__ lehetőséget. Jelentkezzen be ugyanazzal a fiókkal, amelyet a Azure Portalba való bejelentkezéshez használt.

![A bejelentkezési oldal képe](./media/browser-home.png)


1. Az első projekt létrehozásához válassza az **új projekt** lehetőséget. Ekkor megjelenik az **új projekt létrehozása** párbeszédpanel.

    ![Az új projekt párbeszédpanelen a név, a leírás és a tartományok mezői láthatók.](./media/get-started-build-detector/new-project.png)

1. Adja meg a projekt nevét és leírását. Ezután válasszon ki egy erőforráscsoportot. Ha a bejelentkezett fiók egy Azure-fiókhoz van társítva, az erőforráscsoport legördülő lista megjeleníti az összes olyan Azure-erőforráscsoportot, amely Custom Vision Service erőforrást tartalmaz. 

   > [!NOTE]
   > Ha nincs elérhető erőforráscsoport, győződjön meg róla, hogy a [customvision.ai](https://customvision.ai) -ba jelentkezett be ugyanazzal a fiókkal, mint amikor a [Azure Portalba](https://portal.azure.com/)való bejelentkezéshez használt. Továbbá erősítse meg, hogy ugyanazt a "könyvtárat" választotta a Custom Vision webhelyén abban a Azure Portal könyvtárban, ahol a Custom Vision erőforrásai találhatók. Mindkét helyen kiválaszthatja a könyvtárat a képernyő jobb felső sarkában található legördülő menü fiók menüjéből. 

1. Válassza az __objektum felismerése__ a __projektek típusai__ alatt lehetőséget.

1. Ezután válassza ki a rendelkezésre álló tartományok egyikét. Az egyes tartományok a következő táblázatban leírtak szerint optimalizálja az adott típusú képek detektorát. Ha szeretné, később is megváltoztathatja a tartományt.

    |Tartomány|Cél|
    |---|---|
    |__Általános__| Az objektum-észlelési feladatok széles körére optimalizált. Ha a többi tartomány egyike sem megfelelő, vagy nem biztos benne, hogy melyik tartományt szeretné kiválasztani, válassza ki az általános tartományt. |
    |__Embléma__|A képeken található márka emblémák keresésére optimalizált.|
    |__Termékek a polcokon__|A polcokon található termékek észlelésére és besorolására optimalizált.|
    |__Kompakt tartományok__| A mobileszközök valós idejű objektum-észlelésének korlátaira optimalizált. A kompakt tartományok által generált modellek helyileg is futtathatók.|

1. Végül válassza a __projekt létrehozása__ lehetőséget.

## <a name="choose-training-images"></a>Képzési lemezképek kiválasztása

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Ebben a szakaszban feltölti és manuálisan címkézi a képeket az érzékelő betanításához. 

1. Képek hozzáadásához válassza a __képek hozzáadása__ lehetőséget, majd válassza a __helyi fájlok tallózása__ lehetőséget. A Képek feltöltéséhez kattintson a __Megnyitás__ gombra.

    ![A képek hozzáadása vezérlő megjelenik a bal felső sarokban, és az alsó középen lévő gomb.](./media/get-started-build-detector/add-images.png)

1. A feltöltött képeket a felhasználói felület **címkézetlen** szakaszában tekintheti meg. A következő lépés az, hogy manuálisan címkézze azokat az objektumokat, amelyeket el szeretne sajátítani a detektorban. Kattintson az első képre a címkézési párbeszédpanel megnyitásához. 

    ![Feltöltött képek a címkézett szakaszban](./media/get-started-build-detector/images-untagged.png)

1. Kattintson és húzzon egy téglalapot az objektum körül a képen. Ezután adjon meg egy új címke nevet a **+** gombbal, vagy válasszon ki egy meglévő címkét a legördülő listából. Fontos, hogy megcímkézje az objektum (ok) összes példányát, amelyet fel szeretne deríteni, mert az érzékelő a címkézetlen háttér területét használja negatív példaként a képzésben. Ha elkészült a címkézéssel, kattintson a jobb oldalon található nyílra a címkék mentéséhez és a következő képre való áttéréshez.

    ![Objektum címkézése téglalap alakú kijelöléssel](./media/get-started-build-detector/image-tagging.png)

Egy másik rendszerkép feltöltéséhez térjen vissza a szakasz elejére, és ismételje meg a lépéseket.

## <a name="train-the-detector"></a>A detektor betanítása

Az érzékelő modell betanításához kattintson a **vonat** gombra. A detektor az összes aktuális lemezképet és azok címkéit használja az egyes címkézett objektumok azonosítására szolgáló modell létrehozásához.

![A weblap fejlécének jobb felső részén található vonat gomb](./media/getting-started-build-a-classifier/train01.png)

A betanítási folyamat csak néhány percet vesz igénybe. Ebben az időszakban a betanítási folyamattal kapcsolatos információk a **teljesítmény** lapon jelennek meg.

![A böngészőablakban a főszakasz betanítási párbeszédablaka](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>A detektor kiértékelése

A betanítás befejezése után a modell teljesítménye kiszámítva és megjelenik. A Custom Vision szolgáltatás a képzéshez elküldött rendszerképeket használja a pontosság, a visszahívás és az átlagos pontosság kiszámításához. A pontosság és a visszahívás két különböző mérést végez a detektor hatékonyságát illetően:

- A **pontosság** a megfelelő azonosított besorolások töredékét jelzi. Ha például a modell 100 lemezképeket azonosított, és 99 közülük valójában kutyák voltak, akkor a pontosság 99% lenne.
- A **visszahívás** azt jelzi, hogy a tényleges besorolások hányada helyesen azonosítható. Ha például valóban 100-es Alma-lemezképek voltak, és a modellben a 80 as Alma szerepel, a visszahívás 80% lenne.
- Az átlagos pontosság a **középérték** (AP) átlagos értéke. Az AP a pontosság/visszahívás görbe alatti terület (az egyes előrejelzések visszahívása alapján végzett pontosság).

![A betanítási eredmények a teljes pontosságot és a visszahívást, valamint az átlagos pontosságot mutatják.](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>Valószínűségi határérték

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

### <a name="overlap-threshold"></a>Átfedési küszöbérték

Az **átfedésben lévő küszöbérték** csúszkával megtudhatja, hogy az objektumok előrejelzésének helyességét hogyan kell "helyesnek" tekinteni a betanítás során. Beállítja a minimálisan megengedett átfedést az előre jelzett objektum-határoló mező és a tényleges felhasználó által megadott határoló mező között. Ha a határoló mezők nem fedik át ezt a mértéket, az előrejelzés nem tekinthető helyesnek.

## <a name="manage-training-iterations"></a>Tanítási ismétlések kezelése

Minden alkalommal, amikor betanítja a detektort, létrehoz egy új _iterációt_ a saját frissített teljesítmény-metrikákkal. Az összes iterációt megtekintheti a **teljesítmény** lap bal oldali ablaktábláján. A bal oldali panelen a **delete (Törlés** ) gomb is látható, amellyel törölhető egy iteráció, ha elavult. Ha töröl egy iterációt, akkor minden olyan rendszerképet töröl, amely egyedileg hozzá van rendelve.

A betanított modellek programozott módon való elérésének megismeréséhez tekintse meg [a modell használata az előrejelzési API-val](./use-prediction-api.md) című témakört.

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre és taníthat egy Object detektor-modellt a Custom Vision webhelyén. A következő lépés a modell fejlesztésének iterációs folyamatával kapcsolatos további információk.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](test-your-model.md)

* [Mi a Custom Vision?](./overview.md)
