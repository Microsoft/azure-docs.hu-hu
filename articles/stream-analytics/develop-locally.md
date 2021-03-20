---
title: Azure Stream Analytics feladatok helyi fejlesztése és hibakeresése
description: Megtudhatja, hogyan fejlesztheti és tesztelheti Azure Stream Analytics feladatait a helyi számítógépen, mielőtt azokat a Azure Portalban futtatná.
ms.author: sujie
author: su-jie
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 18df480dab90d9ab127bb96971fc19cdc5a361ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016473"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Azure Stream Analytics feladatok helyi fejlesztése és hibakeresése

Noha Azure Stream Analytics feladatok létrehozására és tesztelésére is lehetősége van a Azure Portalban, számos fejlesztő inkább a helyi fejlesztési élményt részesíti előnyben. A Stream Analytics megkönnyíti kedvenc Kódszerkesztő és fejlesztői eszközeinek létrehozását és tesztelését az Azure Event hub, a IoT Hub és a Blob Storage élő esemény-adatfolyamokból származó feladatok létrehozásához és teszteléséhez egy teljes mértékben működő, egyetlen csomópontot futtató helyi futtatókörnyezet használatával. A feladatokat közvetlenül a helyi fejlesztési környezetből is beküldheti az Azure-ba.

## <a name="local-development-environments"></a>Helyi fejlesztési környezetek

A Stream Analytics feladatok helyi számítógépen való fejlesztésének módja az eszköz beállításaitól és a szolgáltatás rendelkezésre állásának függvénye. Az egyes fejlesztői környezetek által támogatott funkciók megtekintéséhez tekintse meg [Azure stream Analytics szolgáltatások összehasonlítását](feature-comparison.md) .

A következő táblázat környezetei támogatják a helyi fejlesztést:

|Környezet                              |Description    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| A Visual Studio Code-hoz készült [Azure stream Analytics Tools bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) lehetővé teszi a stream Analytics-feladatok helyi és a felhőben való létrehozását, kezelését, valamint a gazdag IntelliSense és a natív verziókövetés használatát. Támogatja a fejlesztést Linux, MacOS és Windows rendszeren. További információ: [Azure stream Analytics-feladatok létrehozása a Visual Studio Code-ban](quick-create-visual-studio-code.md). A bővítmény támogatja a [Visual Studio Codespaces](https://visualstudio.microsoft.com/services/visual-studio-codespaces/) is, amely egy felhőben üzemeltetett fejlesztői környezet.|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |A Stream Analytics-eszközök az Azure fejlesztési és adattárolási és-feldolgozási számítási feladatainak részét képezik a Visual Studióban. A Visual Studióval egyéni C# felhasználó által definiált függvények és deszerializálók írhatók. További információ: Azure Stream Analytics- [feladatok létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md).|
|[Parancssor vagy terminál](stream-analytics-tools-for-visual-studio-cicd.md)|A Azure Stream Analytics CI/CD NuGet-csomag eszközöket biztosít a Visual Studio Project buildhez, a helyi teszteléshez egy tetszőleges gépen. A Azure Stream Analytics CI/CD NPM-csomag eszközöket biztosít a Visual Studio Code Project buildek számára (amely létrehoz egy Azure Resource Manager sablont) egy tetszőleges gépen.|

## <a name="next-steps"></a>Következő lépések

* [A Visual Studio Code segítségével helyileg tesztelheti Stream Analytics lekérdezéseket a mintaadatok használatával](visual-studio-code-local-run.md)
* [A Visual Studio Code használatával helyileg tesztelheti Stream Analytics lekérdezéseket élő stream-bevitelsel](visual-studio-code-local-run-live-input.md)
* [A Visual Studióval helyileg tesztelheti Stream Analytics lekérdezéseit](stream-analytics-vs-tools-local-run.md)
* [Élő adatellenőrzés helyi tesztelése a Visual studióhoz készült Azure Stream Analytics Tools használatával](stream-analytics-live-data-local-testing.md)
