---
title: Mi az az Azure Digital Twins?
titleSuffix: Azure Digital Twins
description: Az Azure digitális Twins szolgáltatással végzett teendők áttekintése.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: 315519472baa672e802855b5ab21b8bae8f51935
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109849"
---
# <a name="what-is-azure-digital-twins"></a>Mi az az Azure Digital Twins?

Az **Azure Digital Twins** egy szolgáltatásként nyújtott platform, amely lehetővé teszi, hogy a teljes környezetek digitális modelljein alapuló ismereti diagramokat hozzanak létre. Ezek a környezetek lehetnek olyan épületek, gyárak, gazdaságok, energiahálózatok, vasutak, stadionok és sok más – akár a teljes város is. Ezek a digitális modellek olyan elemzések megszerzésére használhatók, amelyek jobb termékeket, optimalizált műveleteket, csökkentett költségeket és áttörést jelentő felhasználói élményt nyújtanak.

Kihasználhatja az Azure digitális Ikrekre épülő tartományi szaktudását, így testre szabott, csatlakoztatott megoldásokat hozhat létre:
* Bármilyen környezet modellezése és a digitális ikrek életre hozása méretezhető és biztonságos módon
* Eszközök, például IoT-eszközök és meglévő üzleti rendszerek csatlakoztatása
* Robusztus eseményrendszer használatával dinamikus üzleti logikát és adatfeldolgozást hozhat létre
* Integrálhatja az Azure-beli adatelemzési és AI-szolgáltatásokkal, hogy könnyebben nyomon követhesse a múltban, majd előre megjósolja a jövőt

## <a name="azure-digital-twins-capabilities"></a>Azure digitális Twins-képességek

Íme egy összefoglaló az Azure Digital Twins szolgáltatásairól.

### <a name="open-modeling-language"></a>Modellezési nyelv megnyitása

Az Azure Digital Twins szolgáltatásban definiálhatja azokat a digitális entitásokat, amelyek a fizikai környezetben lévő személyeket, helyeket és dolgokat képviselik egyéni, [**modellek**](concepts-models.md)nevű különálló típusok használatával. 

Ezeket a modell-definíciókat speciális szókincsként tekintheti meg a vállalata leírásához. Egy épületfelügyeleti megoldáshoz például meghatározhat olyan modelleket, mint például az "épület", a "Floor" és a "lift". Ezután létrehozhat **digitális ikreket** ezen modellek alapján, hogy az adott környezetet képviselje.

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

A modellek a [digitális Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)nevű JSON-szerű nyelven vannak meghatározva, és leírják az ikreket az állapotuk tulajdonságai, a telemetria-események, a parancsok, az összetevők és a kapcsolatok tekintetében.
* A modellek szemantikai **kapcsolatokat** határoznak meg az entitások között, így összekapcsolhatják az ikreket egy, az interakciókat tükröző Tudásbázisban. A modelleket a világ egy leírásában, illetve a kapcsolatok műveletként is megtekintheti a főkönyvek formájában.
* Az ikreket a modell öröklődésének használatával is kioszthatja. Az egyik modell örökölheti a másikat.

A DTDL a többi Azure IoT-szolgáltatás (például a [IoT Plug and Play (PNP)](../iot-pnp/overview-iot-plug-and-play.md) és a [Time Series Insights (ÁME)](../time-series-insights/overview-what-is-tsi.md)adatmodelljeihez használatos. Ez segít megőrizni az Azure digitális Twins-megoldását, és kompatibilis az Azure ökoszisztéma más részeivel.

### <a name="live-execution-environment"></a>Élő végrehajtási környezet

Az Azure Digital Twins digitális modelljei élő, naprakész képviseletek a valós világban. Az egyéni DTDL-modellek kapcsolatainak használatával összekapcsolhatók az ikrek egy **élő gráfban** , amely a környezetet jelképezi.

Megtekintheti az Azure Digital Twins gráf vizualizációját egy minta alkalmazás, az [**Azure digitális Twins Explorer**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)segítségével.

A minta vizualizáció a következőképpen néz ki:

:::image type="content" source="media/includes/azure-digital-twins-explorer.png" alt-text="Képernyőkép az Azure Digital Twins Explorer alkalmazásról, amely a digitális ikreket jelképező csomópontok gráfját mutatja" lightbox="media/includes/azure-digital-twins-explorer.png":::

Az Azure Digital Twins gazdag **eseményt** biztosít a gráfnak az adatfeldolgozással és az üzleti logikával való folyamatos megőrzéséhez. A külső számítási erőforrások, például a [Azure functions](../azure-functions/functions-overview.md)összekapcsolhatók az adatfeldolgozás rugalmas, testre szabott módon történő elvégzéséhez.

Az Azure Digital Twins hatékony **lekérdezési API**-ját használva az élő végrehajtási környezetből is kinyerheti az eredményeket. Az API lehetővé teszi a részletes keresési feltételekkel való lekérdezést, beleértve a tulajdonságértékek, a kapcsolatok, a kapcsolati tulajdonságok, a modell információit és egyebeket. A lekérdezéseket kombinálhatja a környezettel kapcsolatos információk széles körének összegyűjtésével, valamint az Ön számára fontos egyéni kérdések megválaszolásával is.

### <a name="input-from-iot-and-business-systems"></a>A IoT és az üzleti rendszerek bemenetei

Az Azure Digital ikrek élő végrehajtási környezetének a valós világgal való naprakészen tartásához a [IoT hub](../iot-hub/about-iot-hub.md) segítségével csatlakozhat a megoldáshoz a IoT és a IoT Edge eszközökhöz. Ezek a hub által felügyelt eszközök a Twin gráf részeként jelennek meg, és biztosítják a modellt kezelő adatmennyiséget.

Új IoT Hub hozhat létre erre a célra az Azure digitális Twins szolgáltatással, vagy egy meglévő IoT Hubt is összekapcsolhat a már felügyelt eszközökkel.

Az Azure digitális Twins más adatforrásokból is átadható, REST API-kkal vagy összekötővel más szolgáltatásokhoz, például a [Logic Appshoz](../logic-apps/logic-apps-overview.md).

### <a name="output-to-tsi-storage-and-analytics"></a>Az ÁME, a Storage és az Analytics kimenete

Az Azure Digital Twins-modellben lévő információk további elemzésekhez vagy tároláshoz is átirányíthatók az alárendelt Azure-szolgáltatásokhoz. Ezt olyan esemény- **útvonalakon** keresztül biztosítjuk, amelyek az [Event Hub](../event-hubs/event-hubs-about.md), a [Event Grid](../event-grid/overview.md)vagy a [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) használatával irányítják a kívánt adatfolyamatokat.

Az esemény-útvonalakkal többek között az alábbiakat teheti:
* Az Azure Digital Twins-adatok egy [Azure Data Lake-ben](../storage/blobs/data-lake-storage-introduction.md) való tárolása
* Azure Digital Twins-adatok elemzése az [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) vagy más Microsoftos adatelemzési eszközzel
* Nagyobb munkafolyamatok integrálása a Logic Appsbe
* Az Azure Digital Twins csatlakoztatása Time Series Insights az egyes Twins idősorozatok előzményeinek nyomon követése
* Time Series Insightsbeli idősoros modell igazítása egy Azure Digital Twins-forráshoz

Ez egy másik módja annak, hogy az Azure Digital Twins egy nagyobb megoldáshoz kapcsolódjon, és támogatja az egyéni igényeket, hogy továbbra is működjenek ezekkel az ismeretekkel.

## <a name="azure-digital-twins-in-a-solution-context"></a>Azure digitális Twins megoldási kontextusban

Az Azure digitális Twins általában más Azure-szolgáltatásokkal együtt, egy nagyobb IoT-megoldás részeként használatos. 

Az Azure Digital Twins használatával végzett teljes megoldás a következő részeket tartalmazza:
* Az Azure Digital Twins szolgáltatás példánya. Ez a két modellt és a Twin gráfot az állapotával együtt tárolja, és összehangolja az események feldolgozását.
* Egy vagy több ügyfélalkalmazás, amely a modellek konfigurálásával, a topológia létrehozásával, valamint a Twin gráfból való kinyeréssel hajtja végre az Azure digitális Twins-példányokat.
* Egy vagy több külső számítási erőforrás az Azure digitális ikrek által generált események, illetve a csatlakoztatott adatforrások (például eszközök) feldolgozásához. A számítási erőforrások megadásának egyik gyakori módja [Azure Functionson](../azure-functions/functions-overview.md)keresztül.
* Egy IoT hub, amely az eszközkezelés és a IoT adatfolyam-képességek biztosítására szolgál.
* Alárendelt szolgáltatások olyan feladatok kezelésére, mint például a munkafolyamat-integráció (például [Logic apps](../logic-apps/logic-apps-overview.md), a hűtőházi tárolás, az idősorozat-integráció vagy az elemzés).

Az alábbi ábra azt mutatja be, hogy az Azure Digital Twins egy nagyobb Azure IoT-megoldás kontextusában rejlik.

:::image type="content" source="media/overview/solution-context.png" alt-text="A bemeneti forrásokat, a kimeneti szolgáltatásokat és az ügyfélalkalmazások és a külső számítási erőforrások közötti kétirányú kommunikációt bemutató ábra." border="false" lightbox="media/overview/solution-context.png":::

## <a name="service-limits"></a>Szolgáltatási korlátozások

Az Azure Digital Twins **szolgáltatási korlátairól** itt olvashat: [Azure Digital Twins szolgáltatás korlátai](reference-service-limits.md). Ez akkor lehet hasznos, ha a szolgáltatással együttműködve megérti a szolgáltatás működési és díjszabási korlátozásait, valamint azt, hogy milyen korlátokat lehet módosítani, ha szükséges.

## <a name="terminology"></a>Terminológia

Megtekintheti az **általános IoT használati feltételeinek** listáját, valamint az Azure IoT-szolgáltatásokban, például az Azure digitális Twins-ban, itt találhatja meg az Azure [IoT szószedetét](../iot-fundamentals/iot-glossary.md?toc=/azure/digital-twins/toc.json&bc=/azure/digital-twins/breadcrumb/toc.json). Ez hasznos hivatkozás lehet az Azure digitális Twins szolgáltatással való Ismerkedés és a IoT-megoldás létrehozása során.

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg az Azure Digital Twins szolgáltatással a gyors üzembe helyezési útmutatóban: rövid útmutató [*: példa a forgatókönyvek megismerésére*](quickstart-adt-explorer.md).

* Vagy kezdjen el olvasni az Azure digitális Twins fogalmait a [*fogalmakkal: egyéni modellek*](concepts-models.md).