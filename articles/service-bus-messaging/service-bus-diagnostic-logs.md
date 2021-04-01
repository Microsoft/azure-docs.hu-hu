---
title: Diagnosztikai naplók Azure Service Bus | Microsoft Docs
description: Ez a cikk a Azure Service Bus számára elérhető összes operatív és diagnosztikai napló áttekintését tartalmazza.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: f2672d61285025eadb8f742ca2518297107595ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585263"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>Diagnosztikai naplók engedélyezése a Service Bushoz

Ha megkezdi a Azure Service Bus névteret, érdemes figyelnie, hogyan és mikor hozza létre, törli vagy eléri a névteret. Ez a cikk áttekintést nyújt az összes elérhető operatív és diagnosztikai naplóról.

Azure Service Bus jelenleg támogatja a tevékenység-és működési naplókat, amelyek rögzítik a Azure Service Bus névtéren végrehajtott *felügyeleti műveleteket* . Ezek a naplók pontosan rögzítik a művelet típusát, beleértve a várólista létrehozását, a felhasznált erőforrásokat és a művelet állapotát.

## <a name="operational-logs-schema"></a>Operatív naplók sémája

Az összes napló JavaScript Object Notation (JSON) formátumban van tárolva a következő két helyen:

- **AzureActivity**: azoknak a műveleteknek és műveleteknek a naplóit jeleníti meg, amelyeket a rendszer a Azure Portal vagy Azure Resource Manager sablonon végzett központi telepítések során hajt végre a névtérben.
- **AzureDiagnostics**: megjeleníti a naplókat azokról a műveletekről és műveletekről, amelyeket az API-val, vagy a Language SDK felügyeleti ügyfelein keresztül hajt végre a névtéren.

Az operatív napló JSON-karakterláncai közé tartoznak az alábbi táblázatban felsorolt elemek:

| Név | Leírás |
| ------- | ------- |
| Tevékenységazonosító | A megadott tevékenység azonosítására szolgáló belső azonosító |
| EventName | Művelet neve |
| ResourceId | Erőforrás-azonosító Azure Resource Manager |
| SubscriptionId | Előfizetés azonosítója |
| EventTimeString | Működési idő |
| EventProperties | Művelet tulajdonságai |
| Állapot | Művelet állapota |
| Hívó | A művelet hívója (a Azure Portal vagy a felügyeleti ügyfél) |
| Kategória | OperationalLogs |

Példa egy operatív napló JSON-karakterláncára:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>A műveleti naplókban rögzített események és műveletek

Az operatív naplók rögzítik a Azure Service Bus névtéren végrehajtott összes felügyeleti műveletet. Az adatműveletek nem lesznek rögzítve, mert a Azure Service Buson végrehajtott nagy mennyiségű adatművelet miatt.

> [!NOTE]
> Az adatműveletek jobb nyomon követése érdekében ajánlott az ügyféloldali nyomkövetés használata.

Az üzemeltetési naplók a következő kezelési műveleteket rögzítik: 

| Hatókör | Művelet|
|-------| -------- |
| Névtér | <ul> <li> Névtér létrehozása</li> <li> Névtér frissítése </li> <li> Névtér törlése </li> <li> Névtér SharedAccess házirend frissítése </li> </ul> | 
| Üzenetsor | <ul> <li> Várólista létrehozása</li> <li> Üzenetsor frissítése</li> <li> Üzenetsor törlése </li> <li> Üzenetsor törlése </li> </ul> | 
| Témakör | <ul> <li> Témakör létrehozása </li> <li> Témakör frissítése </li> <li> Témakör törlése </li> <li> Törlési témakör autodelete </li> </ul> |
| Előfizetés | <ul> <li> Előfizetés létrehozása </li> <li> Előfizetés frissítése </li> <li> Előfizetés törlése </li> <li> Előfizetés törlése </li> </ul> |

> [!NOTE]
> Az *olvasási* műveletek jelenleg nincsenek nyomon követve az operatív naplókban.

## <a name="enable-operational-logs"></a>Operatív naplók engedélyezése

Az operatív naplók alapértelmezés szerint le vannak tiltva. A diagnosztikai naplók engedélyezéséhez tegye a következőket:

1. A [Azure Portal](https://portal.azure.com)nyissa meg a Azure Service Bus névteret, majd a **figyelés** területen válassza a  **diagnosztikai beállítások** elemet.

   ![A "diagnosztikai beállítások" hivatkozás](./media/service-bus-diagnostic-logs/image1.png)

1. A **diagnosztika beállításai** ablaktáblán válassza a **diagnosztikai beállítás hozzáadása** elemet.  

   ![A "diagnosztikai beállítás hozzáadása" hivatkozás](./media/service-bus-diagnostic-logs/image2.png)

1. Konfigurálja a diagnosztikai beállításokat a következő módon:

   a. A **név** mezőbe írja be a diagnosztikai beállítások nevét.  

   b. Válasszon egyet a következő három célhely közül a diagnosztikai naplókhoz:  
   - Ha a **Storage-fiókba az Archive (archiválás**) lehetőséget választja, akkor konfigurálnia kell azt a Storage-fiókot, ahol a rendszer a diagnosztikai naplókat tárolja.  
   - Ha a stream elemet választja **egy Event hub-hoz**, akkor konfigurálnia kell azt az Event hub-t, amelybe a diagnosztikai naplókat továbbítani szeretné.
   - Ha a **küldés log Analytics** lehetőséget választja, meg kell adnia a diagnosztika azon log Analytics példányát, amelybe a rendszer elküldi a diagnosztikát.  

   c. Jelölje be a **OperationalLogs** jelölőnégyzetet.

    ![A "diagnosztikai beállítások" panel](./media/service-bus-diagnostic-logs/image3.png)

1. Kattintson a **Mentés** gombra.

Az új beállítások körülbelül 10 percen belül lépnek érvénybe. A naplók a konfigurált archiválási célpontban, a **diagnosztikai naplók** panelen jelennek meg.

A diagnosztikai beállítások konfigurálásával kapcsolatos további információkért tekintse meg az [Azure Diagnostics-naplók áttekintését](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="next-steps"></a>Következő lépések

A Service Busról további információt a következő témakörben talál:

* [Bevezetés a Service Busba](service-bus-messaging-overview.md)
* [A Service Bus használatának első lépései](service-bus-dotnet-get-started-with-queues.md)