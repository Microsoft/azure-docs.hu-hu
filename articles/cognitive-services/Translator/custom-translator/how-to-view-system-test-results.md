---
title: Rendszerteszt eredményeinek és üzembe helyezésének megtekintése – egyéni fordító
titleSuffix: Azure Cognitive Services
description: Ha a képzés sikeres, tekintse át a rendszerteszteket a betanítási eredmények elemzéséhez. Ha elégedett a betanítási eredményekkel, helyezzen üzembe egy központi telepítési kérelmet a betanított modellhez.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: cae2c95e56312c58d396d1e578f4677ce2b14aa2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895934"
---
# <a name="view-system-test-results"></a>Rendszer teszteredményeinek megtekintése

Ha a képzés sikeres, tekintse át a rendszerteszteket a betanítási eredmények elemzéséhez. Ha elégedett a betanítási eredményekkel, helyezzen üzembe egy központi telepítési kérelmet a betanított modellhez.

## <a name="system-test-results-page"></a>Rendszerteszt eredményei lap

Válasszon ki egy projektet, majd válassza ki a projekt modellek lapját, keresse meg a használni kívánt modellt, és végül válassza a test (teszt) lapot.

A teszt lapon a következőket láthatja:

1.  **RendszerA teszt eredményei:** A tesztelési folyamat eredménye a betanításokban. A tesztelési folyamat létrehozza a BLEU pontszámot.

    **Mondatok száma:** A tesztelési készletben hány párhuzamos mondatot használt a rendszer.

     **Bleu pontszáma:** A modell kiképzésének befejezése után létrehozott BLEU-pontszám.

    **Állapot:** Azt jelzi, hogy a tesztelési folyamat befejeződött vagy folyamatban van-e.

    ![Rendszerteszt eredményei](media/how-to/how-to-system-test-results.png)

2.  Kattintson a Rendszerteszt eredményeire, és ekkor megjelenik az eredmény részletei lap tesztelése. Ezen a lapon látható a tesztelési adatkészlet részét képező mondatok gépi fordítása.

3.  A teszt eredményének részleteit tartalmazó oldalon található tábla két oszloppal rendelkezik – egyet a pár egyes nyelveihez. A forrás nyelvének oszlopa a lefordítani kívánt mondatot jeleníti meg. A célként megadott nyelv oszlopa két mondatot tartalmaz az egyes sorokban.

    **Ref:** Ez a mondat a forrásként szolgáló mondatnak a teszt adatkészletben megadott hivatkozási fordítása.

    **MT:** Ez a mondat a képzés elvégzése után a modell által elkészített, a forrásra vonatkozó mondat automatikus fordítása.

    ![Rendszerteszt eredményeinek összehasonlítása](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Teszt letöltése

A zip-fájl letöltéséhez kattintson a fordítások letöltése hivatkozásra. A zip a tesztelési adatkészletben a forrás mondatok gépi fordítását tartalmazza.

![Teszt letöltése](media/how-to/how-to-system-test-download.png)

Ez a letöltött zip-archívum három fájlt tartalmaz.

1.  **custom.mt.txt:** Ez a fájl a forrás nyelvi mondatok gépi fordítását tartalmazza a felhasználói adatgyűjtési modell által megcélzott nyelven.

2.  **ref.txt:** Ez a fájl a forrás nyelvi mondatok felhasználó által megadott fordításait tartalmazza a célnyelv nyelvén.

3.  **source.txt:** Ez a fájl mondatokat tartalmaz a forrás nyelvén.

    ![A rendszertesztek letöltött eredményei](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Modell üzembe helyezése

Központi telepítés igénylése:

1.  Válasszon ki egy projektet, lépjen a modellek lapra.

2. A sikeresen betanított modell esetében az "üzembe helyezés" gomb jelenik meg, ha nincs telepítve.

    ![Képernyőfelvétel: a modellek üzembe helyezésére szolgáló gomb kiemelése.](media/how-to/how-to-deploy-model.png)

3.  Kattintson a telepítés elemre.
4.  Válassza az **üzembe helyezett** lehetőséget azon régió (k) esetében, ahol szeretné telepíteni a modellt, majd kattintson a Save (Mentés) gombra. Több régió számára is kiválaszthatja az **üzembe helyezett** lehetőséget.

    ![Képernyőkép, amely bemutatja, hol telepíthet vagy helyezhet üzembe egy modellt.](media/how-to/how-to-deploy-model-regions.png)

5.  A modell állapotát az "állapot" oszlopban tekintheti meg.

>[!Note]
>Az egyéni Translator egy adott munkaterületen belül 10 telepített modellt támogat.

## <a name="update-deployment-settings"></a>Központi telepítési beállítások frissítése

A központi telepítési beállítások frissítése:

1.  Válasszon ki egy projektet, és lépjen a **modellek** lapra.

2. Egy sikeresen üzembe helyezett modell esetében megjelenik egy **frissítés** gomb.

    ![Képernyőfelvétel: a központi telepítési beállítások frissítésére szolgáló frissítés gomb.](media/how-to/how-to-update-undeploy-model.png)

3.  Válassza a **Frissítés** lehetőséget.
4.  Válassza az **üzembe helyezett** vagy a nem **telepített** lehetőséget azon régió (k) esetében, ahol a modellt központilag telepíteni szeretné, majd kattintson a **Save (Mentés**) gombra.

    ![Modell üzembe helyezése](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Ha az összes régió esetében a nem **telepített** lehetőséget választja, a modell nem települ minden régióból, és nem telepített állapotba kerül. A szolgáltatás már nem érhető el.

## <a name="next-steps"></a>Következő lépések

- Az üzembe helyezett egyéni fordítási modell használatának megkezdése a [Microsoft Translator Text API v3](../reference/v3-0-translate.md?tabs=curl)segítségével.
- Megtudhatja, [Hogyan kezelhet beállításokat](how-to-manage-settings.md) a munkaterület megosztásához, az előfizetési kulcs kezeléséhez.
- Ismerje meg [, hogyan telepítheti át munkaterületét és projektjét](how-to-migrate.md) a [Microsoft Translator hub](https://hub.microsofttranslator.com) használatával