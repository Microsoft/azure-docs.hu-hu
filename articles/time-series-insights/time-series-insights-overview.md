---
title: 'Áttekintés: Mi az Azure Time Series Insights? – Azure Time Series Insights | Microsoft Docs'
description: Bevezetés az idősorozat-adatok elemzéséhez és IoT-megoldásokhoz készült új Azure Time Series Insights szolgáltatás használatába.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: overview
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: cebe0e018b4483a9de1771fb3171effbbd5ca59c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95020061"
---
# <a name="what-is-azure-time-series-insights-gen1"></a>Mi az Azure Time Series Insights Gen1?

> [!CAUTION]
> Ez egy Gen1-cikk.

Azure Time Series Insights a nagy mennyiségű idősoros adatsorozat-információ tárolására, megjelenítésére és lekérdezésére szolgál, például a IoT-eszközök által generált. Ha a felhőben szeretné tárolni, felügyelni, lekérdezni vagy megjeleníteni a Time Series-alapú adatsorozatokat, Azure Time Series Insights valószínűleg az Ön számára megfelelő.

[![Azure Time Series Insights folyamatábrája](media/overview/time-series-insights-flowchart.png)](media/overview/time-series-insights-flowchart.png#lightbox)

A Azure Time Series Insights négy fő feladatot tartalmaz:

- A Felhőbeli átjárók teljes mértékben integrálva vannak, például az Azure IoT Hub és az Azure Event Hubs. Könnyedén csatlakozik ezekhez az eseményforrásokhoz, és elemzi az adatokat egyszerű sorokban és oszlopokban tároló üzenetekből és struktúrákból származó JSON-t. A metaadatokat összeköti a telemetriával, és egy oszlopos tárban indexálja az adatait.
- Azure Time Series Insights kezeli az adatai tárolását. Annak biztosítása érdekében, hogy az adatai mindig könnyen elérhetők legyenek, a memóriában és SSD-ben tárolja az adatait, akár 400 napig. A másodpercek alatt interaktív módon lekérdezheti az események milliárdjait – igény szerint.
- A Azure Time Series Insights a Azure Time Series Insights Explorerben elérhetővé teszi a beépített vizualizációkat.
- A Azure Time Series Insights egy lekérdezési szolgáltatást is biztosít, amely a Azure Time Series Insights Explorerben és a könnyen integrálható API-kkal is használható az idősorozat-adathalmazok egyéni alkalmazásokba való beágyazásához.

Ha belső felhasználásra vagy külső ügyfelek használatára vonatkozó alkalmazást hoz létre, használhatja a Azure Time Series Insightst háttérként. Használhatja az idősorozat-adatokat indexelni, tárolni és összesíteni. Az egyéni vizualizációk és felhasználói élmények létrehozásához használja az [ügyfél-SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)-t. A Azure Time Series Insights több [lekérdezési API](./concepts-json-flattening-escaping-rules.md) -val is rendelkezik a testreszabott forgatókönyvek engedélyezéséhez.

Az idősorozat-adatok mutatják be, hogy hogyan változik az idők során egy adategység vagy folyamat. Az idősorozat-adatsorok az időbélyegzők szerint vannak indexelve, és az idő az a legértelmesebb tengely, amely az ilyen jellegű adatrendezést végzi. Az idősorozat-adatsorok általában szekvenciális sorrendben érkeznek, ezért a rendszer az adatbázis frissítése helyett beszúrásként kezeli.

Nagy mennyiségű idősoros adat tárolására, indexelésére, lekérdezésére, elemzésére és megjelenítésére is kihívást jelenthet.
Azure Time Series Insights rögzíti és tárolja az összes új eseményt sorra, és a változás hatékonyan mérhető az idő múlásával. Ennek eredményeképpen visszafelé is megtekintheti a múltbeli eredményeket a jövőbeli változások előrejelzéséhez.

## <a name="video"></a>Videó

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>További információ a felhőalapú IoT elemzési platformról: Azure Time Series Insights.</br>

[![VIDEÓINAK](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Elsődleges forgatókönyvek

- Az idősorozat-adathalmazokat méretezhető módon tárolja.

   A Azure Time Series Insights a legfontosabb, hogy az idősorozat-adatsorokkal rendelkező adatbázisra van tervezve. Mivel méretezhető és teljes mértékben felügyelt, Azure Time Series Insights kezeli az események tárolásának és kezelésének munkáját.

- A közel valós időben tárja fel az adatelemzést.

   A Azure Time Series Insights egy olyan tallózót biztosít, amely megjeleníti a környezetbe továbbított összes adatforgalmat. Röviddel azután, hogy kapcsolódott egy eseményforrás, megtekintheti, megtekintheti és lekérdezheti az eseményeket az Azure Time Series Insightson belül. Az adatai segítségével ellenőrizheti, hogy az eszköz az elvárt módon bocsát-e ki adatmennyiséget, valamint hogy a IoT-eszközt az állapot, a termelékenység és az általános hatékonyság érdekében figyeli-e.

- Hajtsa végre a kiváltó okok elemzését és a rendellenességek észlelését.

   Azure Time Series Insights rendelkezik olyan eszközökkel, mint például a mintázatok és perspektívák nézetei, és így több lépésből álló kiváltó okok elemzését végezheti el. A Azure Time Series Insights olyan riasztási szolgáltatásokat is használhat, mint például a Azure Stream Analytics, így a Azure Time Series Insights Explorerben közel valós időben megtekintheti a riasztásokat és az észlelt rendellenességeket.

- Globális áttekintést kaphat az idősoros adatokról, amelyek különböző helyekről streameket biztosítanak a több eszköz vagy a hely összehasonlításához.

   Azure Time Series Insights-környezethez több eseményforrás is csatlakoztatható. Így megtekintheti a több, különböző helyekről származó adatfolyamokat közel valós időben. A felhasználók kihasználhatják ezt a láthatóságot az üzleti vezetőkkel való adatmegosztáshoz. Hatékonyabban dolgozhatnak olyan tartományi szakértőkkel, akik szakértelmet alkalmazhatnak a problémák megoldásához, az ajánlott eljárások alkalmazásához és a tanulás megosztásához.

- Hozzon létre egy Customer-alkalmazást Azure Time Series Insightson.

   A Azure Time Series Insights REST lekérdezési API-kat tesz elérhetővé, amelyekkel idősorozat-adatokkal rendelkező alkalmazásokat hozhat létre.

## <a name="capabilities"></a>Képességek

- **Gyors kezdés**: Azure Time Series Insights nem igényel előzetes adatelőkészítést, így gyorsan csatlakozhat több millió eseményhez az IoT hub vagy az Event hub szolgáltatásban. A kapcsolódást követően megjelenítheti és kezelheti az érzékelők adatait, hogy gyorsan érvényesítse IoT-megoldásait. Kód írása nélkül is dolgozhat az adataival, és nem kell új nyelvet tanulnia. A Azure Time Series Insights egy részletes, szabadszöveges lekérdezési felületet biztosít a speciális felhasználók számára, és a pont-és kattintásos feltárást is lehetővé teszi.

- **Közel valós idejű** adatmennyiség: Azure Time Series Insights naponta több millió szenzoros eseményt képes befogadni, egy perces késéssel. Azure Time Series Insights segít betekintést nyerni az érzékelők adataiba. A trendek és rendellenességek észlelésére, a kiváltó okok elemzésére és a költséges állásidő elkerülésére használható. A valós idejű és a korábbi adatkapcsolatok közötti összefüggések révén a rejtett trendek is megtalálhatók az adathalmazban.

- **Egyéni megoldások készítése**: Azure Time Series Insights-adatait beágyazhatja meglévő alkalmazásaiba. Létrehozhat új egyéni megoldásokat is a Azure Time Series Insights REST API-kkal. Személyre szabott nézeteket hozhat létre, hogy mások is megtekinthessék a felfedezett összefüggéseket.

- **Méretezhetőség**: a Azure Time Series Insights a IoT nagy léptékű támogatására lett tervezve. Napi 1–100 millió bejövő eseményt képes kezelni, és alapértelmezés szerint 31 napig őrzi meg ezeket. Közel valós időben jeleníthet meg és elemezheti az élő adatfolyamokat a korábbi adatelemzések mellett.

## <a name="get-started"></a>Bevezetés

Az első lépésekhez kövesse az alábbi lépéseket.

1. Azure Time Series Insights környezet kiépítése a Azure Portal.
1. Kapcsolódjon egy olyan eseményforrás, mint egy IoT hub vagy egy Event hub.
1. Hivatkozási adatok feltöltése. Ez nem egy kiegészítő szolgáltatás.
1. Tekintse át és jelenítse meg az adatait percek alatt a Azure Time Series Insights Explorerben.

## <a name="explorer"></a>Explorer

A Azure Time Series Insights Explorer használatával megtekintheti, elemezheti és feltárhatja az adataiban mutatkozó trendeket.

![Azure Time Series Insights Explorer](media/overview/time-series-insights-explorer-panel.png)

Megtudhatja, hogyan használhatja a [Azure Time Series Insights Explorert](time-series-insights-explorer.md) , és hogyan rajzolhatja meg az adatokból származó információkat.

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg a Azure Time Series Insights Gen1 [bemutató környezettel](./time-series-quickstart.md).

- További információ a Azure Time Series Insights környezet [megtervezéséről](time-series-insights-environment-planning.md) .