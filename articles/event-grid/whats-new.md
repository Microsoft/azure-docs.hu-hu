---
title: Újdonságok Kibocsátási megjegyzések – Azure Event Grid
description: Ismerje meg a Azure Event Grid újdonságait, például a legújabb kibocsátási megjegyzéseket, ismert problémákat, hibajavításokat, elavult funkciókat és a közelgő változásokat.
ms.topic: overview
ms.date: 07/23/2020
ms.openlocfilehash: 7528cef7007704bbf434f16622707da8920eec2b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96350007"
---
# <a name="whats-new-in-azure-event-grid"></a>A Azure Event Grid újdonságai

>Értesítést kaphat arról, hogy mikor kell újra megkeresni ezt az oldalt a frissítésekhez az URL-cím másolásával és beillesztésével: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Event+Grid%22&locale=en-us` az ![ RSS-hírcsatorna olvasójának ikonját a ](./media/whats-new/feed-icon-16x16.png) hírcsatorna-olvasóba.

Azure Event Grid folyamatosan fejleszti a fejlesztéseket. A legújabb fejleményekkel naprakészen tarthatja a következő információkat:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- A változtatások tervei

## <a name="600-2020-06"></a>6.0.0 (2020-06)
- Támogatás hozzáadása az új általánosan elérhető (GA) Service API 2020-06-01-es verziójához.
- A GA új funkciói:
    - [Bemeneti leképezések](input-mappings.md)
    - [Egyéni bemeneti séma](input-mappings.md)
    - [Cloud Event v10-séma](cloud-event-schema.md)
    - [Témakör Service Busa célként](handler-service-bus.md)
    - [Azure-függvény célként](handler-functions.md)
    - [Webhook-köteg](./edge/delivery-output-batching.md)
    - [Biztonságos webhook (Azure Active Directory támogatás)](secure-webhook-delivery.md)
    - [IP-szűrés](configure-firewall.md)
    - [Private link Service-támogatás](configure-private-endpoints.md)
    - [Esemény kézbesítési sémája](event-schema.md)

## <a name="532-preview-2020-05"></a>5.3.2 – előzetes verzió (2020-05)
- Ez a kiadás a minőség javítása érdekében további hibajavításokat tartalmaz.
- Az 5.3.1-as verzióban ez a kiadás a 2020-04-01 – előzetes verziójú API-verziónak felel meg, amely a következő új funkciókat tartalmazza: 
    - [IP-szűrés támogatása az események tartományokra és témakörökre való közzétételekor](configure-firewall.md)
    - [Partnertémakörök](./partner-events-overview.md)
    - [Rendszertémakörök nyomon követett erőforrásokként Azure Portal](system-topics.md)
    - [Esemény kézbesítése felügyelt szolgáltatás identitásával](managed-service-identity.md) 
    - [Private link Service-támogatás](configure-private-endpoints.md)

## <a name="531-preview-2020-04"></a>5.3.1 – előzetes verzió (2020-04)
- Ez a kiadás különféle hibajavításokat tartalmaz a minőség javítása érdekében.
- A 5.3.0 előzetes verziójának részeként ez a kiadás a 2020-04-01-Preview API-verziónak felel meg, amely a következő új funkciókat tartalmazza: 
    - [IP-szűrés támogatása az események tartományokra és témakörökre való közzétételekor](configure-firewall.md)
    - [Partnertémakörök](./partner-events-overview.md)
    - [Rendszertémakörök nyomon követett erőforrásokként Azure Portal](system-topics.md)
    - [Esemény kézbesítése felügyelt szolgáltatás identitásával](managed-service-identity.md) 
    - [Private link Service-támogatás](configure-private-endpoints.md)

## <a name="530-preview-2020-03"></a>5.3.0 – előzetes verzió (2020-03)
- A 5.2.0-Preview verzióban már hozzáadott funkciókra épülő új szolgáltatásokat mutatjuk be. 
- A 5.2.0 előzetes verziójának részeként ez a kiadás a 2020-04-01-Preview API-verziónak felel meg.
- Támogatja a következő új funkciókat: 
    - [IP-szűrés támogatása az események tartományokra és témakörökre való közzétételekor](configure-firewall.md)
    - [Partnertémakörök](./partner-events-overview.md)
    - [Rendszertémakörök nyomon követett erőforrásokként Azure Portal](system-topics.md)
    - [Esemény kézbesítése felügyelt szolgáltatás identitásával](managed-service-identity.md) 
    - [Private link Service-támogatás](configure-private-endpoints.md)

## <a name="520-preview-2020-01"></a>5.2.0 – előzetes verzió (2020-01)
- Ez a kiadás a 2020-04-01-Preview API-verziónak felel meg.
- Támogatja a következő új funkciókat:
    - [IP-szűrés támogatása az események tartományokra és témakörökre való közzétételekor](configure-firewall.md)

## <a name="500-2019-05"></a>5.0.0 (2019-05)
- Ez a kiadás az `2019-06-01` API-verziónak felel meg.
- Támogatást nyújt a következő új funkciókhoz:
    * [Tartományok](event-domains.md)
    * Tördelés és keresési szűrő az erőforrások listája műveleteihez. Példaként tekintse meg a [témakörök-lista előfizetés alapján](/rest/api/eventgrid/version2020-04-01-preview/topics/listbysubscription)című témakört.
    * [Service Bus várólista célhelyként](handler-service-bus.md)
    * [Speciális szűrés](event-filtering.md#advanced-filtering)

## <a name="410-preview-2019-03"></a>4.1.0 – előzetes verzió (2019-03)
- Ez a kiadás a 2019-02-01-Preview API-verziónak felel meg.
- Támogatást nyújt a következő új funkciókhoz:
    * Tördelés és keresési szűrő az erőforrások listája műveleteihez. Példaként tekintse meg a [témakörök-lista előfizetés alapján](/rest/api/eventgrid/version2020-04-01-preview/topics/listbysubscription)című témakört.
    * [Tartományi témakörök manuális létrehozása/törlése](how-to-event-domains.md)
    * [Service Bus várólista célhelyként](handler-service-bus.md)

## <a name="400-2018-12"></a>4.0.0 (2018-12)
- Ez a kiadás megfelel a `2019-01-01` stabil API-verziónak.
- Az esemény-előfizetésekkel kapcsolatos alábbi funkciók általánosan elérhetővé tételét (GA) támogatja:
    * [Kézbesítetlen levél célhelye](manage-event-delivery.md)
    * [Azure Storage-várólista célként](handler-storage-queues.md)
    * [Azure Relay – hibrid kapcsolatok célhelyként](handler-relay-hybrid-connections.md)
    * [Manuális kézfogás ellenőrzése](webhook-event-delivery.md)
    * [Újrapróbálkozási szabályzatok támogatása](delivery-and-retry.md)
- Az előzetes verzióban még elérhető szolgáltatások (például [Event Grid tartományok](event-domains.md) vagy [speciális szűrők támogatása](event-filtering.md#advanced-filtering) továbbra is elérhető az SDK 3.0.1-Preview verziójával. "

## <a name="301-preview-2018-10"></a>3.0.1 – előzetes verzió (2018-10)
- A [Newtonsoft NuGet-csomag 10.0.3-verziójától](https://www.nuget.org/packages/Newtonsoft.Json/10.0.3)függ.

## <a name="300-preview-2018-10"></a>3.0.0 – előzetes verzió (2018-10)
- Ez a kiadás egy előzetes verziójú SDK az 2018-09-15-os API-verzióban bevezetett új funkciókhoz. – Ez a kiadás a következők támogatását tartalmazza:
    - [Tartományi és tartományi témakörök](event-domains.md)
    - [Az esemény-előfizetés lejárati dátumának](concepts.md#event-subscription-expiration) bemutatása
    - Az esemény-előfizetések [speciális szűrésének](event-filtering.md#advanced-filtering) bemutatása
    - Az SDK-t az API-verziót célzó stabil verziója `2018-01-01` továbbra is a 1.3.0 verziójának minősül.

## <a name="next-steps"></a>Következő lépések