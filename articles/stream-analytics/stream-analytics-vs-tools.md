---
title: Azure Stream Analytics feladatok megtekintése a Visual Studióban
description: Megtudhatja, hogyan tekintheti meg, indíthatja el és állíthatja le, tesztelheti a kapcsolatokat, ellenőrizheti az eredményeket, és exportálhatja a Azure Stream Analytics feladatokat a Visual Studióval.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/10/2018
ms.openlocfilehash: 4b568fa8d9e2fb8bd00acb27b0dc3f7c45c09c57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019125"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>A Visual Studio használata Azure Stream Analytics feladatok megtekintéséhez

A Visual studióhoz készült Azure Stream Analytics Tools megkönnyíti a fejlesztők számára, hogy közvetlenül az IDE-ből kezelhesse Stream Analytics-feladatait. Azure Stream Analytics eszközökkel a következőket teheti:
- [Új feladatok létrehozása](stream-analytics-quick-create-vs.md)
- Feladatok elindítása, leállítása és [figyelése](stream-analytics-monitor-jobs-use-vs.md)
- A feladatok eredményeinek megtekintése
- Meglévő feladatok exportálása projektbe
- A bemeneti és kimeneti kapcsolatok tesztelése
- [Lekérdezések helyi futtatása](stream-analytics-vs-tools-local-run.md)

Ismerje meg, hogyan [telepítheti Azure stream Analytics Tools for Visual Studio alkalmazást](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>A feladatok nézet megismerése

A feladat nézettel a Visual studióból Azure Stream Analytics feladatokkal dolgozhat.

### <a name="open-the-job-view"></a>A feladatok nézet megnyitása

1. A **Server Explorerben** válassza **stream Analytics feladatok** elemet, majd válassza a **frissítés** lehetőséget. A feladatnak **stream Analytics feladatok** területen kell megjelennie.

    ![Stream Analytics Server Explorer-lista](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)

2. Bontsa ki a feladatok csomópontot, majd kattintson duplán a feladatok **nézet** csomópontra a feladatok nézet megnyitásához.
    
   ![Kibontott feladatok csomópontja](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Feladatok elindítása és leállítása

Azure Stream Analytics feladatok teljes mértékben kezelhetők a Visual Studióban található feladatok nézetből. A vezérlők segítségével elindíthatja, leállíthatja vagy törölheti a feladatokat.
    
   ![Stream Analytics feladatok vezérlői](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)

## <a name="check-job-results"></a>A feladatok eredményeinek megtekintése

A Stream Analytics Tools for Visual Studio jelenleg támogatja a Azure Data Lake Storage és a blob Storage kimeneti előnézetét. Az eredmény megtekintéséhez egyszerűen kattintson duplán a feladatütemezés kimenet csomópontjára a feladatok **nézetben** , és adja meg a megfelelő hitelesítő adatokat.

   ![Stream Analytics feladatok blobjának kimenete](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)

## <a name="export-jobs-to-a-project"></a>Feladatok exportálása projektbe

A meglévő feladatok egy projektbe való exportálásának kétféleképpen lehetséges.

1. A **Server Explorerben** a stream Analytics feladatok csomópontban kattintson a jobb gombbal a feladat csomópontra. Válassza **az Exportálás új stream Analytics projektbe** lehetőséget.
    
   ![Feladatok exportálása a projektbe](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    A generált projekt megjelenik a **Megoldáskezelőban**.
    
   ![A megoldáskezelő](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. A feladatok nézetben válassza a **projekt előállítása** lehetőséget.
    
   ![Projekt előállítása a feladatokból nézetből](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Kapcsolatok tesztelése

A bemeneti és kimeneti kapcsolatok a **feladatok nézetből** választhatók ki a **kapcsolat tesztelése** legördülő menüből.

   ![A kapcsolatok legördülő menüjének tesztelése](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

A **tesztelési kapcsolatok** eredményei a **kimenet** ablakban jelennek meg.

   ![A kapcsolatok eredményeinek tesztelése](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Következő lépések

* [Azure Stream Analytics feladatok figyelése és kezelése a Visual Studióval](stream-analytics-monitor-jobs-use-vs.md)
* [Rövid útmutató: Stream Analytics-feladatok létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Folyamatos integráció és fejlesztés a Stream Analytics eszközeivel](stream-analytics-tools-for-visual-studio-cicd.md)
