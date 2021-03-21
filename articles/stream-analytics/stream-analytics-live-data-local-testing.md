---
title: Élő adatellenőrzés Azure Stream Analytics a Visual Studióval
description: Megtudhatja, hogyan tesztelheti az Azure Stream Analytics-feladatot helyileg élő adatfolyam-adatok használatával.
author: ajetasin
ms.author: ajetasi
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 99e9ce85bb1e59b5016e836ff16d03cb3326ec8c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020366"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Élő adatellenőrzés helyi tesztelése a Visual Studio Azure Stream Analytics eszközeivel (előzetes verzió)

A Azure Stream Analytics Tools for Visual Studio lehetővé teszi, hogy az Azure Event hub, a IoT Hub és a Blob Storage élő esemény-adatfolyamai segítségével helyileg tesztelje az IDE-feladatokat. Az élő adatok helyi tesztelése nem helyettesítheti a felhőben elvégezhető [teljesítmény-és méretezhetőségi teszteket](stream-analytics-streaming-unit-consumption.md) , de időt takaríthat meg a működés közbeni tesztelés során, mivel nem kell minden alkalommal elküldeni a felhőbe, amikor tesztelni szeretné a stream Analytics feladatot. Ez a funkció előzetes verzióban érhető el, és nem használható éles számítási feladatokhoz.

## <a name="testing-options"></a>Tesztelési beállítások

A következő helyi tesztelési lehetőségek támogatottak:

|**Bevitel**  |**Kimenet**  |**Feladattípus**  |
|---------|---------|---------|
|Helyi statikus adattartalom   |  Helyi statikus adattartalom   |   Felhő/Edge |
|Élő bemeneti adatok   |  Helyi statikus adattartalom   |   Felhőbeli |
|Élő bemeneti adatok   |  Élő kimeneti adatmennyiség   |   Felhőbeli |

## <a name="local-testing-with-live-data"></a>Helyi tesztelés élő adatértékekkel

1. Miután létrehozott egy [Azure stream Analytics felhőalapú projektet a Visual Studióban](stream-analytics-quick-create-vs.md), nyissa meg a **script. asaql**. A helyi tesztelés alapértelmezés szerint helyi bemenetet és helyi kimenetet használ.

   ![A Visual Studio helyi bemenetének és helyi kimenetének Azure Stream Analytics](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Az élő adatok teszteléséhez válassza a **Felhőbeli bevitel használata** lehetőséget a legördülő listából.

   ![Azure Stream Analytics Visual Studio Live Cloud Input](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)

3. Állítsa be a **kezdési időpontot** annak meghatározásához, hogy a rendszer mikor kezdi meg a bemeneti adatok feldolgozását. Előfordulhat, hogy a feladatnak idő előtt kell beolvasnia a bemeneti adatokat a pontos eredmények biztosítása érdekében. Az alapértelmezett idő 30 perccel az aktuális idő előtt van beállítva.

   ![A Visual Studio élő adatkezdési idejének Azure Stream Analytics](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Kattintson a **helyi Futtatás** gombra. Ekkor megjelenik egy konzolablak a futó folyamattal és a feladatok mérőszámával. Ha le szeretné állítani a folyamatot, manuálisan is megteheti. 

   ![Azure Stream Analytics Visual Studio élő adatfolyamati ablak](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   A kimeneti eredmények három másodpercenként frissülnek a helyi Futtatás eredménye ablak első 500 kimeneti sorával, és a kimeneti fájlok a projekt elérési útja **ASALocalRun** mappába kerülnek. A kimeneti fájlokat úgy is megnyithatja, hogy az **eredmények mappa megnyitása** gombra kattint a helyi Futtatás eredménye ablakban.

   ![Azure Stream Analytics Visual Studio Live-adatmegnyitási eredmények mappája](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Ha az eredményeket a Felhőbeli kimeneti adatnyelők számára szeretné kiadni, válassza a **kimenet a felhőbe** lehetőséget a második legördülő listából. A Power BI és a Azure Data Lake Storage nem támogatott kimeneti mosogatók.

   ![A Visual Studio élő adatkimenetének Azure Stream Analytics a felhőbe](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Korlátozások

* A hitelesítési modell korlátozásai miatt a Power BI és a Azure Data Lake Storage nem támogatott kimeneti mosogatók.

* Csak a Felhőbeli beviteli lehetőségek támogatják az [időszabályzatokat](./stream-analytics-time-handling.md) , míg a helyi beviteli beállítások nem.

## <a name="next-steps"></a>Következő lépések

* [Stream Analytics-feladatok létrehozása a Visual studióhoz készült Azure Stream Analytics Tools használatával](stream-analytics-quick-create-vs.md)
* [A Visual Studióval helyileg tesztelheti Stream Analytics lekérdezéseit](stream-analytics-vs-tools-local-run.md)
* [A Visual Studio használata Azure Stream Analytics feladatok megtekintéséhez](stream-analytics-vs-tools.md)