---
title: Gen2 használati esetek – Azure Time Series Insights Gen2 | Microsoft Docs
description: Ismerkedjen meg Azure Time Series Insights Gen2 használati eseteivel.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: seodec18
ms.openlocfilehash: 7d97958c5fd1274495da88c064b63e59e354f691
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606963"
---
# <a name="azure-time-series-insights-gen2-use-cases"></a>Azure Time Series Insights Gen2 használati esetei

Ez a cikk számos gyakori használati esetet foglal össze Azure Time Series Insights Gen2. A cikkben szereplő javaslatok kiindulási pontként szolgálnak az alkalmazások és megoldások Azure Time Series Insights Gen2 való fejlesztéséhez.

Ez a cikk a következő kérdésekre ad választ:

* Milyen gyakori használati esetek Azure Time Series Insights Gen2?
* Milyen előnyökkel jár a Azure Time Series Insights Gen2 használata az [adatfeltárási és a vizualizációs rendellenességek észleléséhez](#data-exploration-and-visual-anomaly-detection)?
* Milyen előnyökkel jár a Azure Time Series Insights Gen2 használata az [operatív elemzéshez és a folyamatok hatékonyságához](#operational-analysis-and-driving-process-efficiency)?
* Milyen előnyökkel jár a [speciális elemzési](#advanced-analytics)Azure Time Series Insights Gen2 használata?

A következő szakaszokban ismertetjük a használati forgatókönyvek áttekintését.

## <a name="introduction"></a>Bevezetés

Azure Time Series Insights Gen2 egy végpontok közötti, szolgáltatásként nyújtott platformot kínál. A szolgáltatás a kifejezetten környezetfüggő, idősorozat-optimalizált IoT adatok gyűjtésére, feldolgozására, tárolására, elemzésére és lekérdezésére szolgál. Ideális megoldás az ad hoc adatfeltárásra és az operatív elemzésre. Azure Time Series Insights Gen2 egy egyedileg bővíthető, testreszabott szolgáltatás, amely megfelel az ipari IoT-telepítések széles körű igényeinek.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Adatelemzési és vizualizációs anomáliák észlelése

Azonnal feltárhatja és elemezheti az észlelt rendellenességeket, és felderítheti az adataiban lévő rejtett trendeket. Azure Time Series Insights Gen2 közel valós idejű teljesítményt nyújt a IoT és a DevOps-elemzési számítási feladatokhoz.

[![Adatkezelő](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

A legtöbb ügyfél egyetért azzal, hogy a betekintéshez szükséges minimális idő az Azure Time Series Insights Gen2 egyik kiemelkedő funkciója:

* Azure Time Series Insights az Gen2 nem igényel előzetes adatelőkészítést.
* Percek alatt összekapcsolhatja az Azure-IoT Hub vagy az Azure Event Hubs-példányok több milliárd eseményét.
* A csatlakozást követően több milliárd eseményt jeleníthet meg és elemezheti a rendellenességek észlelése és az adatai rejtett trendjeinek felderítése érdekében.

Azure Time Series Insights Gen2 intuitív és könnyen használható. Az adataival egyetlen sor kód írása nélkül is dolgozhat. Nem szükséges új nyelvet megtanulnia, bár a Azure Time Series Insights Gen2 részletes szöveges lekérdezési nyelvet biztosít az SQL-t ismerő haladó felhasználók számára. Emellett a kezdők számára kiválasztható és kattintásos feltárást is biztosít.

Az ügyfelek gyorsan kihasználhatják az eszközökhöz kapcsolódó problémák gyors diagnosztizálásához szükséges előnyöket. DevOps-elemzést végezhetnek a IoT-megoldásban fellépő hibák kiváltó okának eléréséhez. Emellett az adatelemzési kezdeményezéseik részeként is meghatározhatják a további vizsgálathoz szükséges területeket.

A Azure Time Series Insights Gen2 tárolt adatkezelés három fő módon használható:

* Első és legegyszerűbb módja a Azure Time Series Insights Gen2 Explorer. A segítségével gyorsan megjelenítheti az összes IoT-adatait egy helyen. Olyan eszközöket biztosít, mint például a hő-Térkép, amely segít az adatrendellenességek kiépítésében. Emellett perspektivikus nézetet is biztosít. Ezzel a beállítással akár négy nézetet is összehasonlíthat egy vagy több Azure Time Series Insights Gen2-környezetből egyetlen irányítópulton. Az irányítópulton megtekintheti az idősoros adatait az összes helyen. További információ a [Azure Time Series Insights Gen2 Explorer böngészőről](./concepts-ux-panels.md). A környezet megtervezéséhez olvassa el [Azure Time Series Insights Gen2 tervezését](./how-to-plan-your-environment.md).

* A második módszer a JavaScript SDK használata a hatékony diagramok és diagramok gyors beágyazásához a webalkalmazásban. A kód mindössze néhány sornyi kódot használhat a hatékony lekérdezések létrehozásához. A táblázatok, a tortadiagramok, a sávdiagramok, a Heat Maps és az adatrácsok feltöltésére használhatók. Ezen elemek mindegyike az SDK használatával létezik. Az SDK absztrakt Azure Time Series Insights Gen2 lekérdezési API-kat is. Az SQL-hez hasonló predikátumok létrehozásához használhatja őket az irányítópulton megjeleníteni kívánt adatlekérdezéshez. A hibrid megjelenítési rétegbeli megoldások esetében Azure Time Series Insights Gen2 paraméteres URL-címeket kínál. Zökkenőmentes csatlakozási pontokat biztosítanak a Azure Time Series Insights Gen2 Explorer segítségével, amelyek részletesen megtekinthetik az adatvesztést.

  * A JavaScript SDK-val kapcsolatos további információért olvassa el a [js ügyféloldali kódtár](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) és a [minta ügyfél](https://github.com/Microsoft/tsiclient) dokumentációját.

  * További információ az URL-címek és az új felhasználói felület megosztásáról: [az Azure Time Series Insights Gen2 Explorer adatainak megjelenítése](./concepts-ux-panels.md).

* A harmadik módszer a hatékony API-k használata Azure Time Series Insights Gen2 tárolt adatlekérdezéshez. Azure Time Series Insights Gen2 olyan ideiglenes operátorokkal rendelkezik, mint a `from` ,, `to` `first` és `last` . Olyan összesítésekkel és átalakításokkal rendelkezik, mint a,,,, `average` `sum` `min` `max` `time-weighted average` `time-weighted sum` stb. Emellett lehetővé teszi a szűrést, a aritmetikai és a logikai operátorok, a skaláris függvények stb. Ezek az operátorok lehetővé teszik, hogy az alárendelt alkalmazások gyorsan megtalálják az adataikban érdekes trendeket és mintákat. Felhasználhatja a betöltött vizualizációk kitöltését a rendellenességek észlelésére.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Operatív elemzés és a vezetési folyamat hatékonysága

Használja Azure Time Series Insights Gen2 a berendezések állapotának, használatának és teljesítményének figyelésére, és mérje fel a működési hatékonyságot. Azure Time Series Insights a Gen2 a terhelés vagy a lekérdezési teljesítmény feláldozása nélkül segíti a különböző és kiszámíthatatlan IoT számítási feladatok kezelését.

[![Képernyőfelvétel: I o T-eszközök/alkalmazásadatok, adatfolyam-feldolgozás, működési hatékonyság, intelligencia/elemzések és fejlett Analitika Azure Time Series Insights Gen2.](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

A működési folyamatokból érkező adatok folyamatos átvitele és folyamatos feldolgozása a megfelelő technológiával vagy megoldással együtt sikeresen átalakíthatja a vállalatokat. Ezek a megoldások gyakran több rendszer kombinációját jelentik. Lehetővé teszik az állandóan módosult, különösen az IoT-tartományon belüli adatelemzést és elemzést, és megosztanak egy közös mintát.

Ezek a minták gyakran olyan IoT-kompatibilis platformokkal kezdődnek, amelyek több milliárd eseményt is betöltenek az eszközöktől és érzékelőktől, amelyek különböző területi beállításokra terjednek ki. Ezek a rendszerek a valós idejű elemzések és műveletek elvégzése érdekében dolgozzák fel és elemzik a streaming-adatforrásokat. Az adatok általában a meleg és a hideg tárolóba kerülnek, közel valós időben és kötegelt elemzésekhez.

Az összegyűjtött adatok több feldolgozási folyamaton keresztül megtisztíthatók és contextualize az alsóbb rétegbeli lekérdezési és elemzési forgatókönyvek esetében. Az Azure gazdag szolgáltatásokat kínál, amelyek alkalmazhatók olyan IoT-forgatókönyvek esetén, mint az eszközök karbantartása és a gyártás. Ezek a szolgáltatások a következők: Azure Time Series Insights Gen2, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning és Power BI.

A megoldás architektúrája a következő módon érhető el:

* Adatok betöltése IoT Hub vagy Event Hubs használatával a legjobb biztonsági, átviteli sebesség és késés érdekében.
* Adatfeldolgozás és-számítások végrehajtása. A betöltött adatcsatornákat olyan szolgáltatásokon keresztül, mint például a Stream Analytics, a Logic Apps és a Azure Functions. Az Ön által használt szolgáltatás a konkrét adatfeldolgozási igényektől függ.
* A feldolgozási folyamatból származó számított jeleket a rendszer leküldi Azure Time Series Insights Gen2 tárolására és elemzésére.

A Azure Time Series Insights Gen2 közel valós idejű adatfeltárást és adatelemzést kínál a múltbeli adatokon keresztül. Az üzleti igényektől függően a MapReduce és a kaptár feladatok a Azure Time Series Insights Gen2 tárolt adatokon futtathatók Azure Time Series Insights Gen2 az Azure HDInsight való csatlakoztatásával. A Azure Time Series Insights Gen2 tárolt adatkészletek Power BI és más ügyfélalkalmazások számára is elérhetők a Azure Time Series Insights Gen2 nyilvános felületű lekérdezési API-kon keresztül. Ezek az adatok a Deep Business és az operatív intelligenciával kapcsolatos forgatókönyvekhez használhatók.

## <a name="advanced-analytics"></a>Bővített analitika

Integrálja a fejlett elemzési szolgáltatásokkal, például a Machine Learning és a Azure Databricksokkal. Azure Time Series Insights Gen2 több millió eszközről ingresses a nyers adatok mennyiségét. Olyan környezetfüggő adatok hozzáadására szolgál, amelyeket az Azure Analytics-szolgáltatások egy csomagja zökkenőmentesen felhasználhat.

[![Elemzés](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

A fejlett elemzési és gépi tanulási szolgáltatás nagy mennyiségű adatmennyiséget használ fel és dolgoz fel. Ezek az adatok az adatvezérelt döntések végrehajtásához és a prediktív elemzések elvégzéséhez használatosak. A IoT-használati esetekben a fejlett elemzési algoritmusok a több millió eszközről gyűjtött adatokból tanulnak. Ezek az eszközök többször továbbítanak adatokat másodpercenként. A IoT-eszközökről gyűjtött adatok nyersek. Nem rendelkezik a kontextussal kapcsolatos információkkal, például az eszköz helyével és az érzékelő olvasásának egységével. Ennek eredményeképpen a nyers adatfeldolgozást nehéz közvetlenül a speciális elemzésekhez használni.

Azure Time Series Insights Gen2 két egyszerű és költséghatékony módszert kínál a IoT-és a speciális elemzési szolgáltatás közötti szakadék áthidalása érdekében:

* Először is, Azure Time Series Insights a Gen2 IoT Hub használatával gyűjti a nyers telemetria adatokat több millió eszközről. A szolgáltatás a környezetfüggő adatokkal gazdagítja az adatokat, és egy parketta formátumba alakítja át az adatokat. Ez a formátum egyszerűen integrálható más fejlett analitikai szolgáltatásokkal, például Machine Learningokkal, Azure Databricksekkel és külső gyártók alkalmazásaival.

    Azure Time Series Insights Gen2 az igazság forrásaként szolgálhat a szervezeten belüli összes adathoz. Létrehoz egy központi tárházat az alsóbb rétegbeli elemzési számítási feladatok felhasználásához. Mivel Azure Time Series Insights Gen2 a közel valós idejű tárolási szolgáltatás, a fejlett elemzési modellek folyamatosan tanulhatnak a bejövő IoT telemetria-adatokból. Ennek eredményeképpen a modellek pontosabb előrejelzéseket készíthetnek.

* Másodszor, a gépi tanulási és előrejelzési modellek kimenete Azure Time Series Insights Gen2 az eredmények megjelenítéséhez és tárolásához. Ez az eljárás segít a szervezeteknek a modelljeik optimalizálásában és finomhangolásában. Azure Time Series Insights Gen2 megkönnyíti a folyamatos átviteli telemetria-adatokat a betanított modell kimenetével megegyező síkban. Így segít az adatelemzési csapatoknak észrevenni a rendellenességeket és azonosítani a mintákat.

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure Time Series Insights Gen2 Explorer böngészőről](./concepts-ux-panels.md).
* A környezet megtervezéséhez olvassa el [Azure Time Series Insights Gen2 ajánlott eljárásokat](./how-to-plan-your-environment.md) .
* Olvassa el a [minta ügyfél](https://github.com/Microsoft/tsiclient) dokumentációját.
