---
title: Események naplózása az Azure Event Hubsba az Azure API Managementban | Microsoft Docs
description: Ismerje meg, hogyan naplózhat eseményeket az Azure Event Hubsba az Azure API Managementban. A Event Hubs egy rugalmasan méretezhető adatbeviteli szolgáltatás.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 5066169951409fa86aa75a64e8fc6d4189947f27
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92072407"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Események naplózása az Azure Event Hubsba az Azure-ban API Management
Az Azure Event Hubs egy kiválóan méretezhető adatbefogadási szolgáltatás, amely másodpercenként több millió esemény fogadására képes, így a csatlakoztatott eszközök és alkalmazások által létrehozott nagy mennyiségű adatot egyszerűen feldolgozhatja és elemezheti. Event Hubs "bejárati ajtóként" viselkedik egy esemény-adatcsatorna esetében, és az adatok egy Event hubhoz való gyűjtése után átalakítható és tárolható bármely valós idejű elemzési szolgáltató vagy kötegelt/Storage-adapter használatával. Az Event Hubs elválasztja az eseménystreamek létrehozását azok felhasználásától, így az események felhasználói a saját ütemezésüknek megfelelően férhetnek hozzá az eseményekhez.

Ez a cikk az [Azure-API Management Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) videóval való integrálását és az Azure Event Hubs használatával történő API Management események naplózását ismerteti.

## <a name="create-an-azure-event-hub"></a>Azure Event Hub létrehozása

Az Event hub létrehozásával és a kapcsolati karakterláncok beszerzésével kapcsolatos részletes lépésekért, amelyekkel az Event hub-ba érkező és onnan érkező eseményeket kell elküldeni, tekintse meg [a Event Hubs névtér és az Event hub Azure Portal használatával történő létrehozását](../event-hubs/event-hubs-create.md)ismertető témakört.

## <a name="create-an-api-management-logger"></a>API Management naplózó létrehozása
Most, hogy rendelkezik egy Event hub-val, a következő lépés egy [naplózó](/rest/api/apimanagement/2019-12-01/logger) konfigurálása a API Management szolgáltatásban, hogy az eseményeket naplózni tudja az Event hub-ban.

API Management naplózók konfigurálása a [API Management REST API](/rest/api/apimanagement/ApiManagementREST/API-Management-REST)használatával történik. A részletes kérelmekre vonatkozó példákat a következő témakörben talál: [naplózók létrehozása](/rest/api/apimanagement/2019-12-01/logger/createorupdate).

## <a name="configure-log-to-eventhub-policies"></a>Eventhub házirendek konfigurálása

Ha a naplózó konfigurálva van API Managementban, beállíthatja a eventhub-házirendet, hogy naplózza a kívánt eseményeket. A eventhub házirend a bejövő házirend szakaszban vagy a kimenő házirend szakaszban használható.

1. Tallózzon az APIM-példányra.
2. Válassza az API lapot.
3. Válassza ki azt az API-t, amelyhez hozzá szeretné adni a szabályzatot. Ebben a példában egy szabályzatot adunk hozzá az **echo API** -hoz a **korlátlan** termékben.
4. Válassza **az összes művelet** lehetőséget.
5. A képernyő felső részén válassza a tervezés lapot.
6. A bejövő vagy kimenő feldolgozási ablakban kattintson a háromszögre (a ceruza mellett).
7. Válassza ki a Kódszerkesztőt. További információ: [szabályzatok beállítása vagy szerkesztése](set-edit-policies.md).
8. Vigye a kurzort a `inbound` vagy a `outbound` házirend szakaszba.
9. A jobb oldali ablakban válassza a **speciális szabályzatok**  >  **napló EventHub** lehetőséget. Ez beszúrja a `log-to-eventhub` házirend-utasítás sablonját.

```xml
<log-to-eventhub logger-id="logger-id">
    @{
        return new JObject(
            new JProperty("EventTime", DateTime.UtcNow.ToString()),
            new JProperty("ServiceName", context.Deployment.ServiceName),
            new JProperty("RequestId", context.RequestId),
            new JProperty("RequestIp", context.Request.IpAddress),
            new JProperty("OperationName", context.Operation.Name)
        ).ToString();
    }
</log-to-eventhub>
```
Cserélje le a `logger-id` értéket a kérelem URL-címében használt értékre az `{loggerId}` előző lépésben létrehozott naplózó létrehozásához.

Bármely olyan kifejezést használhat, amely egy karakterláncot ad vissza a elem értékeként `log-to-eventhub` . Ebben a példában egy JSON formátumú karakterláncot, amely a dátumot és az időt, a szolgáltatásnév, a kérelem AZONOSÍTÓját, a kérelem IP-címét és a művelet nevét tartalmazza.

A frissített házirend-konfiguráció mentéséhez kattintson a **Mentés** gombra. Amint menti a szabályzatot, a rendszer aktív állapotba kerül, és az eseményeket a kijelölt Event hub-ba naplózza.

> [!NOTE]
> A API Management szabályzatból az Event hub számára elküldhető maximális támogatott üzenet mérete 200 kilobájt (KB). Ha az Event hub számára küldött üzenet mérete meghaladja a 200 KB-ot, az automatikusan csonkítva lesz, és a csonkolt üzenet az Event hubokba lesz átadva.

## <a name="preview-the-log-in-event-hubs-by-using-azure-stream-analytics"></a>A bejelentkezés előnézete Event Hubs Azure Stream Analytics használatával

A naplót Event Hubs [Azure stream Analytics lekérdezések](../event-hubs/process-data-azure-stream-analytics.md)használatával tekintheti meg. 

1. A Azure Portal keresse meg azt az Event hub-t, amelyet a naplóz az eseményeket küld. 
2. A **szolgáltatások** területen válassza a **folyamat adat** lapot.
3. A **valós idejű** adatvizsgálatok engedélyezése az események kártyáról lapon válassza a **Tallózás** lehetőséget.
4. A **bemeneti előnézet** lapon megtekintheti a naplót. Ha a megjelenített adatértékek nem aktuálisak, válassza a **frissítés** lehetőséget a legújabb események megtekintéséhez.

## <a name="next-steps"></a>Következő lépések
* További információ az Azure Event Hubs
  * [Ismerkedés az Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Üzenetek fogadása az EventProcessorHost szolgáltatással](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
  * [Event Hubs programozási útmutató](../event-hubs/event-hubs-programming-guide.md)
* További információ a API Management és a Event Hubs integrációról
  * [Naplózó entitás referenciája](/rest/api/apimanagement/2019-12-01/logger)
  * [eventhub házirend-hivatkozás](./api-management-advanced-policies.md#log-to-eventhub)
  * [Az API-k monitorozása az Azure API Management, a Event Hubs és a Moesif segítségével](api-management-log-to-eventhub-sample.md)  
* További információ az [Azure Application Insights-nal való integrációról](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png