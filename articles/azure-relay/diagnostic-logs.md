---
title: Diagnosztikai naplók a Hibrid kapcsolatokhoz
description: Ez a cikk a Azure Relay számára elérhető összes tevékenység-és diagnosztikai napló áttekintését tartalmazza.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 9b459750ad1445da89a8e89a10a35b878bfb64e1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590878"
---
# <a name="enable-diagnostics-logs-for-azure-relay-hybrid-connections"></a>Diagnosztikai naplók engedélyezése Azure Relay Hibrid kapcsolatok
A Azure Relay Hibrid kapcsolatok használatának megkezdése után érdemes figyelni, hogy a figyelők és a küldők hogyan és mikor legyenek megnyitva és lezárva, valamint hogyan történik a Hibrid kapcsolatok létrehozása és az üzenetek küldése. Ez a cikk áttekintést nyújt a Azure Relay szolgáltatás által biztosított tevékenység-és diagnosztikai naplókról. 

A Azure Relay kétféle naplót tekinthet meg:

- [Tevékenységnaplók](../azure-monitor/essentials/platform-logs-overview.md): ezek a naplók információt biztosítanak a névtéren végrehajtott műveletekről a Azure Portal vagy Azure Resource Manager sablonnal. Ezek a naplók mindig engedélyezve vannak. Például: "névtér létrehozása vagy frissítése", "hibrid kapcsolatok létrehozása vagy frissítése". 
- [Diagnosztikai naplók](../azure-monitor/essentials/platform-logs-overview.md): a diagnosztikai naplókat úgy konfigurálhatja, hogy az API vagy a Language SDK használatával megjelenő műveletekkel és műveletekkel kapcsolatos összes műveletet megtekintse.

## <a name="view-activity-logs"></a>Tevékenységnaplók megtekintése
Ha meg szeretné tekinteni a Azure Relay névtérhez tartozó tevékenységek naplóit, váltson a Azure Portal **tevékenység napló** lapjára.

![Azure Relay – tevékenység naplója](./media/diagnostic-logs/activity-log.png)

## <a name="enable-diagnostic-logs"></a>Diagnosztikai naplók engedélyezése

> [!NOTE]
> A diagnosztikai naplók csak Hibrid kapcsolatok esetén érhetők el, Windows Communication Foundation (WCF) továbbítóhoz nem.

A diagnosztikai naplók engedélyezéséhez hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com)nyissa meg a Azure Relay névteret, majd a **figyelés** területen válassza a  **diagnosztikai beállítások** elemet.
1. A diagnosztikai **Beállítások** lapon válassza a **diagnosztikai beállítás hozzáadása** elemet.  

   ![A "diagnosztikai beállítás hozzáadása" hivatkozás](./media/diagnostic-logs/add-diagnostic-setting.png)

1. Konfigurálja a diagnosztikai beállításokat a következő lépések végrehajtásával:
    1. A **név** mezőbe írja be a diagnosztikai beállítások nevét.  
    2. Válassza a **HybridConnectionsEvent** lehetőséget a napló típusához. 
    3. Válasszon egyet a következő három **célhely** közül a diagnosztikai naplókhoz:  
        1. Ha a **Storage-fiókba az Archive (archiválás**) lehetőséget választja, akkor konfigurálja azt a Storage-fiókot, ahol a rendszer a diagnosztikai naplókat tárolja.  
        2. Ha a stream elemet választja **egy Event hub**-ra, konfigurálja azt az Event hub-t, amelyre a diagnosztikai naplókat továbbítani kívánja.
        3. Ha a **küldés log Analytics** lehetőséget választja, adja meg, hogy a diagnosztika melyik log Analytics példányát fogja elküldeni a rendszer.  

        ![Diagnosztikai beállítások](./media/diagnostic-logs/sample-diagnostic-settings.png)
1. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra az eszköztáron.

Az új beállítások körülbelül 10 percen belül lépnek érvénybe. A naplók a konfigurált archiválási célpontban, a **diagnosztikai naplók** panelen jelennek meg. A diagnosztikai beállítások konfigurálásával kapcsolatos további információkért tekintse meg az [Azure Diagnostics-naplók áttekintését](../azure-monitor/essentials/platform-logs-overview.md).


## <a name="schema-for-hybrid-connections-events"></a>Séma hibrid kapcsolati eseményekhez
A hibrid kapcsolatok eseménynaplójának JSON-karakterláncai közé tartoznak az alábbi táblázatban felsorolt elemek:

| Név | Leírás |
| ------- | ------- |
| ResourceId | Erőforrás-azonosító Azure Resource Manager |
| Tevékenységazonosító | A megadott művelet azonosítására szolgáló belső azonosító. A "TrackingId" néven is ismert |
| Végpont | A továbbító erőforrás címe |
| OperationName | A naplózott Hibrid kapcsolatok művelet típusa |
| EventTimeString | A napló-rekord UTC-időbélyege |
| Üzenet | Az esemény részletes üzenete |
| Kategória | Az esemény kategóriája. Jelenleg csak `HybridConnectionsEvents` . 


## <a name="sample-hybrid-connections-event"></a>Hibrid kapcsolati esemény mintája
Itt látható egy hibrid kapcsolati esemény JSON formátumban. 

```json
{
    "resourceId": "/SUBSCRIPTIONS/0000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/MyResourceGroup/PROVIDERS/MICROSOFT.RELAY/NAMESPACES/MyRelayNamespace",
    "ActivityId": "7006a0db-27eb-445c-939b-ce86133014cc",
    "endpoint": "sb://myrelaynamespace.servicebus.windows.net/myhybridconnection/7006a0db-27eb-445c-939b-ce86133014cc_G5",
    "operationName": "Microsoft.Relay/HybridConnections/NewSenderRegistering",
    "EventTimeString": "2020-04-27T20:27:57.3842810Z",
    "message": "A new sender is registering.",
    "category": "HybridConnectionsEvent"
}
```

## <a name="events-and-operations-captured-in-diagnostic-logs"></a>A diagnosztikai naplókban rögzített események és műveletek

| Művelet | Description | 
| --------- | ----------- | 
| AuthorizationFailed | Az engedélyezés sikertelen.|
| InvalidSasToken | Érvénytelen SAS-jogkivonat. | 
| ListenerAcceptingConnection | A figyelő fogadja a kapcsolatokat. |
| ListenerAcceptingConnectionTimeout | Túllépte az időkorlátot, ha a figyelő elfogadja a kapcsolatokat. |
| ListenerAcceptingHttpRequestFailed | Egy kivétel miatt sikertelen volt a HTTP-kérelem elfogadását kérő figyelő. |
| ListenerAcceptingRequestTimeout | Túllépte a kérelem elfogadását kérő figyelő időkorlátját. |  
| ListenerClosingFromExpiredToken | A figyelő bezárul, mert a biztonsági jogkivonat lejárt. | 
| ListenerRejectedConnection | A figyelő elutasította a kapcsolatokat. |
| ListenerReturningHttpResponse | A figyelő HTTP-választ ad vissza. |  
| ListenerReturningHttpResponseFailed | A figyelő egy hibakódtal rendelkező HTTP-választ ad vissza. | 
 ListenerSentHttpResponse | A Relay szolgáltatás HTTP-választ kapott a figyelőtől. | 
| ListenerUnregistered | A figyelő regisztrációja megszüntetve. | 
| ListenerUnresponsive | A figyelő válasz küldésekor nem válaszol. | 
| MessageSendingToListener | Üzenet küldése a figyelőnek. |
| MessageSentToListener | A rendszer elküldi az üzenetet a figyelőnek. | 
| NewListenerRegistered | Új figyelő regisztrálva. |
| NewSenderRegistering | Új feladó regisztrálása. | 
| ProcessingRequestFailed | A hibrid kapcsolatok műveletének feldolgozása sikertelen volt. | 
| SenderConnectionClosed | A küldő kapcsolódása bezárult. |
| SenderListenerConnectionEstablished | A küldő és a figyelő sikeresen létrehozta a kapcsolódást. |
| SenderSentHttpRequest | A küldő HTTP-kérelmet küld. | 


## <a name="next-steps"></a>Következő lépések

A Azure Relayról további információt a következő témakörben talál:

* [Bevezetés a Azure Relayba](relay-what-is-it.md)
* [Ismerkedés a hibrid Relay-kapcsolatokkal](relay-hybrid-connections-dotnet-get-started.md)
