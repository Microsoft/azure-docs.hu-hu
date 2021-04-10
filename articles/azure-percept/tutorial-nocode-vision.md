---
title: Kód nélküli jövőkép megoldás létrehozása az Azure Percept Studióban
description: Megtudhatja, hogyan hozhat létre nem Code-alapú megoldást az Azure Percept Studióban, és hogyan helyezheti üzembe az Azure Percept DK-ben
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 49e5db729dab7abaa440b1adf6a61e9e52a1efbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023129"
---
# <a name="create-a-no-code-vision-solution-in-azure-percept-studio"></a>Kód nélküli jövőkép megoldás létrehozása az Azure Percept Studióban

Az Azure Percept Studio lehetővé teszi egyéni számítógép-vizuális megoldások létrehozását és üzembe helyezését, és nincs szükség kódolásra. Ebben a cikkben:

- Víziós projekt létrehozása az [Azure Percept Studióban](https://go.microsoft.com/fwlink/?linkid=2135819)
- Begyűjtheti a betanítási képeket a fejlesztői készlet
- A betanítási lemezképek címkézése [Custom Vision](https://www.customvision.ai/)
- Az egyéni objektum-észlelési vagy besorolási modell betanítása
- A modell üzembe helyezése a fejlesztői készlet
- Fejlessze modelljét az átképzés beállításával

Ez az oktatóanyag olyan fejlesztők számára megfelelő, akik egyszerűen nem rendelkeznek mesterséges intelligenciával és az Azure Percept-vel való ismerkedéssel.

## <a name="prerequisites"></a>Előfeltételek

- Azure Percept DK (fejlesztői készlet)
- [Azure-előfizetés](https://azure.microsoft.com/free/)
- Az Azure Percept DK telepítési felülete: csatlakoztatta a fejlesztői készlet egy Wi-Fi hálózathoz, létrehozott egy IoT Hub, és csatlakoztatta a fejlesztői készlet a IoT Hub

## <a name="create-a-vision-prototype"></a>Víziós prototípus létrehozása

1. Indítsa el a böngészőt, és nyissa meg az [Azure Percept studiót](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Az Áttekintés lapon kattintson a **bemutatók & oktatóanyagok** fülre.  Az  :::image type="content" source="./media/tutorial-nocode-vision/percept-studio-overview-inline.png" alt-text="Azure Percept Studio áttekintése képernyő." lightbox="./media/tutorial-nocode-vision/percept-studio-overview.png":::

1. A **jövőkép oktatóanyagok és bemutatók** területen kattintson **a jövőkép prototípus létrehozása** elemre.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-tutorials-and-demos-inline.png" alt-text="Az Azure Percept Studio-bemutatók és-oktatóanyagok képernyője." lightbox="./media/tutorial-nocode-vision/vision-tutorials-and-demos.png":::

1. Az **új Azure Percept Custom Vision Prototype** lapon tegye a következőket:

    1. A **projekt neve** mezőben adja meg a látási prototípus nevét.

    1. A **projekt leírása** mezőben adja meg a látási prototípus leírását.

    1. Az **eszköz típusa** legördülő menüben válassza az **Azure Percept DK** elemet.

    1. Válasszon ki egy erőforrást az **erőforrás** legördülő menüben, vagy kattintson az **új erőforrás létrehozása** elemre. Ha úgy dönt, hogy új erőforrást hoz létre, a **Létrehozás** ablakban tegye a következőket:
        1. Adja meg az új erőforrás nevét.
        1. Válassza ki Azure-előfizetését.
        1. Válasszon egy erőforráscsoportot, vagy hozzon létre egy újat.
        1. Válassza ki a kívánt régiót.
        1. Válassza ki a díjszabási szintet (javasoljuk, hogy S0).
        1. Kattintson az ablak alján található **Létrehozás** gombra.

        :::image type="content" source="./media/tutorial-nocode-vision/create-resource.png" alt-text="Erőforrás-ablak létrehozása":::

    1. A **Project Type (projekt típusa) mezőben** adja meg, hogy a víziós projekt el fogja-e végezni az objektumok észlelését A Project típusaival kapcsolatos további információkért kattintson a **segítség a választáshoz** lehetőségre.

    1. Az **optimalizáláshoz** válassza ki, hogy szeretné-e optimalizálni a projektet a pontosság, az alacsony hálózati késés vagy a kettő egyenlege tekintetében.

    1. Kattintson a **Létrehozás** gombra.

        :::image type="content" source="./media/tutorial-nocode-vision/create-prototype.png" alt-text="Hozzon létre egyéni megjelenítő prototípus-képernyőt.":::

## <a name="connect-a-device-to-your-project-and-capture-images"></a>Eszköz csatlakoztatása a projekthez és lemezképek rögzítése

A víziós megoldás létrehozása után hozzá kell adnia a fejlesztői készlet és a hozzá tartozó IoT Hub.

1. Kapcsolja be a fejlesztői készlet.

1. A **IoT hub** legördülő menüben válassza ki azt az IoT hubot, amelyhez a fejlesztői készlet kapcsolódott az Oobe során.

1. Az **eszközök** legördülő menüben válassza ki a fejlesztői készlet.

Ezután be kell töltenie képeket, vagy képeket kell rögzítenie az AI-modell betanításához. Javasoljuk, hogy legalább 30 képet töltsön fel a címkék típusára. Ha például egy kutyát és egy Cat-detektort szeretne létrehozni, fel kell töltenie legalább 30 képet a kutyákból és 30 képet a macskákról. Ha a képeket a fejlesztői készlet megjelenő SoM-val szeretné rögzíteni, tegye a következőket:

1. A **rendszerkép rögzítése** ablakban válassza az **eszköz stream megtekintése** lehetőséget a jövőkép SoM videó stream megtekintéséhez.

1. Ellenőrizze a videó streamet, és győződjön meg arról, hogy a víziós SoM-kamera helyesen van igazítva a betanítási képekhez. Szükség szerint végezze el a kívánt módosításokat.

1. A **rendszerkép rögzítése** ablakban kattintson a **fénykép készítése** elemre.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture.png" alt-text="Rendszerkép-rögzítő képernyő.":::

1. Azt is megteheti, hogy egy automatikus rendszerkép-rögzítést állít be, hogy egyszerre nagy mennyiségű képet gyűjtsön, ha ellenőrzi az **automatikus rendszerkép-rögzítési** mezőt. Válassza ki a kívánt képalkotási arányt a **rögzítési arány** és a **cél** alatt gyűjteni kívánt lemezképek teljes száma alapján. Kattintson az **automatikus rögzítés beállítása** elemre az automatikus rendszerkép-rögzítési folyamat elindításához.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture-auto.png" alt-text="Automatikus rendszerkép-rögzítés legördülő menüje":::

Ha elegendő fényképe van, kattintson a **Tovább gombra: képek címkézése és a modell tanítása** a képernyő alján. Az összes lemezkép [Custom Vision](https://www.customvision.ai/)lesz mentve.

> [!NOTE]
> Ha a betanítási képeket közvetlenül a Custom Visionba tölti fel, vegye figyelembe, hogy a képfájl mérete nem haladhatja meg a 6MB.

## <a name="tag-images-and-train-your-model"></a>Képek címkézése és a modell betanítása

A modell betanítása előtt adjon hozzá címkéket a képekhez.

1. A **képek címkézése és a modell betanítása** lapon kattintson a **projekt megnyitása Custom Vision** lehetőségre.

1. A **Custom Vision** lap bal **oldalán kattintson a** **címkék** elemre az előző lépésben összegyűjtött rendszerképek megtekintéséhez. Válasszon ki egy vagy több címkézetlen lemezképet.

1. A **rendszerkép részletei** ablakban kattintson a képre a címkézés megkezdéséhez. Ha a projekt típusaként az objektum észlelése lehetőséget választotta, akkor a címkével ellátni kívánt objektumok köré is meg kell rajzolni egy [határoló mezőt](../cognitive-services/custom-vision-service/get-started-build-detector.md#upload-and-tag-images) . Szükség szerint módosítsa a határoló mezőt. Írja be az Object címkét, és kattintson a **+** címke alkalmazásához. Ha például olyan víziós megoldást hozott létre, amely értesíti Önt, ha egy áruházi polc újrakészletezésre szorul, adja hozzá az "üres polc" címkét az üres polcok képeihez, és adja hozzá a "teljes polc" címkét a teljes értékű polcokhoz tartozó képekhez. Ismételje meg az összes címkézetlen lemezképet.

    :::image type="content" source="./media/tutorial-nocode-vision/image-tagging.png" alt-text="Képcímkéző képernyő a Custom Visionban.":::

1. A képek címkézése után kattintson az ablak jobb felső sarkában található **X** ikonra. A **címkék** **elemre kattintva** megtekintheti az összes újonnan címkézett képet.

1. A képek címkézése után készen áll az AI-modell betanítására. Ehhez kattintson a **betanítás** elemre az oldal tetején. A modell betanításához legalább 15 rendszerképnek kell szerepelnie (legalább 30 használatával ajánlott). A képzés általában körülbelül 30 percet vesz igénybe, de hosszabb időt is igénybe vehet, ha a rendszerkép beállítása rendkívül nagy.

    :::image type="content" source="./media/tutorial-nocode-vision/train-model.png" alt-text="Betanítási Képválasztás a betanítás gomb kijelölve.":::

1. A képzés befejezését követően a képernyő megjeleníti a modell teljesítményét. Az eredmények kiértékelésével kapcsolatos további információkért tekintse meg a [modell értékelési dokumentációját](../cognitive-services/custom-vision-service/get-started-build-detector.md#evaluate-the-detector). A képzés után szükség esetén további rendszerképeken is [tesztelheti a modelljét](../cognitive-services/custom-vision-service/test-your-model.md) . Minden alkalommal, amikor betanítja a modellt, új iterációként lesz mentve. A modell teljesítményének növelésével kapcsolatos további információkért a [Custom Vision dokumentációjában](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) talál további információt.

    :::image type="content" source="./media/tutorial-nocode-vision/iteration.png" alt-text="Modellek képzésének eredményei.":::

    > [!NOTE]
    > Ha úgy dönt, hogy teszteli a modellt a Custom Vision további rendszerképeken, vegye figyelembe, hogy a képfájlok mérete nem haladhatja meg a 4MB nál.

Ha elégedett a modell teljesítményével, zárja be Custom Vision a böngésző lap bezárásával.

## <a name="deploy-your-ai-model"></a>AI-modell üzembe helyezése

1. Lépjen vissza az Azure Percept Studio lapra, és kattintson a **Tovább gombra: kiértékelés és üzembe helyezés** a képernyő alján.

1. A **kiértékelés és üzembe helyezés** ablak megjeleníti a kiválasztott modell iterációjának teljesítményét. Válassza ki a fejlesztői készlet központilag telepíteni kívánt iterációt a **modell iteráció** legördülő menüben, majd kattintson a **modell üzembe helyezése** gombra a képernyő alján.

    :::image type="content" source="./media/tutorial-nocode-vision/deploy-model-inline.png" alt-text="Modell üzembe helyezése képernyő." lightbox="./media/tutorial-nocode-vision/deploy-model.png":::

1. A modell üzembe helyezése után tekintse meg az eszköz video streamjét, hogy megtekintse a modellt a működés közben.

    :::image type="content" source="./media/tutorial-nocode-vision/view-device-stream.png" alt-text="A fejhallgató-detektor működés közbeni megjelenítése.":::

Az ablak bezárása után bármikor visszatérhet és szerkesztheti a víziós projektet, ha az Azure Percept Studio kezdőlapján az **AI-projektek** **elemre kattint,** és kiválasztja a víziós projekt nevét.

:::image type="content" source="./media/tutorial-nocode-vision/vision-project-inline.png" alt-text="Jövőkép projekt lapja" lightbox="./media/tutorial-nocode-vision/vision-project.png":::

## <a name="improve-your-model-by-setting-up-retraining"></a>Fejlessze modelljét az átképzés beállításával

Miután betanítja a modellt, és üzembe helyezte az eszközön, javíthatja a modell teljesítményét úgy, hogy átképzési paramétereket állít be a további betanítási adatok rögzítéséhez. Ez a funkció a betanított modell teljesítményének javítására szolgál azáltal, hogy lehetővé teszi a lemezképek valószínűségi tartományon alapuló rögzítését. Beállíthatja például, hogy az eszköz csak akkor rögzítsen betanítási képeket, ha a valószínűség alacsony. Itt talál [további útmutatást](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) további képek hozzáadásához és a betanítási információk kiegyensúlyozásához.

1. Az átképzés beállításához lépjen vissza a **projektbe**, majd a **projekt összegzése** elemre.
1. A **rendszerkép rögzítése** lapon válassza az **automatikus rendszerkép-rögzítés** lehetőséget, és **állítsa be az átképzést**.
1. Állítsa be az automatikus képrögzítést úgy, hogy egyszerre nagy mennyiségű képet gyűjtsön az **automatikus rendszerkép-rögzítési** mező ellenőrzésével.
1. Válassza ki a kívánt képalkotási arányt a **rögzítési arány** és a **cél** alatt gyűjteni kívánt lemezképek teljes száma alapján.
1. Az **átképzés beállítása** szakaszban válassza ki azt az iterációt, amelyhez további betanítási adatmennyiséget szeretne rögzíteni, majd válassza ki a valószínűségi tartományt. Csak a valószínűségi aránynak megfelelő lemezképek lesznek feltöltve a projektbe.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-image-capture.png" alt-text="rendszerkép rögzítése.":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha létrehozott egy új Azure-erőforrást ehhez az oktatóanyaghoz, és már nem kívánja fejleszteni vagy használni a jövőképét, hajtsa végre az alábbi lépéseket az erőforrás törléséhez:

1. Nyissa meg az [Azure Portal](https://ms.portal.azure.com/).
1. Kattintson az **összes erőforrás** elemre.
1. Kattintson az oktatóanyag során létrehozott erőforrás melletti jelölőnégyzetre. Az erőforrástípus **Cognitive Servicesként** jelenik meg.
1. Kattintson a **Törlés** ikonra a képernyő felső részén.

## <a name="video-walkthrough"></a>Bemutató videó

A fent ismertetett lépések vizuális áttekintését a következő videóban találja:

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

</br>

## <a name="next-steps"></a>Következő lépések

Következő lépésként tekintse meg az Azure Percept Studio további látási megoldási funkcióinak ismertetését ismertető cikket.

<!--
Add links to how-to articles and oobe article.
-->