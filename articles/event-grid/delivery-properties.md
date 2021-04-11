---
title: Azure Event Grid – egyéni fejlécek beállítása a továbbított eseményeken
description: Ismerteti, hogyan lehet egyéni fejléceket (vagy kézbesítési tulajdonságokat) beállítani a kézbesített eseményekhez.
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: e2eff6b0d1dc78f0d558bb8e4e1ad79c62c52657
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630420"
---
# <a name="delivery-with-custom-headers"></a>Kézbesítés egyéni fejlécekkel
Az esemény-előfizetések lehetővé teszik a kézbesített események részét képező HTTP-fejlécek beállítását. Ez a funkció lehetővé teszi, hogy egyéni fejléceket állítson be, amelyekre a célhelynek szüksége van. Esemény-előfizetés létrehozásakor akár 10 fejlécet is beállíthat. Minden fejléc értéke nem lehet nagyobb, mint 4 096 (4K) bájt.

Egyéni fejléceket állíthat be a következő célhelyekre küldött eseményeken:

- Webhookok
- Témakörök és várólisták Azure Service Bus
- Azure Event Hubs
- Továbbító Hibrid kapcsolatok

Amikor esemény-előfizetést hoz létre a Azure Portalban, a **kézbesítés tulajdonságai** lapon adhatja meg az egyéni HTTP-fejléceket. Ezen a lapon megadhatja a rögzített és a dinamikus fejlécek értékeit.

## <a name="setting-static-header-values"></a>Statikus fejléc értékeinek beállítása
Rögzített értékkel rendelkező fejlécek megadásához adja meg a fejléc nevét és a hozzá tartozó értéket a megfelelő mezőkben:

:::image type="content" source="./media/delivery-properties/static-header-property.png" alt-text="Kézbesítési tulajdonságok – statikus":::

Érdemes lehet megtekinteni **a titkos kulcsot? a** bizalmas adatok megadásakor. A bizalmas adatok nem jelennek meg a Azure Portal. 

## <a name="setting-dynamic-header-values"></a>Dinamikus fejléc értékeinek beállítása
A fejlécek értékét megadhatja egy bejövő esemény tulajdonsága alapján. A JsonPath szintaxissal hivatkozhat egy bejövő esemény tulajdonságérték értékére, amelyet a kimenő kérelmek fejlécének értékeként kíván használni. Például egy **csatorna** nevű fejléc értékének megadásához a bejövő **esemény tulajdonságának értékével az esemény** adataiban konfigurálja az esemény-előfizetést a következő módon:

:::image type="content" source="./media/delivery-properties/dynamic-header-property.png" alt-text="Kézbesítési tulajdonságok – dinamikus":::

## <a name="examples"></a>Példák
Ez a szakasz néhány példát kínál a kézbesítési tulajdonságok használatára.

### <a name="setting-the-authorization-header-with-a-bearer-token-non-normative-example"></a>Az engedélyezési fejléc beállítása tulajdonosi jogkivonattal (nem normatív példával)

Állítson be egy értéket egy engedélyezési fejlécre, hogy azonosítsa a kérést a webhook-kezelővel. Az engedélyezési fejléc beállítható, ha nem [védi a webhookot a Azure Active Directoryával](secure-webhook-delivery.md).

| Fejléc neve   | Fejléc típusa | Fejléc értéke |
| :--           | :--         | :--            |
|`Authorization` | Statikus | `BEARER SlAV32hkKG...`|

A kimenő kérelmeknek ekkor az esemény-előfizetés fejlécét kell tartalmazniuk:

```console
GET /home.html HTTP/1.1

Host: acme.com

User-Agent: <user-agent goes here>

Authorization: BEARER SlAV32hkKG...
```

> [!NOTE]
> Az engedélyezési fejlécek meghatározása egy ésszerű lehetőség, ha a cél egy webhook. Nem használható az erőforrás-azonosítóval, Service Bussal, Event Hubstal és Hibrid kapcsolatoktel [előfizetett függvények](/rest/api/eventgrid/eventsubscriptions/createorupdate#azurefunctioneventsubscriptiondestination)esetében, mivel ezek a célhelyek támogatják a saját hitelesítési rendszerüket a Event Grid használatakor.

### <a name="service-bus-example"></a>Service Bus példa
A Azure Service Bus támogatja a [BROKERPROPERTIES HTTP-fejlécek](/rest/api/servicebus/message-headers-and-properties#message-headers) használatát az üzenetek tulajdonságainak definiálásához egyetlen üzenet küldésekor. A `BrokerProperties` fejléc értékét JSON formátumban kell megadni. Ha például az üzenet tulajdonságait úgy kell beállítani, hogy egyetlen üzenetet küldjön a Service Busnak, a következő módon állítsa be a fejlécet:

| Fejléc neve | Fejléc típusa | Fejléc értéke |
| :-- | :-- | :-- |
|`BrokerProperties` | Statikus     | `BrokerProperties:  { "MessageId": "{701332E1-B37B-4D29-AA0A-E367906C206E}", "TimeToLive" : 90}` |


### <a name="event-hubs-example"></a>Event Hubs példa

Ha eseményeket kell közzétennie egy adott partíción az esemény-központon belül, Definiáljon egy [BROKERPROPERTIES http-fejlécet](/rest/api/eventhub/event-hubs-runtime-rest#common-headers) az esemény-előfizetésben, hogy megadja a cél Event hub-partíciót azonosító partíciós kulcsot.

| Fejléc neve | Fejléc típusa | Fejléc értéke                                  |
| :-- | :-- | :-- |
|`BrokerProperties` | Statikus | `BrokerProperties: {"PartitionKey": "0000000000-0000-0000-0000-000000000000000"}`  |


### <a name="configure-time-to-live-on-outgoing-events-to-azure-storage-queues"></a>A kimenő események élettartamának beállítása az Azure Storage-várólistákba
Az Azure Storage-várólisták célhelyének eléréséhez csak azt követően konfigurálhatja az élettartamot, hogy a kimenő üzenet elérhetővé válik az Azure Storage-üzenetsor számára. Ha nincs megadva idő, az üzenet alapértelmezett élettartama 7 nap. Az eseményt úgy is beállíthatja, hogy soha ne járjon le.

:::image type="content" source="./media/delivery-properties/delivery-properties-storage-queue.png" alt-text="Kézbesítési tulajdonságok – tárolási várólista":::

## <a name="next-steps"></a>Következő lépések
Az események kézbesítésével kapcsolatos további információkért tekintse meg a következő cikket:

- [Teljesítés és újrapróbálkozás](delivery-and-retry.md)
- [Webhook-eseménykézbesítés](webhook-event-delivery.md)
- [Eseményszűrés](event-filtering.md)
