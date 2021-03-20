---
title: Az Azure Functions áttekintése
description: Ismerje meg, hogyan hozhat létre Azure Functions robusztus, kiszolgáló nélküli alkalmazásokat.
author: craigshoemaker
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 11/20/2020
ms.author: cshoe
ms.custom: contperf-fy21q2
ms.openlocfilehash: 41b627259d84539c868f95eb3cf33db5dbdab52c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97862403"
---
# <a name="introduction-to-azure-functions"></a>Az Azure Functions bemutatása

A Azure Functions kiszolgáló nélküli megoldás, amely lehetővé teszi kevesebb kód írására, kevesebb infrastruktúra fenntartására és költségek megtakarítására. Ahelyett, hogy a kiszolgálók üzembe helyezésével és karbantartásával kellene foglalkoznia, a felhőalapú infrastruktúra biztosítja az alkalmazások futtatásához szükséges összes naprakész erőforrást.

Így Ön a legfontosabb kódolási feladatokra koncentrálhat, a többit pedig az Azure Functionsre bízhatja.<br /><br />

> [!VIDEO https://www.youtube.com/embed/8-jz5f_JyEQ]

Gyakran olyan rendszereket hozunk létre, amelyek kritikus események sorozatára reagálnak. Legyen szó webes API-k létrehozásáról, az adatbázis-változásokra való reagálásról, a IoT-adatfolyamok feldolgozásáról vagy az üzenetsor kezeléséről – minden alkalmazásnak szüksége van egy kód futtatására, mivel ezek az események történnek.

Ennek a szükségletnek a kielégítése érdekében a Azure Functions két jelentős módon biztosítja a "számítás igény szerint" lehetőséget.

Első lépésként a Azure Functions lehetővé teszi, hogy a rendszerek logikáját könnyen elérhető kódrészletbe implementálja. Ezeket a kódrészleteket "functions"-nek nevezzük. A különböző függvények bármikor futtathatók, ha a kritikus eseményekre kell válaszolnia.

Másodszor, ahogy a kérelmek száma nő, Azure Functions megfelel az igénynek annyi erőforrást és függvényt, mint amennyi szükséges, de csak szükség esetén. A kérelmek bukása esetén minden további erőforrás és alkalmazás-példány automatikusan kiesik.

Honnan származnak az összes számítási erőforrás? A Azure Functions az alkalmazás igényének kielégítése érdekében annyi [vagy kevés számítási erőforrást biztosít](./functions-scale.md) , amennyire csak szüksége van.

A számítási erőforrások igény szerinti biztosítása a [kiszolgáló nélküli számítástechnika](https://azure.microsoft.com/solutions/serverless/) lényege Azure Functionsban.

## <a name="scenarios"></a>Forgatókönyvek

Számos esetben a függvények a [Cloud Services egy tömbjét integrálják](./functions-triggers-bindings.md) a funkciókban gazdag implementációk biztosításához.

Az alábbiakban gyakoriak, _de nem teljes körűen_ a Azure functions forgatókönyvek.

| Ha szeretné... | Ezután... |
| --- | --- |
| **Webes API létrehozása** | Végpont implementálása a webalkalmazásokhoz a [http-trigger](./functions-bindings-http-webhook.md) használatával |
| **Fájlfeltöltés feldolgozása** | Kód futtatása a [blob Storage](./functions-bindings-storage-blob.md) -beli fájlok feltöltésekor vagy módosításakor |
| **Kiszolgáló nélküli munkafolyamat létrehozása** | Függvények egy sorozatának láncolása [tartós függvények](./durable/durable-functions-overview.md) használatával |
| **Válaszadás az adatbázis változásaira** | Egyéni logika futtatása dokumentum létrehozásakor vagy frissítésekor [Cosmos db](./functions-bindings-cosmosdb-v2.md) |
| **Ütemezett feladatok futtatása** | Kód végrehajtása a [beállított időpontokban](./functions-bindings-timer.md) |
| **Megbízható üzenetsor-rendszerek létrehozása** | Üzenetek várólistáinak feldolgozása [Queue Storage](./functions-bindings-storage-queue.md), [Service Bus](./functions-bindings-service-bus.md)vagy [Event Hubs](./functions-bindings-event-hubs.md) használatával |
| **IoT adatfolyamok elemzése** | [IoT-eszközök adatainak](./functions-bindings-event-iot.md) összegyűjtése és feldolgozása |
| **Az adatfeldolgozás valós időben** | A [függvények és a jelző](./functions-bindings-signalr-service.md) használata az adatokra adott pillanatban való válaszadáshoz |

A függvények összeállításakor a következő lehetőségek és erőforrások érhetők el:

- **Használja az Ön által választott nyelvet**: Write functions in [C#, Java, JavaScript, PowerShell vagy Python](./supported-languages.md), vagy használjon [Egyéni kezelőt](./functions-custom-handlers.md) , hogy gyakorlatilag bármilyen más nyelvet használjon.

- Az **üzembe helyezés automatizálása**: a külső folyamatok eszközön alapuló megközelítése révén [számtalan üzembe helyezési lehetőség](./functions-deployment-technologies.md) áll rendelkezésre.

- **Függvények hibakeresése**: a [figyelési eszközök](./functions-monitoring.md) és a [tesztelési stratégiák](./functions-test-a-function.md) segítségével betekintést nyerhet az alkalmazásaiba.

- **Rugalmas díjszabási lehetőségek**: a [használati csomaggal csak a függvények](./pricing.md) futása után kell fizetnie, a [prémium](./pricing.md) és [app Service](./pricing.md) csomagok pedig speciális igényekhez nyújtanak szolgáltatásokat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a leckék, minták és interaktív oktatóanyagok használatával](./functions-get-started.md)
