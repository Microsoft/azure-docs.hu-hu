---
title: Az Azure Stream Analytics-lekérdezések Visual studióval helyileg tesztelése
description: Ez a cikk ismerteti, hogyan tesztelhet helyileg az Azure Stream Analytics Tools for Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 1b86085a76f5ff87147db9dbd0a584784f5e4a2e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64686488"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Stream Analytics-lekérdezések Visual studióval helyileg tesztelése

Az Azure Stream Analytics tools for Visual Studio segítségével a Stream Analytics-feladatok helyileg mintaadatokkal tesztelheti.

Ezzel [rövid](stream-analytics-quick-create-vs.md) megtudhatja, hogyan hozhat létre egy Stream Analytics-feladatot a Visual Studio használatával.

## <a name="test-your-query"></a>A lekérdezés tesztelése

Az Azure Stream Analytics-projektben kattintson duplán a **Script.asaql** a szkript a szerkesztőben való megnyitásához. A lekérdezést, hogy vannak-e szintaxishibát állíthat össze. A Lekérdezésszerkesztő támogatja az IntelliSense, a színezést szintaxis és a egy hiba jelölő.

![Lekérdezésszerkesztő](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Helyi bemenet hozzáadása

A lekérdezéshez helyi statikus adatok érvényesítéséhez kattintson a jobb gombbal a bemeneti, és válassza **helyi bemenet hozzáadása**.
   
![Helyi bemenet hozzáadása](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
Az előugró ablakban válassza a helyi elérési utat a mintaadatok és **mentése**.
   
![Helyi bemenet hozzáadása](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
Nevű fájl **local_EntryStream.json** automatikusan hozzáadja a bemeneti mappában.
   
![Bemeneti mappa helyi fájlok listája](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
Válassza ki **futtatása helyileg** a Lekérdezésszerkesztő. Vagy nyomja le az F5 billentyűt.
   
![Helyileg történő futtatása](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
A kimenet táblázatos formában közvetlenül a Visual Studióból lehet megtekinteni.

![A kimenet táblázatos formában](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

A kimeneti elérési út a konzol kimenetében található. Nyomja le bármelyik billentyűt, nyissa meg az eredményt mappát.
   
![Helyi futtatás](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Ellenőrizze az eredményeket a helyi mappában.
   
![Helyi mappa eredménye](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Minta beviteli
Egy helyi fájlba a bemeneti forrásból is gyűjthet a bemeneti mintaadatokat. Kattintson a jobb gombbal a bemeneti konfigurációs fájlt, és válassza ki **mintaadatok**. 

![Adatmintavétel](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Csak streamelés az Event Hubs vagy IoT-központok a mintaadatok is. Más bemeneti forrásokkal nem támogatottak. Az előugró párbeszédpanelen adja meg a minta adatait, és válassza ki a helyi elérési útja **minta**.

![Példa adatok konfigurációja](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Láthatja, hogy a folyamatot a a **kimeneti** ablak. 

![Minta kimeneti adatok](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>További lépések

* [A Visual Studio használata az Azure Stream Analytics-feladatok megtekintése](stream-analytics-vs-tools.md)
* [Rövid útmutató: A Visual Studio használatával a Stream Analytics-feladat létrehozása](stream-analytics-quick-create-vs.md)
* [Oktatóanyag: Üzembe helyezés az Azure Stream Analytics-feladat a CI/CD Azure DevOps használatával](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Folyamatos integráció és fejlesztés a Stream Analytics eszközeivel](stream-analytics-tools-for-visual-studio-cicd.md)