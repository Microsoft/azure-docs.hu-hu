---
title: Event Hubs eseményforrás hozzáadása – Azure Time Series Insights | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá Azure Event Hubs eseményforrás a Azure Time Series Insights-környezethez.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: ee66e68216933c410092865a1cdb781476a944c6
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103461134"
---
# <a name="add-an-event-hub-event-source-to-your-azure-time-series-insights-environment"></a>Event hub-eseményforrás hozzáadása a Azure Time Series Insights-környezethez

Ez a cikk azt ismerteti, hogyan használható a Azure Portal egy olyan eseményforrás hozzáadásához, amely adatokat olvas be az Azure Event Hubsból a Azure Time Series Insights környezetbe.

> [!NOTE]
> A cikkben ismertetett lépések a Azure Time Series Insights Gen 1 és a Azure Time Series Insights Gen 2 környezetekre egyaránt érvényesek.

## <a name="prerequisites"></a>Előfeltételek

- Hozzon létre egy Azure Time Series Insights környezetet a [Azure Time Series Insights-környezet létrehozása](./tutorial-set-up-environment.md)című témakörben leírtak szerint.
- Eseményközpont létrehozása. [A Azure Portal használatával olvassa el a Event Hubs névtér és az Event hub létrehozása című cikk utasításait](../event-hubs/event-hubs-create.md).
- Az Event hub-nak aktív üzenetküldési eseményekkel kell rendelkeznie. Ismerje meg, hogyan [küldhet eseményeket az Azure Event Hubs a .NET-keretrendszer használatával](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Hozzon létre egy dedikált fogyasztói csoportot az Event hub-ban, amelyet a Azure Time Series Insights-környezet képes használni. Minden Azure Time Series Insights eseményforrás saját dedikált fogyasztói csoportjának kell lennie, amely nincs más fogyasztóval megosztva. Ha több olvasó is felhasználja az azonos fogyasztói csoportból származó eseményeket, az összes olvasó valószínűleg hibákat fog kimutatni. Az Event hub-ban legfeljebb 20 fogyasztói csoport lehet. Részletekért olvassa el a [Event Hubs programozási útmutatót](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Fogyasztói csoport hozzáadása az Event hub-hoz

Az alkalmazások fogyasztói csoportokat használnak az adatok Azure-Event Hubsból való lekéréséhez. Az Event hub adatainak megbízható beolvasásához adjon meg egy dedikált fogyasztói csoportot, amelyet csak ez a Azure Time Series Insights-környezet használ.

Új fogyasztói csoport hozzáadása az Event hub-ban:

1. A [Azure Portal](https://portal.azure.com)keresse meg és nyissa meg az Event hub-példányt az Event hub-névtér **Áttekintés** paneljén. Válassza az **entitások > Event Hubs** lehetőséget, vagy keresse meg a példány **nevét**.

    [![Az Event hub-névtér megnyitása](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. Az Event hub-példányban válassza az **entitások > fogyasztói csoportok** lehetőséget. Ezután válassza a **+ fogyasztói csoport** lehetőséget egy új fogyasztói csoport hozzáadásához.

   [![Event hub – fogyasztói csoport hozzáadása](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   Ellenkező esetben válasszon ki egy meglévő fogyasztói csoportot, és ugorjon a következő szakaszra.

1. A **fogyasztói csoportok** lapon adjon meg egy új egyedi értéket a **név** mezőben.  Akkor használja ezt a nevet, amikor új eseményforrás hoz létre a Azure Time Series Insights környezetben.

1. Válassza a **Létrehozás** lehetőséget.

## <a name="add-a-new-event-source"></a>Új eseményforrás hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keresse meg a meglévő Azure Time Series Insights-környezetét. A bal oldali menüben válassza a **minden erőforrás** lehetőséget, majd válassza ki a Azure Time Series Insights-környezetet.

1. Válassza az **eseményforrás** lehetőséget, majd kattintson a **Hozzáadás** gombra.

   [![Az eseményforrás területen válassza a Hozzáadás gombot](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Adjon meg egy értéket az **eseményforrás neve** számára, amely egyedi a Azure Time Series Insights környezetben, például: `Contoso-TSI-Gen 1-Event-Hub-ES` .

1. A **forrás** mezőben válassza az **Event hub** elemet.

1. Válassza ki a megfelelő értékeket az **importálási beállításhoz**:

   - Ha az egyik előfizetésében van egy meglévő Event hub, válassza az **Event hub használata az elérhető előfizetések közül** lehetőséget. Ez a lehetőség a legegyszerűbb megközelítés.

     [![Válasszon egy eseményforrás-importálási lehetőséget](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

   - A következő táblázat az **Event hub elérhető előfizetések** lehetőségből való használatának kötelező tulajdonságait ismerteti:

       [![Az előfizetés és az Event hub részletei](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Tulajdonság | Leírás |
       | --- | --- |
       | Előfizetés | Az előfizetés, amelyhez a kívánt Event hub-példány és névtér tartozik. |
       | Event Hubs-névtér | Az Event hub-névtér a kívánt Event hub-példányhoz tartozik. |
       | Eseményközpont neve | A kívánt Event hub-példány neve. |
       | Event hub-házirend értéke | Válassza ki a kívánt megosztott hozzáférési szabályzatot. A megosztott elérési szabályzatot az Event hub configure (esemény központ **konfigurálása** ) lapon lehet létrehozni. Minden megosztott hozzáférési házirend rendelkezik egy névvel, a beállított engedélyekkel és a hozzáférési kulcsokkal. Az eseményforrás megosztott hozzáférési házirendjének **olvasási** engedélyekkel *kell* rendelkezniük. |
       | Event hub-házirend kulcsa | Előre kitöltve a kiválasztott Event hub-házirend értékéről. |

   - Ha az Event hub kívül esik az előfizetéseken, vagy ha a speciális beállításokat szeretné kiválasztani, válassza az **Event hub-beállítások manuális megadása** lehetőséget.

       Az alábbi táblázat ismerteti az **Event hub-beállítások manuális** megadása beállítás szükséges tulajdonságait:

       | Tulajdonság | Leírás |
       | --- | --- |
       | Előfizetés azonosítója | Az előfizetés, amelyhez a kívánt Event hub-példány és névtér tartozik. |
       | Erőforráscsoport | Az az erőforráscsoport, amelyhez a kívánt Event hub-példány és névtér tartozik. |
       | Event Hubs-névtér | Az Event hub-névtér a kívánt Event hub-példányhoz tartozik. |
       | Eseményközpont neve | A kívánt Event hub-példány neve. |
       | Event hub-házirend értéke | Válassza ki a kívánt megosztott hozzáférési szabályzatot. A megosztott elérési szabályzatot az Event hub configure (esemény központ **konfigurálása** ) lapon lehet létrehozni. Minden megosztott hozzáférési házirend rendelkezik egy névvel, a beállított engedélyekkel és a hozzáférési kulcsokkal. Az eseményforrás megosztott hozzáférési házirendjének **olvasási** engedélyekkel *kell* rendelkezniük. |
       | Event hub-házirend kulcsa | A Service Bus névtér hozzáférésének hitelesítéséhez használt megosztott elérési kulcs. Itt adhatja meg az elsődleges vagy a másodlagos kulcsot. |

   - Mindkét beállítás a következő konfigurációs beállításokat használja:

       | Tulajdonság | Leírás |
       | --- | --- |
       | Eseményközpont fogyasztói csoportja | Az Event hub eseményeinek olvasására szolgáló fogyasztói csoport. Javasoljuk, hogy használjon egy dedikált fogyasztói csoportot az eseményforrás számára. |
       | Eseményszerializációs formátum | Jelenleg a JSON az egyetlen elérhető szerializálási formátum. Az esemény üzeneteinek ebben a formátumban kell lenniük, vagy nem olvashatók be az adatforrások. |
       | Időbélyeg-tulajdonság neve | Az érték meghatározásához meg kell ismernie az Event hub számára küldött üzenet-adatformátumot. Ez az érték annak az üzenetnek a **neve** , amelyet az esemény időbélyegzőként használni kíván. Az érték megkülönbözteti a kis-és nagybetűket. Ha üresen hagyja, a rendszer az eseményforrás **sorba helyezni időt** használja az esemény időbélyegzője. |

1. Adja hozzá az Event hub-hoz hozzáadott dedikált Azure Time Series Insights fogyasztói csoport nevét.

1. Válassza a **Létrehozás** lehetőséget.

   Az eseményforrás létrehozása után Azure Time Series Insights automatikusan megkezdi az adatátvitelt a környezetbe.

## <a name="next-steps"></a>Következő lépések

- [Adathozzáférési szabályzatok definiálása](./concepts-access-policies.md) az adatvédelme érdekében.

- [Események küldése](time-series-insights-send-events.md) az esemény forrásának.

- A környezet elérése a [Azure Time Series Insights Explorerben](https://insights.timeseries.azure.com).
