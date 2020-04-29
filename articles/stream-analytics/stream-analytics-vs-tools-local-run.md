---
title: Azure Stream Analytics lekérdezések helyi tesztelése a Visual Studióban
description: Ez a cikk azt ismerteti, hogyan lehet a lekérdezéseket helyileg tesztelni a Visual studióhoz készült Azure Stream Analytics eszközökkel.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 34c8555356d5c0142d7b677c8119fe66806ab064
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76834909"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>A Visual Studióval helyileg tesztelheti Stream Analytics lekérdezéseit

A Visual studióhoz készült Azure Stream Analytics Tools segítségével helyileg tesztelheti Stream Analytics-feladatait mintaadatok vagy [élő adatok](stream-analytics-live-data-local-testing.md)használatával. 

Ebből a rövid útmutatóból megtudhatja [, hogyan](stream-analytics-quick-create-vs.md) hozhat létre stream Analytics feladatokat a Visual Studióval.

## <a name="test-your-query"></a>A lekérdezés tesztelése

A Azure Stream Analytics projektben kattintson duplán a **script. asaql** elemre a szkript megnyitásához a szerkesztőben. A lekérdezés fordításával megállapíthatja, hogy vannak-e szintaktikai hibák. A lekérdezés-szerkesztő támogatja az IntelliSense, a szintaxis színezését és a hiba jelölőjét.

![Lekérdezésszerkesztő](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Helyi bemenet hozzáadása

A helyi statikus adatokra vonatkozó lekérdezés ellenőrzéséhez kattintson a jobb gombbal a bevitelre, és válassza a **helyi bemenet hozzáadása**elemet.
   
![Helyi bemenet hozzáadása](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
Az előugró ablakban válassza a mintaadatok lehetőséget a helyi elérési útról, és **mentse**.
   
![Helyi bemenet hozzáadása](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
A rendszer automatikusan felvesz egy **local_EntryStream. JSON** nevű fájlt a bemenetek mappájába.
   
![Helyi bemeneti mappa fájljának listája](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
A lekérdezés-szerkesztőben válassza a **helyi Futtatás** lehetőséget. Vagy nyomja le az F5 billentyűt.
   
![Helyi Futtatás](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
A kimenet táblázatos formában is megtekinthető közvetlenül a Visual studióból.

![Kimenet táblázatos formátumban](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

A kimeneti útvonalat a konzol kimenetében találja. A találati mappa megnyitásához nyomja le bármelyik billentyűt.
   
![Helyi futtatás](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Vizsgálja meg az eredményeket a helyi mappában.
   
![Helyi mappa eredménye](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Minta bemenet
A bemeneti forrásokból is gyűjthet minta bemeneti adatokat egy helyi fájlba. Kattintson a jobb gombbal a bemeneti konfigurációs fájlra, és válassza a **mintaadatok**elemet. 

![Adatmintavétel](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Csak Event Hubs vagy IoT hubokból származó adatfolyamokat lehet megtekinteni. Más bemeneti források nem támogatottak. Az előugró párbeszédpanelen töltse ki a helyi elérési utat a mintaadatok mentéséhez és a **minta**elem kiválasztásához.

![Mintaadatok konfigurálása](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
A folyamat előrehaladását a **kimenet** ablakban tekintheti meg. 

![Mintaadatok kimenete](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Stream Analytics-feladatok létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [A Visual Studio használata Azure Stream Analytics feladatok megtekintéséhez](stream-analytics-vs-tools.md)
* [Élő adatellenőrzés helyi tesztelése a Visual Studio Azure Stream Analytics eszközeivel (előzetes verzió)](stream-analytics-live-data-local-testing.md)
* [Oktatóanyag: Azure Stream Analytics-feladatok üzembe helyezése CI/CD-vel az Azure DevOps használatával](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Folyamatos integráció és fejlesztés a Stream Analytics eszközeivel](stream-analytics-tools-for-visual-studio-cicd.md)
