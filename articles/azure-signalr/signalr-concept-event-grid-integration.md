---
title: Reagálás az Azure Signaler szolgáltatás eseményeire
description: Azure Event Grid használata az Azure Signaler szolgáltatás eseményeire való előfizetéshez. Ezek az események más alsóbb rétegbeli szolgáltatásokat is indíthatnak.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74158187"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reagálás az Azure SignalR Service eseményeire

Az Azure Signaler szolgáltatás eseményei lehetővé teszik, hogy az alkalmazások reagálni tudjanak a modern kiszolgáló nélküli architektúrákkal létesített vagy leválasztott ügyfélkapcsolatokra. Ehhez nincs szükség bonyolult programkódra vagy költséges és nem hatékony lekérdezési szolgáltatásokra.  Ehelyett az eseményeket [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) az előfizetők, például a [Azure Functions](https://azure.microsoft.com/services/functions/), a [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/), vagy akár a saját egyéni HTTP-figyelő számára is leküldik, és csak azért kell fizetnie, amit ténylegesen használ.

Az Azure Signaler szolgáltatás eseményei megbízhatóan eljutnak a Event Grid szolgáltatásba, amely megbízható kézbesítési szolgáltatásokat nyújt az alkalmazásoknak a gazdag újrapróbálkozási szabályzatok és a kézbesítetlen levelek kézbesítése révén. További információ: [Event Grid üzenet kézbesítése, és próbálkozzon újra](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

![Event Grid modell](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Kiszolgáló nélküli állapot
Az Azure Signaler szolgáltatás eseményei csak akkor aktívak, ha az ügyfélkapcsolatok kiszolgáló nélküli állapotban vannak. Általánosságban elmondható, hogy ha egy ügyfél nem irányít egy központi kiszolgálót, akkor a kiszolgáló nélküli állapotba kerül. A klasszikus mód csak abban az esetben működik, ha a hub, amelyhez az ügyfélkapcsolatok csatlakoznak, nem rendelkezik központi kiszolgálóval. A probléma elkerülése érdekében azonban a kiszolgáló nélküli üzemmód használata javasolt. A szolgáltatási móddal kapcsolatos további információkért lásd: [a szolgáltatási mód kiválasztása](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Elérhető Azure Signaler szolgáltatás eseményei
Az Event Grid [esemény-előfizetések](../event-grid/concepts.md#event-subscriptions) használatával irányítja az esemény-üzeneteket az előfizetőknek. Az Azure Signaler szolgáltatás esemény-előfizetései két típusú eseményt támogatnak:  

|Esemény neve|Description|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Az ügyfélkapcsolatok kapcsolódásakor következik be.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Az ügyfél kapcsolatának leválasztásakor következik be.|

## <a name="event-schema"></a>Eseményséma
Az Azure Signaler szolgáltatás eseményei tartalmazzák az adatok változásaira való válaszadáshoz szükséges összes információt. A "Microsoft. SignalRService" kezdetű Azure Signaler szolgáltatásbeli eseményt azonosíthatja a eventType tulajdonsággal. Event Grid esemény tulajdonságainak használatáról további információt [Event Grid Event Schema](../event-grid/event-schema.md)dokumentációban olvashat.  

Az alábbi példa egy ügyfél-kapcsolathoz kapcsolódó eseményt mutat be:
```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

További információ: [signaler Service Events Schema](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>További lépések

Tudjon meg többet a Event Gridről, és adjon meg egy esélyt az Azure Signaler szolgáltatás eseményeire:

> [!div class="nextstepaction"]
> [Példa Event Grid az Azure signaler szolgáltatással](./signalr-howto-event-grid-integration.md) 
>  való integráció kipróbálására [Tudnivalók Event Grid](../event-grid/overview.md)
