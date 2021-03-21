---
title: Rendszertémakörök a Azure Event Grid
description: A Azure Event Grid rendszertémaköreinek ismertetése.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: b1fbecb1e372602f9c252d43d2a1f93524ef1846
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052965"
---
# <a name="system-topics-in-azure-event-grid"></a>Rendszertémakörök a Azure Event Grid
A Event Grid rendszertémaköre egy vagy több olyan eseményt jelent, amelyet az Azure-szolgáltatások, például az Azure Storage és az Azure Event Hubs tesznek közzé. Előfordulhat például, hogy egy rendszertémakör az **összes blob-eseményt** vagy csak a **blob által létrehozott** és a blob által **törölt** , **adott Storage-fiók** számára közzétett eseményeket jelöli. Ebben a példában, amikor egy blobot töltenek fel a Storage-fiókba, az Azure Storage szolgáltatás **létrehoz egy blobot létrehozott** eseményt a Event grid rendszer témakörében, amely ezután továbbítja az eseményt a témakör azon [előfizetőknek](event-handlers.md) , akik megkapják és feldolgozzák az eseményt. 

> [!NOTE] 
> Csak az Azure-szolgáltatások tehetnek közzé eseményeket a rendszer témaköreiben. Ezért nem kap olyan végpontot vagy hozzáférési kulcsot, amelyet az egyéni témakörökhöz vagy tartományokhoz hasonló események közzétételére használhat.

## <a name="azure-services-that-support-system-topics"></a>Rendszertémaköröket támogató Azure-szolgáltatások
Itt látható az Azure-szolgáltatások aktuális listája, amelyek támogatják a rendszertémakörök létrehozását.

- [Azure App Configuration](event-schema-app-configuration.md)
- [Azure App Service](event-schema-app-service.md)
- [Azure Blob Storage](event-schema-blob-storage.md)
- [Azure Communication Services](event-schema-communication-services.md) 
- [Azure Container Registry](event-schema-container-registry.md)
- [Azure Event Hubs](event-schema-event-hubs.md)
- [Azure IoT Hub](event-schema-iot-hub.md)
- [Azure Key Vault](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Azure Maps](event-schema-azure-maps.md)
- [Azure Media Services](event-schema-media-services.md)
- [Azure-erőforráscsoportok](event-schema-resource-groups.md)
- [Azure Service Bus](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Azure-előfizetések](event-schema-subscriptions.md)
- [Azure Cache for Redis](event-schema-azure-cache.md)

## <a name="system-topics-as-azure-resources"></a>Rendszertémakörök Azure-erőforrásként
A múltban a rendszertémakör implicit volt, és nem volt elérhető az egyszerűség kedvéért. A rendszertémakörök mostantól Azure-erőforrásokként jelennek meg, és az alábbi képességeket biztosítják:

- [Rendszertémakörök megtekintése a Azure Portal](create-view-manage-system-topics.md#view-all-system-topics)
- Resource Manager-sablonok exportálása a rendszertémakörökhöz és az esemény-előfizetésekhez a Azure Portalban
- [Diagnosztikai naplók beállítása a rendszertémakörökhöz](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-system-topic)
- Riasztások beállítása közzétételi és kézbesítési hibák esetén 

## <a name="lifecycle-of-system-topics"></a>A rendszertémakörök életciklusa
A rendszertémakört kétféleképpen hozhatja létre: 

- Hozzon létre egy [esemény-előfizetést egy Azure-erőforráshoz kiterjesztési erőforrásként](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate), amely automatikusan létrehoz egy rendszertémakört a következő formátumban: `<Azure resource name>-<GUID>` . Az ily módon létrehozott rendszertémakör automatikusan törlődik, amikor a témakör utolsó esemény-előfizetését törli a rendszer. 
- Hozzon létre egy rendszertémakört az Azure-erőforrásokhoz, majd hozzon létre egy esemény-előfizetést az adott rendszertémakörhöz. Ha ezt a módszert használja, megadhatja a rendszer témakör nevét. A legutóbbi esemény-előfizetés törlésekor a rendszer nem törli automatikusan a rendszertémakört. Manuálisan kell törölnie. 

    A Azure Portal használatakor mindig ezt a módszert használja. Amikor [egy Azure-erőforrás **események** lapjával](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage)hoz létre egy esemény-előfizetést, a rendszer először létrehozza a rendszertémakört, majd létrehozza a témakör előfizetését. Az [ **Event Grid rendszertémakörök** lapon](create-view-manage-system-topics.md#create-a-system-topic) explicit módon létrehozhat egy rendszertémakört, majd létrehoz egy előfizetést az adott témakörhöz. 

A [CLI](create-view-manage-system-topics-cli.md), a [REST](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)vagy a [Azure Resource Manager sablon](create-view-manage-system-topics-arm.md)használatakor a fenti módszerek bármelyikét kiválaszthatja. Javasoljuk, hogy először hozzon létre egy rendszertémakört, majd hozzon létre egy előfizetést a témakörben, mivel ez a legújabb módszer a rendszertémakörök létrehozásához.

A rendszertémakör létrehozása sikertelen lesz, ha úgy állította be az Azure-szabályzatokat, hogy az Event Grid szolgáltatás nem tudja létrehozni. Például rendelkezhet egy olyan házirenddel, amely csak bizonyos típusú erőforrások (például az Azure Storage, az Azure Event Hubs stb.) létrehozását engedélyezi az előfizetésben. 

## <a name="location-and-resource-group-for-a-system-topic"></a>Rendszerbeli témakör helye és erőforráscsoport
Egy adott régióban/helyen található Azure-beli eseményforrás esetén a rendszertémakör ugyanazon a helyen jön létre, mint az Azure-esemény forrása. Ha például egy Azure Blob Storage-hoz hoz létre egy Event-előfizetést az USA keleti régiójában, akkor a rendszer témakör az USA keleti régiójában jön létre. A globális Azure-események, például az Azure-előfizetések, az erőforráscsoportok vagy a Azure Maps esetében a Event Grid a **globális** helyen hozza létre a rendszer témakört. 

Általánosságban elmondható, hogy a rendszertémakör ugyanabban az erőforráscsoportban jön létre, amelyben az Azure-esemény forrása található. Az Azure-előfizetések hatókörében létrehozott esemény-előfizetések esetén a System témakör az **USA 2. nyugati** régiójában, az **alapértelmezett EventGrid** erőforráscsoporthoz jön létre. Ha az erőforráscsoport nem létezik, Azure Event Grid létrehozza azt a rendszertémakör létrehozása előtt. 

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket: 

- [Rendszertémakörök létrehozása, megtekintése és kezelése Azure Portal használatával](create-view-manage-system-topics.md).
- [Event Grid rendszertémakörök létrehozása, megtekintése és kezelése az Azure CLI használatával](create-view-manage-system-topics-cli.md)
- [Event Grid rendszertémakörök létrehozása Azure Resource Manager sablonok használatával](create-view-manage-system-topics-arm.md)
