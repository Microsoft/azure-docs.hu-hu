---
title: Azure Media Services eseményekre való reagálás
description: Ez a cikk azt ismerteti, hogyan használható a Azure Event Grid az Media Services eseményekre való előfizetéshez.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0d4479914ffee6cf667a5f6db2fd665baf2b857c
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895645"
---
# <a name="handling-event-grid-events"></a>Event Grid-események kezelése

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services események lehetővé teszik az alkalmazások számára a különböző eseményekre való reagálást (például a feladatok állapotának változási eseményét) a modern kiszolgáló nélküli architektúrák használatával. Ehhez nincs szükség bonyolult programkódra vagy költséges és nem hatékony lekérdezési szolgáltatásokra. Ehelyett az eseményeket [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) az eseménykezelők, például a [Azure Functions](https://azure.microsoft.com/services/functions/), a [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/), vagy akár a saját webhook számára, és csak azért kell fizetnie, amit ténylegesen használ. További információ a díjszabásról: [Event Grid díjszabása](https://azure.microsoft.com/pricing/details/event-grid/).

Media Services események rendelkezésre állása Event Grid [rendelkezésre álláshoz](../../event-grid/overview.md) kötődik, és más régiókban is elérhetővé válik, mint Event Grid.  

## <a name="media-services-events-and-schemas"></a>Események és sémák Media Services

Az Event Grid [esemény-előfizetések](../../event-grid/concepts.md#event-subscriptions) használatával irányítja az esemény-üzeneteket az előfizetőknek. Media Services események tartalmazzák az adatok változásaira való válaszadáshoz szükséges összes információt. Azonosíthatja Media Services eseményt, mert a eventType tulajdonság a "Microsoft. Media" karakterlánccal kezdődik.

További információ: [Media Services esemény sémái](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Az események felhasználásának eljárásai

Az Media Services eseményeket kezelő alkalmazásoknak néhány ajánlott gyakorlatot követniük kell:

* Mivel több előfizetést is konfigurálhat az események ugyanahhoz az eseménykezelőhöz való átirányításához, fontos, hogy ne feltételezzük, hogy az események egy adott forrásból származnak, de az üzenet témakörének ellenőrzésével győződjön meg arról, hogy a várt Storage-fiókból származik.
* Hasonlóképpen győződjön meg arról, hogy a eventType az egyik készen áll a feldolgozásra, és nem feltételezi, hogy az összes kapott esemény lesz a várt típus.
* Figyelmen kívül hagyhatja a nem értelmezhető mezőket.  Ez a gyakorlat segít megőrizni a jövőben esetlegesen hozzáadott új funkciókkal való ellenálló képességet.
* Az események adott eseményre való korlátozásához használja a "tárgy" előtagot és utótagot.

> [!NOTE]
> Az események a Event Grid [szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/)hatálya alá esnek. Ha API-kat használó esemény-értesítéseket szeretne kapni, tekintse meg a példákat az események használatáról a [.net SDK](https://github.com/Azure-Samples/media-services-v3-dotnet) -val vagy a [Java SDK](https://github.com/Azure-Samples/media-services-v3-java)-val.

## <a name="next-steps"></a>Következő lépések

* [Események figyelése – portál](monitor-events-portal-how-to.md)
* [Események monitorozása – Parancssori felület](job-state-events-cli-how-to.md)
