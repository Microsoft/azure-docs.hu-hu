---
title: Az Azure cache for Redis Event Grid áttekintése
description: A Azure Event Grid használatával közzéteheti az Azure cache-t a Redis eseményeihez.
author: curib
ms.author: cauribeg
ms.date: 12/21/2020
ms.topic: conceptual
ms.service: cache
ms.openlocfilehash: 0a0809076367356739dfeadcf8dd63f88866a987
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056076"
---
# <a name="azure-cache-for-redis-event-grid-overview"></a>Az Azure cache for Redis Event Grid áttekintése 

A Redis-események, például a javítások, a skálázási, az importálási/exportálási (RDB) események Azure cache-t az előfizetők, például a Azure Functions, a Azure Logic Apps vagy akár a saját HTTP-figyelő számára is [leAzure Event Grid](https://azure.microsoft.com/services/event-grid/) küldik. A Event Grid megbízható esemény-kézbesítést biztosít az alkalmazásai számára a részletes újrapróbálkozási szabályzatok és a kézbesítetlen levelek használatával.

Az Azure cache for Redis által támogatott események teljes listájának megtekintéséhez tekintse meg az Azure cache [for Redis Events Schema](../event-grid/event-schema-azure-cache.md) cikkét.

Ha szeretné kipróbálni az Azure cache-t a Redis eseményeihez, tekintse meg az alábbi rövid útmutatók egyikét:

|Ha ezt az eszközt szeretné használni:    |Tekintse meg ezt a rövid útmutatót: |
|--|-|
|Azure Portal    |[Gyors útmutató: az Azure cache átirányítása a Redis-események webes végpontba Azure Portal](cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Gyors útmutató: az Azure cache átirányítása a Redis-események webes végponthoz a PowerShell használatával](cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[Gyors útmutató: az Azure cache átirányítása a Redis eseményeihez a webes végponthoz az Azure CLI-vel](cache-event-grid-quickstart-cli.md)|

## <a name="the-event-model"></a>Az esemény modellje

A Event Grid [esemény-előfizetések](../event-grid/concepts.md#event-subscriptions) használatával irányítja az üzeneteket az előfizetőknek. Ez a rendszerkép az esemény-közzétevők, az esemény-előfizetések és az eseménykezelők közötti kapcsolatot mutatja be.

:::image type="content" source="media/cache-event-grid/event-grid-model.png" alt-text="Event Grid-modell.":::

Először fizessen elő egy végpontot egy eseményre. Ezt követően az esemény indításakor a Event Grid szolgáltatás elküldi az eseményre vonatkozó információkat a végpontnak.

Tekintse meg az [Azure cache for Redis Events Schema](../event-grid/event-schema-azure-cache.md) című cikket a következő megtekintéséhez:

> [!div class="checklist"]
> * Az Azure cache teljes listája az események Redis és az egyes események aktiválásának módjáról.
> * Egy példa arra, amelyet a Event Grid az egyes eseményekhez küld.
> * Az egyes kulcs-érték párok célja, amely megjelenik az adatban.


## <a name="best-practices-for-consuming-events"></a>Ajánlott eljárások az események fogyasztásához

Az Azure cache-t Redis-események kezelésére szolgáló alkalmazásoknak néhány ajánlott eljárást kell követniük:
> [!div class="checklist"]
> * Mivel több előfizetést is konfigurálhat az események ugyanahhoz az eseménykezelőhöz való továbbításához, fontos, hogy ne feltételezzük, hogy az események egy adott forrásból származnak, de az üzenet témakörének ellenőrzésével győződjön meg arról, hogy az Azure cache a várt Redis-példányhoz tartozik.
> * Hasonlóképpen győződjön meg arról, hogy a eventType az egyik készen áll a feldolgozásra, és nem feltételezi, hogy az összes kapott esemény lesz a várt típus.
> * Az Azure cache a Redis-események esetében legalább egyszeri kézbesítést biztosít az előfizetőknek, ami biztosítja, hogy minden üzenet le legyen leképezve. Az újrapróbálkozások vagy az előfizetések rendelkezésre állása miatt azonban időnként előfordulhat, hogy ismétlődő üzenetek történnek. Ha többet szeretne megtudni az üzenetek kézbesítéséről, és próbálkozzon újra, tekintse meg [Event Grid üzenet kézbesítését, és próbálkozzon újra](../event-grid/delivery-and-retry.md).


## <a name="next-steps"></a>Következő lépések

Tudjon meg többet a Event Gridről, és adja meg az Azure cache-t a Redis eseményekhez:

- [Bevezetés az Event Grid használatába](../event-grid/overview.md)
- [Azure cache a Redis-események sémája számára](../event-grid/event-schema-azure-cache.md)
- [Az Azure cache átirányítása Redis-eseményekhez webes végpontra az Azure CLI-vel](cache-event-grid-quickstart-cli.md)
- [Az Azure cache átirányítása a Redis-események webes végpontba Azure Portal](cache-event-grid-quickstart-portal.md)
- [Az Azure cache átirányítása a Redis-események webes végponthoz a PowerShell használatával](cache-event-grid-quickstart-powershell.md)
