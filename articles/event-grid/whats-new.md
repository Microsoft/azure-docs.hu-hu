---
title: Újdonságok Kibocsátási megjegyzések – Azure Event Grid
description: Ismerje meg a Azure Event Grid újdonságát, például a legújabb kibocsátási megjegyzéseket, az ismert problémákat, a hibajavításokat, az elavult funkciókat és a közelgő módosításokat.
ms.topic: overview
ms.date: 07/23/2020
ms.openlocfilehash: f135b25f28002c037dd24fa0cb3bb7476a06309f
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389587"
---
# <a name="whats-new-in-azure-event-grid"></a>Újdonságok a Azure Event Grid?

>Az RSS-hírcsatorna olvasó ikoncsatorna-olvasójának bemásolásával és beillesztésével értesítést kap arról, hogy mikor kell újra az oldalra frissítéseket `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Event+Grid%22&locale=en-us` ![ ](./media/whats-new/feed-icon-16x16.png) szereznie.

Azure Event Grid folyamatosan kap fejlesztéseket. Annak érdekében, hogy naprakész maradjon a legújabb fejlesztésekkel kapcsolatban, ez a cikk a következő információkkal rendelkezik:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- Terv a változásokhoz

## <a name="610-preview-2020-10"></a>6.1.0-preview (2020-10)
- [Rendszertémaktémák felügyelt identitásai](enable-identity-system-topics.md)
- [Egyéni kézbesítési tulajdonságok](delivery-properties.md)
- [Storage-üzenetsor – üzenethez való idő (TTL)](delivery-properties.md#configure-time-to-live-on-outgoing-events-to-azure-storage-queues)
- [Speciális szűrési fejlesztések](event-filtering.md#advanced-filtering)
    - Tömbadatok szűrésének támogatása bejövő eseményekben
    - A CloudEvents bővítmény környezeti attribútumainak szűrésének engedélyezése
    - Új operátorok
        - StringNotContains
        - StringNotBeginsWith
        - StringNotEndsWith
        - NumberInRange
        - NumberNotInRange
        - IsNull
        - IsNotNull
- [Séma Event Grid CloudEvents 1.0 sémaátalakításának engedélyezése egyéni témakörökhöz és tartományokhoz](cloudevents-schema.md#configure-event-grid-for-cloudevents)
        

## <a name="600-2020-06"></a>6.0.0 (2020-06)
- Támogatás hozzáadása az új általánosan elérhető (GA) szolgáltatás API 2020-06-01-es verziójához.
- Az új funkciók, amelyek ga gavává váltak:
    - [Bemeneti leképezések](input-mappings.md)
    - [Egyéni bemeneti séma](input-mappings.md)
    - [V10-es felhőesemény sémája](cloud-event-schema.md)
    - [Service Bus témakör mint cél](handler-service-bus.md)
    - [Az Azure-függvény mint célhely](handler-functions.md)
    - [Webhookköteezés](./edge/delivery-output-batching.md)
    - [Biztonságos webhook (Azure Active Directory támogatás)](secure-webhook-delivery.md)
    - [IP-szűrés](configure-firewall.md)
    - [Private Link szolgáltatás támogatása](configure-private-endpoints.md)
    - [Eseményk kézbesítési séma](event-schema.md)

## <a name="532-preview-2020-05"></a>5.3.2-preview (2020-05)
- Ez a kiadás további hibajavításokat tartalmaz a minőség javítása érdekében.
- Az 5.3.1-preview verzióban ez a kiadás a 2020-04-01-Preview API-verziónak felel meg, amely a következő új funkciókat tartalmazza: 
    - [IP-szűrés támogatása események tartományokba és témakörökbe való közzétételekor](configure-firewall.md)
    - [Partnertémakörök](./partner-events-overview.md)
    - [Rendszertémaktémák nyomon követhető erőforrásokként a Azure Portal](system-topics.md)
    - [Események kézbesítése felügyeltszolgáltatás-identitással](managed-service-identity.md) 
    - [Private Link szolgáltatás támogatása](configure-private-endpoints.md)

## <a name="531-preview-2020-04"></a>5.3.1-preview (2020-04)
- Ez a kiadás számos hibajavítást tartalmaz a minőség javítása érdekében.
- Az 5.3.0-preview verzióban ez a kiadás a 2020-04-01-Preview API-verziónak felel meg, amely a következő új funkciókat tartalmazza: 
    - [IP-szűrés támogatása események tartományokba és témakörökbe való közzétételekor](configure-firewall.md)
    - [Partnertémakörök](./partner-events-overview.md)
    - [Rendszertémaktémák nyomon követhető erőforrásokként a Azure Portal](system-topics.md)
    - [Események kézbesítése felügyeltszolgáltatás-identitással](managed-service-identity.md) 
    - [Private Link szolgáltatás támogatása](configure-private-endpoints.md)

## <a name="530-preview-2020-03"></a>5.3.0-preview (2020-03)
- Az új funkciókat az 5.2.0-s előzetes verzióban már elérhető funkciókon felül vezetjük be. 
- Az 5.2.0-s előzetes verzióként ez a kiadás a 2020-04-01-Preview API-verziónak felel meg.
- A következő új funkciókat támogatja: 
    - [IP-szűrés támogatása események tartományokba és témakörökbe való közzétételekor](configure-firewall.md)
    - [Partnertémakörök](./partner-events-overview.md)
    - [Rendszertémaktémák nyomon követhető erőforrásokként a Azure Portal](system-topics.md)
    - [Események kézbesítése felügyeltszolgáltatás-identitással](managed-service-identity.md) 
    - [Private Link szolgáltatás támogatása](configure-private-endpoints.md)

## <a name="520-preview-2020-01"></a>5.2.0-preview (2020-01)
- Ez a kiadás a 2020-04-01-Preview API-verziónak felel meg.
- A következő új funkciókat támogatja:
    - [IP-szűrés támogatása események tartományokba és témakörökbe való közzétételekor](configure-firewall.md)

## <a name="500-2019-05"></a>5.0.0 (2019-05)
- Ez a kiadás az `2019-06-01` API verziójának felel meg.
- A következő új funkciókat támogatja:
    * [Tartományok](event-domains.md)
    * Lapszámozási és keresési szűrő az erőforrások listázási műveleteihez. Egy példát a [Témakörök – List by Subscription (Témakörök – Listás előfizetés szerint) témakörben láthat.](/rest/api/eventgrid/version2020-10-15-preview/partnernamespaces/listbysubscription)
    * [Service Bus célként való várakozási sor](handler-service-bus.md)
    * [Speciális szűrés](event-filtering.md#advanced-filtering)

## <a name="410-preview-2019-03"></a>4.1.0-preview (2019-03)
- Ez a kiadás a 2019. 02. 01. 01., előzetes verziójú API-verziónak felel meg.
- A következő új funkciókat támogatja:
    * Lapszámozási és keresési szűrő az erőforrások listázási műveleteihez. Egy példát a [Témakörök – List by Subscription (Témakörök – Listás előfizetés szerint) témakörben láthat.](/rest/api/eventgrid/version2020-10-15-preview/partnernamespaces/listbysubscription)
    * [Tartományi témakörök manuális létrehozása/törlése](how-to-event-domains.md)
    * [Service Bus queue mint célhely](handler-service-bus.md)

## <a name="400-2018-12"></a>4.0.0 (2018-12)
- Ez a kiadás a stabil `2019-01-01` API-verziónak felel meg.
- Támogatja az esemény-előfizetésekkel kapcsolatos alábbi funkciók általános elérhetőségét:
    * [Holtbetű célhely](manage-event-delivery.md)
    * [Azure Storage-üzenetsor célként](handler-storage-queues.md)
    * [Azure Relay – Hibrid kapcsolat célként](handler-relay-hybrid-connections.md)
    * [Manuális kézfogás-ellenőrzés](webhook-event-delivery.md)
    * [Újrapróbálkozási szabályzatok támogatása](delivery-and-retry.md)
- A még előzetes verziójú funkciók (például [](event-filtering.md#advanced-filtering) az [Event Grid-tartományok](event-domains.md) vagy a speciális szűrők támogatása) továbbra is elérhetők az SDK 3.0.1-es előzetes verziójával."

## <a name="301-preview-2018-10"></a>3.0.1-preview (2018-10)
- Függőséget vesz fel a [Newtonsoft NuGet-csomag 10.0.3-as verziójától.](https://www.nuget.org/packages/Newtonsoft.Json/10.0.3)

## <a name="300-preview-2018-10"></a>3.0.0-preview (2018-10)
- Ez a kiadás egy előzetes verziójú SDK a 2018. 09. 15-én bevezetett új funkciókhoz. – Ez a kiadás a következőket támogatja:
    - [Tartományi és tartományi témakörök](event-domains.md)
    - Az [esemény-előfizetés lejárati dátuma](concepts.md#event-subscription-expiration)
    - Az [esemény-előfizetések speciális](event-filtering.md#advanced-filtering) szűrésének bevezetése
    - Az API-verziót célzó SDK stabil verziója továbbra is `2018-01-01` 1.3.0-ás verzióként létezik

## <a name="next-steps"></a>Következő lépések