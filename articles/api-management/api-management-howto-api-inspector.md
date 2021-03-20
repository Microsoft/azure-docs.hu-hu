---
title: Oktatóanyag – API-k hibakeresése az Azure-ban API Management a kérelmek nyomon követésével
description: Az oktatóanyag lépéseit követve engedélyezheti a nyomkövetést és a kérelmek feldolgozási lépéseit az Azure API Managementban.
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: 531e346569b85ababc382f997fd7764a92b3d05f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542314"
---
# <a name="tutorial-debug-your-apis-using-request-tracing"></a>Oktatóanyag: API-k hibakeresése kérelmek nyomkövetésének használatával

Ez az oktatóanyag leírja, hogyan vizsgálja meg a kérelmek feldolgozását az Azure API Managementban az API-k hibakereséséhez és hibaelhárításához. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Példa egy hívás nyomon követésére
> * Kérelmek feldolgozási lépéseinek áttekintése

:::image type="content" source="media/api-management-howto-api-inspector/api-inspector-001.png" alt-text="API-vizsgáló":::

## <a name="prerequisites"></a>Előfeltételek

+ Az [Azure API Management terminológiájának](api-management-terminology.md) ismerete.
+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
+ Hajtsa végre a következő oktatóanyagot: az [első API importálása és közzététele](import-and-publish.md).

## <a name="verify-allow-tracing-setting"></a>Nyomkövetés engedélyezése beállítás ellenőrzése 

Az API-hoz használt előfizetés **nyomkövetésének engedélyezése** beállítást engedélyezni kell. Ha a beépített összes előfizetést használja, az alapértelmezés szerint engedélyezve van. A portálon való ellenőrzéshez navigáljon a API Management-példányhoz, és válassza az **előfizetések** lehetőséget.

   :::image type="content" source="media/api-management-howto-api-inspector/allow-tracing.png" alt-text="Előfizetés nyomkövetésének engedélyezése":::

## <a name="trace-a-call"></a>Hívás nyomon követése

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és navigáljon a API Management-példányhoz.
1. Válassza az **API-k** lehetőséget.
1. Válassza ki a  **bemutató konferencia API** -t az API-listából.
1. Kattintson a **Teszt** fülre.
1. Válassza a **GetSpeakers** műveletet.
1. Győződjön meg arról, hogy a HTTP-kérelem fejléce tartalmazza a **OCP-APIM-Trace: true** értéket, valamint a **OCP-APIM-Subscription-Key** érvényes értékét. Ha nem, válassza a **+ fejléc hozzáadása** lehetőséget a fejléc hozzáadásához.
1. Az API-hívás létrehozásához válassza a **Küldés** lehetőséget.

  :::image type="content" source="media/api-management-howto-api-inspector/06-debug-your-apis-01-trace-call.png" alt-text="API-nyomkövetés konfigurálása":::

> [!TIP]
> Ha a **OCP-APIM-előfizetés-Key** nincs automatikusan kitöltve a http-kérelemben, lekérheti azt a portálon. Válassza az **előfizetések** lehetőséget, majd nyissa meg a suscription tartozó helyi menüt (**..**.). Válassza a **kulcsok megjelenítése/elrejtése** lehetőséget. Szükség esetén a kulcsok újragenerálása is megadható. Ezután adjon hozzá egy kulcsot a fejléchez.

## <a name="review-trace-information"></a>Nyomkövetési adatok áttekintése

1. A hívás befejezése után a **http-válasz** **nyomkövetés** lapján lépjen a következőre:.
1. A következő hivatkozások bármelyikét kiválasztva a részletes nyomkövetési információkra ugorhat: **bejövő**, **háttér**, **kimenő**.

     :::image type="content" source="media/api-management-howto-api-inspector/response-trace.png" alt-text="Válasz nyomkövetésének áttekintése":::

    * **Bejövő** – a hívótól kapott eredeti kérés API Management és a kérésre alkalmazott szabályzatok megjelenítése. Ha például hozzáadta a szabályzatokat az [oktatóanyagban: alakítsa át és óvja az API](transform-api.md)-t, itt jelennek meg.

    * **Háttér** – MEGJELENÍTI az API-háttérnek és a kapott válasznak API Management küldött kéréseket.

    * **Kimenő** – a válaszra alkalmazott házirendeket jeleníti meg a hívónak való visszaküldés előtt.

    > [!TIP]
    > Az egyes lépések a hívás az API Management által való fogadása óta eltelt időt is mutatják.

1. Az **üzenet** lapon a **OCP-APIM-Trace-Location** fejléc az Azure Blob Storage-ban tárolt nyomkövetési adat helyét jeleníti meg. Ha szükséges, lépjen erre a helyre a nyomkövetés lekéréséhez.

     :::image type="content" source="media/api-management-howto-api-inspector/response-message.png" alt-text="Nyomkövetési hely az Azure Storage-ban":::
## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Példa egy hívás nyomon követésére
> * Kérelmek feldolgozási lépéseinek áttekintése

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Változatok használata](api-management-get-started-revise-api.md)
