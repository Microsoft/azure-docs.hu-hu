---
title: Eseményútvonalak
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan irányíthatja az eseményeket az Azure Digital Twins szolgáltatásban és más Azure-szolgáltatásokba.
author: baanders
ms.author: baanders
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ea412b695c12f3ff7fdfa6250e2a474b618b8032
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102430921"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Események irányítása az Azure digitális Twins-n belül és kívül

Az Azure Digital Twins **esemény-útvonalakat** használ az adatküldés céljából a szolgáltatáson kívüli felhasználók számára. 

Az Azure Digital Twins-adatok küldésének két fő esete van:
* Adatok küldése az Azure digitális Twins gráfból egy másikba. Ha például egy adott digitális kettős változásra vonatkozó tulajdonságot módosít, érdemes lehet értesíteni és frissíteni egy másik digitális dupla, ennek megfelelően.
* Adatok küldése a downstream adatszolgáltatásoknak további tárhely vagy feldolgozás céljából (más néven *adatforgalom*). Például:
  - A kórház az Azure Digital Twins-események [Time Series Insights (ÁME)](../time-series-insights/overview-what-is-tsi.md)szolgáltatásba való küldéséhez érdemes lehet a tömeges elemzéshez kapcsolódó, kézmosással kapcsolatos események idősoros adatainak rögzítése.
  - Egy olyan vállalat, amely már használ [Azure Maps](../azure-maps/about-azure-maps.md) , érdemes lehet az Azure digitális Twins használatával javítani a megoldást. Az Azure digitális Twins beállítása után gyorsan engedélyezhetik az Azure Map-t, hogy az Azure Map-entitásokat [digitális ikrekként](concepts-twins-graph.md) használják a Twin Graphban, vagy futtasson hatékony lekérdezéseket a Azure Maps és az Azure digitális Twins-adatszolgáltatások használatával.

Az esemény-útvonalak mindkét forgatókönyv esetében használhatók.

## <a name="about-event-routes"></a>Az események útvonala

Az Event Route lehetővé teszi, hogy az Azure Digital Twins digitális Twins-beli eseményeinek adatait az előfizetések egyéni meghatározott végpontjai számára küldje el. Jelenleg három Azure-szolgáltatás támogatott a végpontok számára: [Event hub](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)és [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Az Azure-szolgáltatások mindegyike kapcsolódhat más szolgáltatásokhoz, és a legközelebb álló adatokhoz, például az ÁME-hez vagy a Azure Mapshoz is elküldheti a szükséges feldolgozást.

Az alábbi ábrán az Event-adatforgalom egy nagyobb IoT-megoldáson keresztül látható az Azure digitális Twins aspektusával:

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="Az Azure digitális Twins több alsóbb rétegbeli szolgáltatás felé irányítja át a végpontokon keresztüli adattovábbítást" border="false":::

Az esemény-útvonalakra jellemző alsóbb rétegbeli célok olyan erőforrások, mint az ÁME, a Azure Maps, a Storage és az elemzési megoldások.

### <a name="event-routes-for-internal-digital-twin-events"></a>Esemény-útvonalak belső digitális kettős eseményekhez

Az Event Routes szolgáltatás a Twin gráf eseményeinek kezelésére is használható, és az adatok digitális Twin-ről digitális Twin-re való küldését is elküldheti. Ezt úgy teheti meg, hogy összekapcsolja az esemény-útvonalakat Event Grid a számítási erőforrások, például a [Azure functions](../azure-functions/functions-overview.md)használatával. Ezek a függvények határozzák meg, hogy az ikrek hogyan fogadhatnak és reagálnak az eseményekre. 

Ha egy számítási erőforrás módosítani szeretné a Twin gráfot egy esemény-útvonalon keresztül kapott esemény alapján, akkor hasznos, ha tudni szeretné, hogy az idő előtt milyen kettős módosításokat kíván módosítani. 

Azt is megteheti, hogy az üzenet az üzenetet küldő forrás-iker AZONOSÍTÓját is tartalmazza, így a számítási erőforrások lekérdezéseket használhatnak, illetve a kapcsolatok bejárásával megkereshetik a kívánt művelethez tartozó dupla célt. 

A számítási erőforrásnak emellett a biztonsági és hozzáférési engedélyeket egymástól függetlenül kell létrehoznia.

Az Azure-függvények digitális kettős események feldolgozására való beállításának folyamatát lásd: [*útmutató: Azure-függvény beállítása az adat feldolgozásához*](how-to-create-azure-function.md).

## <a name="create-an-endpoint"></a>Végpont létrehozása

Eseményútvonal definiálásához az a fejlesztőknek először végpontokat kell definiálniuk. A **végpont** az Azure Digital ikreken kívüli cél, amely támogatja az útvonal-kapcsolatokat. A támogatott célhelyek a következők:
* Egyéni témakörök Event Grid
* Eseményközpont
* Service Bus

Végpont létrehozásához használhatja az Azure Digital Twins [REST API-kat, a CLI-parancsokat](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins)vagy a [Azure Portal](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins).

A végpontok meghatározásakor a következőket kell megadnia:
* A végpont neve
* A végpont típusa (Event Grid, Event hub vagy Service Bus)
* A hitelesítő elsődleges kapcsolódási sztring és másodlagos kapcsolódási sztring 
* A végpont témakörének elérési útja, például *Your-topic.westus2.eventgrid.Azure.net*

A vezérlési síkon elérhető Endpoint API-k a következők:
* Végpont létrehozása
* Végpontok listájának beolvasása
* Végpont beolvasása név alapján
* Végpont törlése név szerint

## <a name="create-an-event-route"></a>Esemény útvonalának létrehozása
 
Esemény-útvonal létrehozásához használhatja az Azure Digital Twins [REST API-kat, a CLI-parancsokat](how-to-manage-routes-apis-cli.md#create-an-event-route)vagy a [Azure Portal](how-to-manage-routes-portal.md#create-an-event-route).

Íme egy példa arra, hogyan hozhat létre egy esemény-útvonalat az ügyfélalkalmazások között a `CreateOrReplaceEventRouteAsync` [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client) -hívás használatával: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::

1. Először egy `DigitalTwinsEventRoute` objektum jön létre, és a konstruktor a végpont nevét veszi át. Ez a `endpointName` mező egy végpontot azonosít, például egy Event hub, Event Grid vagy Service Bus. Ezeket a végpontokat az előfizetésében kell létrehoznia, és az Azure Digital Twins-hoz kell csatolni a vezérlési sík API-kkal a regisztrációs hívás előtt.

2. Az Event Route objektumhoz tartozik egy [**szűrő**](how-to-manage-routes-apis-cli.md#filter-events) mező is, amely az útvonalat követő események típusának korlátozására használható. A szűrője `true` lehetővé teszi az útvonal további szűrés nélküli használatát (a szűrő `false` letiltja az útvonalat). 

3. Ekkor a rendszer átadja ezt az esemény `CreateOrReplaceEventRouteAsync` -útválasztási objektumot, valamint az útvonal nevét.

> [!TIP]
> Minden SDK-függvény szinkron és aszinkron verzióban érhető el.

## <a name="dead-letter-events"></a>Kézbesítetlen levelek eseményei

Ha egy végpont nem tud eseményt kézbesíteni egy adott időszakon belül, vagy ha az eseményt bizonyos számú alkalommal próbálta kézbesíteni, akkor a kézbesítetlen eseményt elküldheti egy Storage-fiókba. Ezt a folyamatot **Kézbesítetlen levélnek** nevezzük. Az Azure Digital Twins egy eseményt fog kézbesíteni, ha teljesülnek **az alábbi feltételek valamelyike** . 

* Az esemény nem az adott időszakon belül érkezik
* Az esemény kézbesítésére tett kísérletek száma túllépte a korlátot

Ha a feltételek bármelyike teljesül, az esemény eldobása vagy elutasítása nem történik meg. Alapértelmezés szerint az egyes végpontok **nem** kapcsolják be a kézbesítetlen betűket. Ennek engedélyezéséhez meg kell adnia egy Storage-fiókot, amely a végpont létrehozásakor a nem kézbesítési eseményeket fogja tárolni. Ezután lehívhatja az eseményeket ebből a Storage-fiókból a kézbesítés feloldásához.

A kézbesítetlen levelek helyének beállítása előtt egy tárolóval rendelkező Storage-fiókkal kell rendelkeznie. Adja meg a tároló URL-címét a végpont létrehozásakor. A kézbesítetlen levél egy SAS-tokent tároló URL-ként van megadva. A jogkivonat csak a `write` Storage-fiókban lévő cél tárolóra vonatkozó engedélyre van szüksége. A teljesen formázott URL-cím a (z) formátumban jelenik meg: `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>`

További információ az SAS-tokenekről: [ *korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával*](../storage/common/storage-sas-overview.md)

A következő témakörben megtudhatja, hogyan állíthatja be a végpontokat a kézbesítetlen üzenetek használatával [*: hogyan kezelheti a végpontokat és az útvonalakat az Azure digitális ikrekben (API-k és parancssori felület)*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering).

### <a name="types-of-event-messages"></a>Az esemény típusú üzenetek típusai

A IoT Hub és az Azure Digital Twins különböző típusú eseményei különböző típusú értesítési üzeneteket hoznak létre az alább leírtak szerint.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>Következő lépések

Lásd: események útvonalának beállítása és kezelése:
* [*Útmutató: végpontok és útvonalak kezelése*](how-to-manage-routes-apis-cli.md)

Vagy tekintse meg a Azure Functions használata az események útválasztásához az Azure digitális Ikrekben:
* [*Útmutató: Azure-függvény beállítása az adat feldolgozásához*](how-to-create-azure-function.md)