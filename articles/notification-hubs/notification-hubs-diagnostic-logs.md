---
title: Azure Notification Hubs diagnosztikai naplók | Microsoft Docs
description: Ez a cikk az Azure Notification Hubs számára elérhető összes operatív és diagnosztikai napló áttekintését tartalmazza.
author: brannon
ms.author: brjones
ms.service: notification-hubs
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: b98a04a70062461cec603bea83052c4f1224819e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101736236"
---
# <a name="enable-diagnostics-logs-for-notification-hubs"></a>Diagnosztikai naplók engedélyezése a Notification Hubshoz

Az Azure Notification Hubs névtér használatának megkezdése után érdemes megfigyelni, hogy a névtér hogyan és mikor jön létre, törölve vagy elérhető. Ez a cikk áttekintést nyújt az összes elérhető operatív és diagnosztikai naplóról.

Az Azure Notification Hubs jelenleg támogatja a tevékenység-és működési naplókat, amelyek rögzítik az Azure Notification Hubs-névtéren végrehajtott *felügyeleti műveleteket* .

## <a name="diagnostic-logs-schema"></a>Diagnosztikai naplók sémája

Az összes napló JavaScript Object Notation (JSON) formátumban van tárolva a következő két helyen:

- **AzureActivity**: azoknak a műveleteknek és műveleteknek a naplóit jeleníti meg, amelyeket a rendszer a Azure Portal vagy Azure Resource Manager sablonon végzett központi telepítések során hajt végre a névtérben.
- **AzureDiagnostics**: megjeleníti a naplókat azokról a műveletekről és műveletekről, amelyeket az API-val, vagy a Language SDK felügyeleti ügyfelein keresztül hajt végre a névtéren.

A diagnosztikai napló JSON-karakterláncai közé tartoznak az alábbi táblázatban felsorolt elemek:

| Név | Leírás |
| ------- | ------- |
| time | A napló UTC-időbélyege |
| resourceId | Az Azure-erőforrás relatív elérési útja |
| operationName | A kezelési művelet neve |
| category | Napló kategóriája. Érvényes értékek: `OperationalLogs` |
| callerIdentity | A felügyeleti műveletet kezdeményező hívó identitása |
| resultType | A kezelési művelet állapota. Érvényes értékek: `Succeeded` vagy `Failed` |
| resultDescription | A kezelési művelet leírása |
| correlationId | A kezelési művelet korrelációs azonosítója (ha meg van adva) |
| callerIpAddress | A hívó IP-címe. Üres a Azure Portalból származó hívásokhoz |

Példa egy operatív napló JSON-karakterláncára:

```json
{
    "operationName": "Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action",
    "resourceId": "/SUBSCRIPTIONS/2CAC2A14-BA6B-46A6-BCE8-2D9781A41BA2/RESOURCEGROUPS/SAMPLES/PROVIDERS/MICROSOFT.NOTIFICATIONHUBS/NAMESPACES/SAMPLE-NS",
    "time": "1/1/2021 5:16:32 AM +00:00",
    "category": "OperationalLogs",
    "resultType": "Succeeded",
    "resultDescription": "Gets Hub Authorization Rules",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "callerIdentity": "{ \"identityType\": \"Portal\", \"identity\": \"\" }"
}
```

A `callerIdentity` mező lehet üres, vagy egy JSON-karakterlánc, amely az alábbi formátumok valamelyikével rendelkezik.

A Azure Portalból származó hívások esetén a `identity` mező üres. A napló összekapcsolhatók a tevékenység naplóival a bejelentkezett felhasználó meghatározásához.
```json
{
    "identityType": "Portal",
    "identity": ""
}
```

A Azure Resource Manager keresztül végrehajtott hívások esetén a `identity` mező a bejelentkezett felhasználó felhasználónevét fogja tartalmazni.
```json
{
   "identityType": "Username",
   "identity": "test@foo.com"
}
```

A Notification Hubs REST API a `identity` mező a SharedAccessSignature-token létrehozásához használt hozzáférési szabályzat nevét fogja tartalmazni.
```json
{
   "identityType": "KeyName",
   "identity": "SharedAccessRootKey2"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>A műveleti naplókban rögzített események és műveletek

Az operatív naplók rögzítik az Azure Notification Hubs-névtéren végrehajtott összes felügyeleti műveletet. Az adatműveletek nem kerülnek rögzítésre, mert az Azure Notification Hubson végrehajtott nagy mennyiségű adatművelet miatt.

Az üzemeltetési naplók a következő kezelési műveleteket rögzítik: 

| Hatókör | Művelet neve | Művelet leírása |
| :-- | :-- | :-- |
| Névtér | Microsoft. NotificationHubs/névterek/engedélyezési szabályok/művelet | Engedélyezési szabályok listázása |
| Névtér | Microsoft. NotificationHubs/névterek/engedélyezési szabályok/delete | Engedélyezési szabály törlése |
| Névtér | Microsoft. NotificationHubs/névterek/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | Kulcsok listázása |
| Névtér | Microsoft. NotificationHubs/névterek/engedélyezési szabályok/olvasás | Engedélyezési szabály lekérése |
| Névtér | Microsoft. NotificationHubs/névterek/engedélyezési szabályok/regenerateKeys/művelet | Kulcsok újragenerálása |
| Névtér | Microsoft. NotificationHubs/névterek/engedélyezési szabályok/írás | Engedélyezési szabály létrehozása vagy frissítése |
| Névtér | Microsoft. NotificationHubs/névterek/törlés | Névtér törlése |
| Névtér | Microsoft. NotificationHubs/névterek/olvasás | Névtér beolvasása |
| Névtér | Microsoft. NotificationHubs/névterek/írás | Névtér létrehozása vagy frissítése |
| Notification Hubs | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/művelet | Engedélyezési szabályok listázása |
| Notification Hubs | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/delete | Engedélyezési szabály törlése |
| Notification Hubs | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | Kulcsok listázása |
| Notification Hubs | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/READ | Engedélyezési szabály olvasása |
| Notification Hubs | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/regenerateKeys/művelet | Kulcsok újragenerálása |
| Notification Hubs | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/Write | Engedélyezési szabály létrehozása vagy frissítése |
| Notification Hubs | Microsoft. NotificationHubs/névterek/NotificationHubs/delete | Értesítési központ törlése |
| Notification Hubs | Microsoft. NotificationHubs/névterek/NotificationHubs/pnsCredentials/művelet | PNS hitelesítő adatainak létrehozása, frissítése vagy beolvasása |
| Notification Hubs | Microsoft. NotificationHubs/névterek/NotificationHubs/olvasás | Értesítési központ beolvasása |
| Notification Hubs | Microsoft. NotificationHubs/névterek/NotificationHubs/írás | Értesítési központ létrehozása vagy frissítése |

## <a name="enable-operational-logs"></a>Operatív naplók engedélyezése

Az operatív naplók alapértelmezés szerint le vannak tiltva. A naplók engedélyezéséhez tegye a következőket:

1. A [Azure Portal](https://portal.azure.com)nyissa meg az Azure Notification Hubs-névteret, majd a **figyelés** területen válassza a  **diagnosztikai beállítások** elemet.

   ![A "diagnosztikai beállítások" hivatkozás](./media/notification-hubs-diagnostic-logs/image-1.png)

1. A **diagnosztika beállításai** ablaktáblán válassza a **diagnosztikai beállítás hozzáadása** elemet.  

   ![A "diagnosztikai beállítás hozzáadása" hivatkozás](./media/notification-hubs-diagnostic-logs/image-2.png)

1. Konfigurálja a diagnosztikai beállításokat a következő módon:

   a. A **név** mezőbe írja be a diagnosztikai beállítások nevét.  

   b. Válasszon egyet a következő három célhely közül a diagnosztikai naplókhoz:  
   - Ha a **küldés log Analytics munkaterületre** lehetőséget választja, meg kell adnia a diagnosztika azon log Analytics példányát, amelybe a rendszer elküldi a diagnosztikát.  
   - Ha a **Storage-fiókba az Archive (archiválás**) lehetőséget választja, akkor konfigurálnia kell azt a Storage-fiókot, ahol a rendszer a diagnosztikai naplókat tárolja.  
   - Ha a stream elemet választja **egy Event hub-hoz**, akkor konfigurálnia kell azt az Event hub-t, amelybe a diagnosztikai naplókat továbbítani szeretné.

   c. Jelölje be a **OperationalLogs** jelölőnégyzetet.

    ![A "diagnosztikai beállítások" panel](./media/notification-hubs-diagnostic-logs/image-3.png)

1. Kattintson a **Mentés** gombra.

Az új beállítások körülbelül 10 percen belül lépnek érvénybe. A naplók a konfigurált archiválási célpontban, a **diagnosztikai naplók** panelen jelennek meg.

## <a name="next-steps"></a>Következő lépések

A diagnosztikai beállítások konfigurálásával kapcsolatos további tudnivalókért tekintse meg a következőt:
* [Az Azure Diagnostics-naplók áttekintése](../azure-monitor/essentials/platform-logs-overview.md).

Az Azure Notification Hubsról további információt a következő témakörben talál:
* [Mi az az Azure Notification Hubs?](notification-hubs-push-notification-overview.md)