---
title: IoT Edge-eszközökön futó Azure Stream Analytics
description: Hozzon létre Edge-feladatokat a Azure Stream Analyticsban, és telepítse őket Azure IoT Edge rendszert futtató eszközökre.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: c2a062b75caa84a0e3c342ca1ce45ccce12f2bb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012614"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>IoT Edge-eszközökön futó Azure Stream Analytics
 
IoT Edge-eszközökön futó Azure Stream Analytics lehetővé teszi a fejlesztők számára a közel valós idejű analitikai intelligencia üzembe helyezését a IoT-eszközökhöz, hogy az eszköz által generált adatok teljes értékét fel tudják oldani. Az Azure Stream Analytics kis késésre, rugalmasságra, a sávszélesség hatékony használatára és megfelelőségre lett tervezve. A vállalatok üzembe helyezhetik a vezérlési logikát az ipari műveletekhez, és kiegészítik a felhőben végzett Big adatelemzést.

A IoT Edge-eszközökön futó Azure Stream Analytics a [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) -keretrendszerben fut. Miután a feladatot létrehozta Stream Analyticsban, IoT Hub használatával telepítheti és felügyelheti.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

Ez a szakasz a IoT Edge Stream Analytics gyakori forgatókönyveit ismerteti. Az alábbi ábrán az IoT-eszközök és az Azure-felhő közötti adatáramlás látható.

:::image type="content" source="media/stream-analytics-edge/edge-high-level-diagram.png" alt-text="IoT Edge magas szintű diagramja":::

### <a name="low-latency-command-and-control"></a>Kis késleltetésű parancs és vezérlés

A gyártási biztonsági rendszereknek rendkívül alacsony késéssel kell válaszolniuk az operatív adatszolgáltatásokra. A Stream Analytics on IoT Edge esetében közel valós időben elemezheti az érzékelőket, és parancsokat adhat ki, amikor a gép leállítása vagy a riasztások elindítása miatt rendellenességek észlelhetők.

### <a name="limited-connectivity-to-the-cloud"></a>Korlátozott kapcsolódás a felhőhöz

A kritikus fontosságú rendszerek, például a távoli adatbányászati berendezések, a csatlakoztatott hajók vagy a tengeri fúrások esetében az adatelemzést és az adatváltozásokat akkor is el kell végezni, ha a felhőalapú kapcsolat időszakos. A Stream Analytics a folyamatos átviteli logikája a hálózati kapcsolattól függetlenül fut, és kiválaszthatja, hogy a felhőben milyen további feldolgozásra vagy tárolásra legyenek elküldve.

### <a name="limited-bandwidth"></a>Korlátozott sávszélesség

A sugárhajtású vagy csatlakoztatott személygépkocsik által előállított adatok mennyisége olyan nagy lehet, hogy az adatokat szűrni kell, vagy előre fel kell dolgozni a felhőbe való küldés előtt. A Stream Analytics használatával szűrheti vagy összesítheti a felhőbe küldendő adatokat.

### <a name="compliance"></a>Megfelelőség

A szabályozás megfelelősége miatt előfordulhat, hogy bizonyos adatokat helyileg kell névtelenül vagy összesíteni a felhőbe való küldés előtt.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Edge-feladatok a Azure Stream Analyticsban

Stream Analytics Edge-feladatok [Azure IoT Edge eszközökön](../iot-edge/about-iot-edge.md)üzembe helyezett tárolókban futnak. Az Edge-feladatok két részből állnak:

* A feladattípusért felelős Felhőbeli rész: a felhasználók bemeneteket, kimeneteket, lekérdezéseket és egyéb beállításokat határoznak meg, például a felhőben lévő megrendelésen kívüli eseményeket.

* Egy modul, amely a IoT-eszközökön fut. A modul tartalmazza a Stream Analytics motort, és a feladatsort fogadja a felhőből. 

A Stream Analytics az IoT Hub használatával helyezi üzembe az Edge-feladatokat az eszköz (ek) re. További információ: [IoT Edge központi telepítés](../iot-edge/module-deployment-monitoring.md).

:::image type="content" source="media/stream-analytics-edge/stream-analytics-edge-job.png" alt-text="Azure Stream Analytics Edge-feladatok":::

## <a name="edge-job-limitations"></a>Az Edge-feladatok korlátai

A cél a IoT Edge feladatok és a Felhőbeli feladatok közötti paritás. A legtöbb SQL-lekérdezés nyelvi funkciója támogatott mind a Edge, mind a felhőben. Az Edge-feladatok esetében azonban a következő funkciók nem támogatottak:
* Felhasználó által definiált függvények (UDF) a JavaScriptben. Az UDF a C# nyelven érhető el [IoT Edge feladatokhoz](./stream-analytics-edge-csharp-udf.md) (előzetes verzió).
* Felhasználó által definiált összesítések (UDA).
* Azure ML függvények.
* A bemeneti/kimeneti AVRO formátuma. Jelenleg csak a CSV és a JSON támogatott.
* A következő SQL-operátorok:
    * PARTICIONÁLÁS
    * GetMetadataPropertyValue
* Késői érkezési szabályzat

### <a name="runtime-and-hardware-requirements"></a>Futtatókörnyezet és hardverkövetelmények
A Stream Analytics IoT Edge-on való futtatásához olyan eszközökre van szükség, amelyek futtathatnak [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

A Stream Analytics és Azure IoT Edge **Docker** -tárolók használatával biztosítanak egy olyan hordozható megoldást, amely több gazdagépen futó operációs rendszeren (Windows, Linux) fut.

A Stream Analytics on IoT Edge elérhető Windows-és Linux-rendszerképekként, amely x86-64 vagy ARM (Advanced RISC machines) architektúrán fut. 


## <a name="input-and-output"></a>Bemenet és kimenet

Stream Analytics Edge-feladatok beszerezhetik a IoT Edge eszközökön futó más modulok bemeneteit és kimeneteit. A és az adott modulokhoz való kapcsolódáshoz beállíthatja az útválasztási konfigurációt a központi telepítés ideje alatt. További információ a [IoT Edge modul-összeállítás dokumentációjában](../iot-edge/module-composition.md)olvasható.

A bemenetek és kimenetek esetében a CSV és a JSON formátum is támogatott.

A Stream Analytics-feladatban létrehozott minden egyes bemeneti és kimeneti adatfolyamhoz létrejön egy megfelelő végpont az üzembe helyezett modulon. Ezek a végpontok használhatók az üzemelő példány útvonalán.

A támogatott stream típusú bemeneti típusok a következők:
* Peremhálózati hub
* Eseményközpont
* IoT Hub

A támogatott stream-kimeneti típusok a következők:
* Peremhálózati hub
* SQL Database
* Eseményközpont
* Blob Storage/ADLS Gen2

A hivatkozás bemenete támogatja a hivatkozási fájl típusát. Más kimenetek is elérhetők egy felhőalapú feladatokkal. Az Edge-ben futtatott Stream Analytics-feladatok például a kimenetet a peremhálózati hubhoz küldik, amely ezután kimenetet küld a IoT Hubnak. Egy másik, felhőben üzemeltetett Azure Stream Analytics feladatot használhat IoT Hub és kimenetből Power BI vagy más kimeneti típusba való bevitelsel.

## <a name="license-and-third-party-notices"></a>Licencek és harmadik felekkel kapcsolatos közlemények
* [IoT Edge-eszközökön futó Azure stream Analytics licenc](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Harmadik féltől származó értesítés a IoT Edge-eszközökön futó Azure stream Analytics](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Azure Stream Analytics modul képének adatai 

A verzióra vonatkozó információk utolsó frissítése 2020-09-21:

- Kép: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - alaprendszerkép: mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - platform
      - architektúra: amd64
      - operációs rendszer: Linux
 
- Kép: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - alaprendszerkép: mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - platform
      - architektúra: ARM
      - operációs rendszer: Linux
 
- Kép: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - alaprendszerkép: mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - platform
      - architektúra: arm64
      - operációs rendszer: Linux
      
      
## <a name="get-help"></a>Segítség kérése
További segítségért próbálja ki a [Microsoft Q&a Azure stream Analytics vonatkozó kérdés oldalát](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Következő lépések

* [További információ a Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [Stream Analytics IoT Edge oktatóanyag](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Stream Analytics Edge-feladatok fejlesztése a Visual Studio Tools használatával](./stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [CI/CD implementálása a Stream Analytics API-k használatával](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
