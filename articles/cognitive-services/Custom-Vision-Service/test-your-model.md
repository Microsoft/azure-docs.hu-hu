---
title: Modell tesztelése és újratanítása – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan tesztelheti a rendszerképet, és hogyan végezheti el a modell átképzését a Custom Vision szolgáltatásban.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 736d135323e2a2df38a13dc46189e2a243fbe381
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097534"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Modell tesztelése és újratanítása Custom Vision Service

A modell betanítása után gyorsan tesztelheti egy helyileg tárolt képpel vagy egy online képpel. A teszt a legutóbb betanított iterációt használja.

## <a name="test-your-model"></a>Modell tesztelése

1. A [Custom Vision weblapon](https://customvision.ai)válassza ki a projektet. A felső menüsor jobb oldalán válassza a **gyors teszt** lehetőséget. Ez a művelet megnyit egy **gyors teszttel** ellátott ablakot.

    ![A gyors teszt gomb az ablak jobb felső sarkában látható.](./media/test-your-model/quick-test-button.png)

2. A **gyors teszt** ablakban kattintson a **rendszerkép elküldése** mezőbe, és adja meg a teszthez használni kívánt rendszerkép URL-címét. Ha helyileg tárolt rendszerképet szeretne használni, kattintson a **helyi fájlok tallózása** gombra, és válasszon ki egy helyi képfájlt.

    ![A rendszerkép elküldése oldal képe](./media/test-your-model/submit-image.png)

A kiválasztott rendszerkép a lap közepén jelenik meg. Ezután az eredmények egy táblázat formájában jelennek meg a képen, két oszloppal **, címkével és** **megbízhatósággal**. Az eredmények megtekintése után lezárhatja a **gyors tesztelés** ablakát.

Most hozzáadhatja ezt a tesztoldalt a modellhez, majd Újrataníthatja a modellt.

## <a name="use-the-predicted-image-for-training"></a>A prediktív rendszerképek használata képzéshez

A korábban betanításra elküldött rendszerkép használatához kövesse az alábbi lépéseket:

1. Az osztályozó által küldött képek megtekintéséhez nyissa meg a [Custom Vision weblapot](https://customvision.ai) , és válassza a __jóslatok__ fület.

    ![Az előrejelzések lap képe](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > Az alapértelmezett nézet az aktuális iteráció képeit jeleníti meg. Az __iteráció__ legördülő mező használatával megtekintheti az előző ismétlések során elküldött képeket.

2. Vigye az egérmutatót egy képre az osztályozó által előre jelzett címkék megtekintéséhez.

    > [!TIP]
    > A rendszerképek rangsorolva vannak, így azok a képek, amelyek az osztályozó legnagyobb nyereségét tudják elérni, felül vannak rendezve. Egy másik rendezés kiválasztásához használja a __Rendezés__ szakaszt.

    Ha képet szeretne adni a betanítási adatokhoz, válassza ki a képet, válassza ki a címkét, majd válassza a __Mentés és bezárás__ lehetőséget. A rendszer eltávolítja a lemezképet az __előrejelzések__ közül, és hozzáadja őket a betanítási lemezképekhez. A __betanítási képek__ lapon tekintheti meg.

    ![A címkézési lap képe](./media/test-your-model/tag-image.png)

3. A __vonat__ gomb használatával Újrataníthatja az osztályozó.

## <a name="next-steps"></a>Következő lépések

[Modell továbbfejlesztése](getting-started-improving-your-classifier.md)
