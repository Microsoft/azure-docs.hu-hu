---
title: Azure Event Grid – diagnosztikai naplók engedélyezése témakörökhöz vagy tartományokhoz
description: Ez a cikk lépésről lépésre bemutatja, hogyan engedélyezheti a diagnosztikai naplókat egy Azure Event Grid-témakörben.
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: ff00c1438c49cbc9f9e67eba0cf0acef7991a5a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576451"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>Diagnosztikai naplók engedélyezése az Azure Event Grid-témakörökhöz vagy-tartományokhoz
Ez a cikk részletes útmutatást nyújt a Event Grid témakörök vagy tartományok diagnosztikai beállításainak engedélyezéséhez.  Ezekkel a beállításokkal rögzítheti és megtekintheti a **közzétételi és a kézbesítési hibák** naplóit. 

## <a name="prerequisites"></a>Előfeltételek

- Kiépített Event Grid-témakör
- A diagnosztikai naplók rögzítésére szolgáló kiépített cél. A következő célpontok egyike lehet ugyanazon a helyen, mint az Event Grid-témakör:
    - Azure Storage-fiók
    - Eseményközpont
    - Log Analytics-munkaterület

## <a name="enable-diagnostic-logs-for-a-custom-topic"></a>Diagnosztikai naplók engedélyezése egyéni témakörhöz

> [!NOTE]
> A következő eljárás részletes útmutatást nyújt a diagnosztikai naplók engedélyezéséhez a témakörben. A tartomány diagnosztikai naplói engedélyezésének lépései nagyon hasonlóak. A 2. lépésben lépjen a Azure Portal Event Grid- **tartományára** .  

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Lépjen arra az Event Grid-témakörre, amelyre vonatkozóan engedélyezni kívánja a diagnosztikai napló beállításait. 
    1. A felső keresési sávban keresse meg **Event Grid témaköröket**. 
    
        ![Egyéni témakörök keresése](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    1. Válassza ki a listából azokat a **témákat** , amelyekhez diagnosztikai beállításokat szeretne konfigurálni. 
1. A bal oldali menüben válassza a **diagnosztikai beállítások** lehetőséget a **figyelés** elemnél.
1. A **diagnosztikai beállítások** lapon válassza az **új diagnosztikai beállítás hozzáadása** elemet. 
    
    ![Diagnosztikai beállítások hozzáadása gomb](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Adja meg a diagnosztikai beállítás **nevét** . 
6. Válassza ki a **DeliveryFailures** és a **PublishFailures** beállításokat a **napló** szakaszban. 
    ![A hibák kiválasztása](./media/enable-diagnostic-logs-topic/log-failures.png)
7. Engedélyezzen egy vagy több rögzítési célhelyet a naplókhoz, majd konfigurálja őket úgy, hogy kiválasztja az előző létrehozott rögzítési erőforrást. 
    - Ha kijelöli az **archiválás egy Storage-fiókba** lehetőséget, válassza a **Storage-fiók konfigurálása** lehetőséget, majd válassza ki a Storage-fiókot az Azure-előfizetésében. 

        ![Képernyőkép: "diagnosztikai beállítások" oldal, "Archive to a Azure Storage-fiók", és egy kiválasztott Storage-fiók.](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Ha **az adatfolyam lehetőséget választja egy Event hub**-ra, válassza az **Event hub-configure** lehetőséget, majd válassza ki a Event Hubs névteret, az Event hub és a hozzáférési házirendet. 
        ![A "diagnosztikai beállítások" lapot, amely az "adatfolyam az Event hubhoz" jelölőnégyzetet mutatja be.](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Ha a **küldés log Analytics** lehetőséget választja, válassza ki a log Analytics munkaterületet.
        ![A "Küldés Log Analyticsba" jelölőnégyzettel rendelkező "diagnosztikai beállítások" oldalt megjelenítő képernyőkép.](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. Kattintson a **Mentés** gombra. Ezután a jobb oldali sarokban található **X** gombra kattintva zárhatja be a lapot. 
9. Most vissza a **diagnosztikai beállítások** lapon ellenőrizze, hogy megjelenik-e új bejegyzés a **diagnosztikai beállítások** táblázatban. 
    ![Képernyőkép: a "diagnosztikai beállítások" oldal, amely egy új bejegyzéssel jelenik meg a "diagnosztikai beállítások" táblában.](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     A témakör összes mérőszámának gyűjteményét is engedélyezheti. 

## <a name="enable-diagnostic-logs-for-a-system-topic"></a>Diagnosztikai naplók engedélyezése rendszertémakörhöz

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Lépjen arra az Event Grid-témakörre, amelyre vonatkozóan engedélyezni kívánja a diagnosztikai napló beállításait. 
    1. A felső keresési sávban keresse meg **Event Grid Rendszertémaköröket**. 
    
        ![Rendszertémakörök keresése](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. Válassza ki azt a **rendszertémakört** , amelyre vonatkozóan be szeretné állítani a diagnosztikai beállításokat. 
    
        ![Rendszertémakör kiválasztása](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. **A bal** oldali menüben válassza a **diagnosztika** lehetőséget, majd válassza a **diagnosztikai beállítás hozzáadása** lehetőséget. 

    ![Diagnosztikai beállítások hozzáadása – gomb](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. Adja meg a diagnosztikai beállítás **nevét** . 
7. Válassza ki a **DeliveryFailures** a **napló** szakaszban. 
    ![Kézbesítési hibák kiválasztása](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. Engedélyezzen egy vagy több rögzítési célhelyet a naplókhoz, majd konfigurálja őket úgy, hogy kiválasztja az előző létrehozott rögzítési erőforrást. 
    - Ha a **küldés log Analytics** lehetőséget választja, válassza ki a log Analytics munkaterületet.
        ![Küldés a Log Analyticsnek](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png) 
    - Ha kijelöli az **archiválás egy Storage-fiókba** lehetőséget, válassza a **Storage-fiók konfigurálása** lehetőséget, majd válassza ki a Storage-fiókot az Azure-előfizetésében. 

        ![Archiválás Azure Storage-fiókba](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - Ha **az adatfolyam lehetőséget választja egy Event hub**-ra, válassza az **Event hub-configure** lehetőséget, majd válassza ki a Event Hubs névteret, az Event hub és a hozzáférési házirendet. 
        ![Streamelés eseményközpontba](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
8. Kattintson a **Mentés** gombra. Ezután a jobb oldali sarokban található **X** gombra kattintva zárhatja be a lapot. 
9. Most vissza a **diagnosztikai beállítások** lapon ellenőrizze, hogy megjelenik-e új bejegyzés a **diagnosztikai beállítások** táblázatban. 
    ![Diagnosztikai beállítás a listában](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

     Emellett engedélyezheti a rendszertémakör összes **mérőszámának** gyűjteményét is.

    ![Rendszertémakör – az összes metrika engedélyezése](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>Diagnosztikai naplók megtekintése az Azure Storage-ban 

1. Miután engedélyezte a Storage-fiók rögzítési célhelyét, Event Grid elindítja a diagnosztikai naplók kibocsátását. Meg kell jelennie az elemzések nevű új tárolóknak – **naplók – deliveryfailures** és elemzések – **naplók – publishfailures** a Storage-fiókban. 

    ![Storage – tárolók a diagnosztikai naplókhoz](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. Ahogy az egyik tárolóban navigál, a rendszer a blobot JSON formátumban fogja végrehajtani. A fájl kézbesítési hiba vagy közzétételi hiba esetén bejegyzéseket tartalmaz. A navigációs útvonal az Event Grid-témakör **ResourceId** és az időbélyeg (perc szint), amely a naplóbejegyzések kibocsátásának időpontját jelöli. A letölthető blob-vagy JSON-fájl a végén betartja a következő szakaszban ismertetett sémát. 

    [![A tárolóban ](./media/enable-diagnostic-logs-topic/select-json.png) található JSON-fájl](./media/enable-diagnostic-logs-topic/select-json.png)
3. A JSON-fájlban a következő példához hasonló tartalomnak kell megjelennie: 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```
## <a name="next-steps"></a>Következő lépések
A naplózási sémával és a témakörök vagy tartományok diagnosztikai naplóival kapcsolatos egyéb fogalmi információkkal kapcsolatban lásd: [diagnosztikai naplók](diagnostic-logs.md).
