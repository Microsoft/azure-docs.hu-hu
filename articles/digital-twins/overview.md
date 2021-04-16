---
title: Mi az az Azure Digital Twins?
titleSuffix: Azure Digital Twins
description: A Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: d03a4865c8db52f74f4130c458fec3028f5b95a4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481667"
---
# <a name="what-is-azure-digital-twins"></a>Mi az az Azure Digital Twins?

**Azure Digital Twins** egy szolgáltatásként szolgáló platform (PaaS) ajánlat, amely a teljes környezetek digitális modelljei alapján teszi lehetővé tudásgráfok létrehozását. Ezek a környezetek például épületek, gyárak, farmok, energiahálózatok, házak, stb. – akár teljes városok is. Ezekkel a digitális modellekkel jobb termékeket, optimalizált műveleteket, alacsonyabb költségeket és áttörést jelentő ügyfélélményeket hozhatnak.

Használja ki a tartományokkal kapcsolatos szakértelmét a Azure Digital Twins, személyre szabott, csatlakoztatott megoldások felépítéséhez, amelyek:
* Bármely környezet modellezése és a digitális ikerkörnyezetek életre hozása skálázható és biztonságos módon
* Eszközök, például IoT-eszközök és meglévő üzleti rendszerek csatlakoztatása
* Robusztus eseményrendszer használata dinamikus üzleti logika és adatfeldolgozás felépítéséhez
* Integrálás Azure-adatokkal, elemzésekkel és AI-szolgáltatásokkal a múlt nyomon követése, majd a jövő előrejelzése érdekében

## <a name="azure-digital-twins-capabilities"></a>Azure Digital Twins képességek

Az Azure Digital Twins.

### <a name="open-modeling-language"></a>Nyílt modellezési nyelv

A Azure Digital Twins a modellek nevű egyéni ikertípusokkal definiálhatja a fizikai környezetben a személyeket, helyeket és dolgokat képviselő digitális [**entitásokat.**](concepts-models.md) 

Ezekre a modelldefiníciókra úgy is gondolhat, mint egy specializált szókincsre az üzlet leírására. Egy épületkezelési megoldáshoz definiálhat például olyan modelleket, mint az "épület", a "padló", és a "lift". Ezután ezen modellek alapján digitális **ikereket** hozhat létre, amelyek az adott környezetet képviselik.

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

A modellek egy [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)nevű JSON-hez hasonló nyelven vannak definiálva, és az ikereket állapottulajdonságokkal, telemetriai eseményekkel, parancsokkal, összetevőkkel és kapcsolatokkal írják le.
* A modellek szemantikai kapcsolatokat **definiálnak** az entitások között, így az ikereket az interakciókat tükröző tudásgráfhoz csatlakoztathatja. A modellekre a világ leírásában főnevekként, a kapcsolatokra pedig igékként gondolhat.
* Az ikereket modellöröklés használatával is specializálhatja. Az egyik modell örökölhet egy másikat.

A DTDL más Azure IoT-szolgáltatások, például az [IoT Plug and Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) és a Time Series Insights [(TSI) adatmodelljéhez használatos.](../time-series-insights/overview-what-is-tsi.md) Ez segít abban, hogy Azure Digital Twins megoldás csatlakoztatva maradjon, és kompatibilis legyen az Azure-ökoszisztéma más részeivel.

### <a name="live-execution-environment"></a>Élő végrehajtási környezet

A digitális Azure Digital Twins valós világ élő, naprakész ábrázolásai. Az egyéni DTDL-modellekben található kapcsolatokat használva az ikereket **egy** élő gráfhoz csatlakoztatja, amely az Ön környezetét ábrázolja.

A diagram vizualizációit egy mintaalkalmazás, a Azure Digital Twins explorer segítségével [**Azure Digital Twins meg.**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)

A mintavizualizáció a következő képi megjelenítést ábrázolja:

:::image type="content" source="media/includes/azure-digital-twins-explorer.png" alt-text="Képernyőkép a Azure Digital Twins Explorer mintaalkalmazásról, amely a digitális ikereket jelképezó csomópontok grafikonján látható" lightbox="media/includes/azure-digital-twins-explorer.png":::

Azure Digital Twins gazdag eseményrendszert **biztosít,** amely biztosítja, hogy a gráf mindig aktuális legyen az adatfeldolgozással és az üzleti logikával. Az adatfeldolgozás rugalmas, testreszabott [](../azure-functions/functions-overview.md)módon történő kezeléséhez csatlakoztathat külső számítási erőforrásokat, például Azure Functions a Azure Functions.

Az élő végrehajtási környezetből elemzéseket is kinyerhet a Azure Digital Twins lekérdezési **API-jának használatával.** Az API-val részletes keresési feltételekkel, többek között tulajdonságértékekkel, kapcsolati tulajdonságokkal, modellinformációkkal és sok más funkcióval is lekérdezheti a lekérdezéseket. A lekérdezéseket kombinálhatja is, számos információt gyűjthet a környezetről, és megválaszolhatja az Ön számára fontos egyéni kérdéseket.

### <a name="input-from-iot-and-business-systems"></a>Az IoT és az üzleti rendszerek bemenete

Annak érdekében, hogy a Azure Digital Twins a valós világgal naprakész maradjon, a [IoT Hub](../iot-hub/about-iot-hub.md) segítségével csatlakoztathatja a megoldást az IoT-hez és IoT Edge eszközökhöz. Ezek a központ által felügyelt eszközök az ikergráf részeként biztosítanak adatokat a modellhez.

Létrehozhat egy új IoT Hub erre a célra a Azure Digital Twins, vagy összekapcsolhat egy meglévő IoT Hub azokkal az eszközökkel, amelyek már felügyelve vannak.

A rest Azure Digital Twins más adatforrásból származó adatokat is csatlakoztathat REST API-k vagy összekötők használatával más szolgáltatásokhoz, például a [Logic Apps.](../logic-apps/logic-apps-overview.md)

### <a name="output-to-tsi-storage-and-analytics"></a>Kimenet a TSI-be, a tárolásba és az elemzésbe

Az adatmodellben Azure Digital Twins az adatokat az lefelé irányuló Azure-szolgáltatásokhoz irányíthatja további elemzések vagy tárolás céljából. Ez a [](../service-bus-messaging/service-bus-messaging-overview.md) eseményútvonalakon [](../event-hubs/event-hubs-about.md)keresztül **érhető** el, amelyek a kívánt adatfolyamok Event Grid , [Service Bus](../event-grid/overview.md)vagy Service Bus használják.

Néhány dolog, amit az eseményútvonalak esetében lehet megtenni:
* Az Azure Digital Twins-adatok egy [Azure Data Lake-ben](../storage/blobs/data-lake-storage-introduction.md) való tárolása
* Azure Digital Twins-adatok elemzése az [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) vagy más Microsoftos adatelemzési eszközzel
* Nagyobb munkafolyamatok integrálása a Logic Appsbe
* Csatlakozás Azure Digital Twins és Time Series Insights az egyes ikeridősor-előzmények nyomon követéséhez
* Time Series Insightsbeli idősoros modell igazítása egy Azure Digital Twins-forráshoz

Ez egy másik módja annak, Azure Digital Twins egy nagyobb megoldáshoz kapcsolódhat, és támogathatja az egyéni igényeket az elemzésekkel való folyamatos munkához.

## <a name="azure-digital-twins-in-a-solution-context"></a>Azure Digital Twins megoldáskörnyezetben

Azure Digital Twins gyakran használják más Azure-szolgáltatásokkal együtt egy nagyobb IoT-megoldás részeként. 

A teljes megoldás a Azure Digital Twins a következő részeket tartalmazhatja:
* A Azure Digital Twins szolgáltatáspéldány. Ez tárolja az ikermodelleket és az ikergráfot az állapotával együtt, és összehangolja az eseményfeldolgozást.
* Egy vagy több ügyfélalkalmazás, amely a Azure Digital Twins a modellek konfigurálásával, a topológia létrehozásával és az ikergráfból kinyert elemzésekkel.
* Egy vagy több külső számítási erőforrás az adatforrások vagy Azure Digital Twins adatforrások, például eszközök által létrehozott események feldolgozásához. A számítási erőforrások biztosítanak egyik gyakori módja a [Azure Functions.](../azure-functions/functions-overview.md)
* Egy IoT Hub, amely eszközkezelési és IoT-adatfolyam-képességeket biztosít.
* Lefelé irányuló szolgáltatások, amelyek olyan feladatokat kezelnek, mint a [munkafolyamat-integráció](../logic-apps/logic-apps-overview.md)(például Logic Apps, a hideg tárolás, az idősorozat-integráció vagy az elemzés).

Az alábbi ábra bemutatja, Azure Digital Twins hol található egy nagyobb Azure IoT-megoldás kontextusában.

:::image type="content" source="media/overview/solution-context.png" alt-text="Diagram a bemeneti forrásokról, a kimeneti szolgáltatásokról, valamint az ügyfélalkalmazásokkal és a külső számítási erőforrásokkal való kétútos kommunikációról." border="false" lightbox="media/overview/solution-context.png":::

## <a name="service-limits"></a>Szolgáltatási korlátozások

A szolgáltatási korlátokról a következő **Azure Digital Twins** olvashat: [Azure Digital Twins szolgáltatáskorlátok.](reference-service-limits.md) Ez hasznos lehet, amikor a szolgáltatással dolgozik, hogy megértse a szolgáltatás működési és sebességkorlátozását, valamint azt, hogy szükség esetén mely korlátok módosíthatók.

## <a name="terminology"></a>Terminológia

A gyakori **IoT-kifejezések** és azok azure-beli IoT-szolgáltatásokban való használatának listáját ( beleértve a Azure Digital Twins is) itt található: [Azure IoT szószedet.](../iot-fundamentals/iot-glossary.md?toc=/azure/digital-twins/toc.json&bc=/azure/digital-twins/breadcrumb/toc.json) Ez hasznos referencia lehet az IoT-megoldások Azure Digital Twins első lépésekhez.

## <a name="next-steps"></a>Következő lépések

* Ismerkedés a Azure Digital Twins: Rövid [*útmutató: Mintaforgatókönyv megismerve.*](quickstart-azure-digital-twins-explorer.md)

* Vagy kezdjen el olvasni a Azure Digital Twins alapfogalmairól [*az Alapfogalmak: Egyéni modellek .*](concepts-models.md)