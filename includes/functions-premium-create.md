---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2020
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 4eaea46044f0da4766fda051655d57caee9d98b6
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937015"
---
1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

1. Az **új** lapon válassza a **számítási**  >  **függvényalkalmazás** elemet.

1. Az **alapvető** beállítások lapon használja az alábbi táblázatban megadott Function App-beállításokat:

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. |
    | **Függvényalkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Az érvényes karakterek az `a-z` (kis- és nagybetűk megkülönböztetése nélkül) `0-9`és az `-`.  |
    |**Közzététel**| Kód | Kódfájlok közzétételét teszi lehetővé egy Docker-tárolóban. |
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. Válassza a **.NET** lehetőséget a C# és az F# függvényekhez. |
    |**Régió**| Előnyben részesített régió | Válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) vagy a funkciókhoz tartozó egyéb szolgáltatások közelében. |

    ![Alapismeretek lap](./media/functions-premium-create/function-app-create-basics.png)

1. Válassza a **Tovább: üzemeltetés** lehetőséget. Az **üzemeltetés** lapon adja meg a következő beállításokat:

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Storage-fiók](../articles/storage/common/storage-account-create.md)** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat, amelynek meg kell felelnie a [Storage-fiókra vonatkozó követelményeknek](../articles/azure-functions/storage-considerations.md#storage-account-requirements). |
    |**Operációs rendszer**| Előnyben részesített operációs rendszer | Az operációs rendszer előre ki van választva a futásidejű verem kiválasztása alapján, de szükség esetén módosíthatja a beállítást. A Python csak Linux rendszeren támogatott. |
    | **[Felkészülés](../articles/azure-functions/functions-scale.md)** | Prémium | Szolgáltatási csomag, amely meghatározza az erőforrások lefoglalását a függvényalkalmazáshoz. Válassza a **prémium** lehetőséget. Alapértelmezés szerint a rendszer új App Service tervet hoz létre. Az alapértelmezett **SKU és size** érték a **EP1**, ahol az EP a _rugalmas prémium szintű támogatást_ nyújtja. További információért lásd a [prémium SKU-ket tartalmazó listát](../articles/azure-functions/functions-premium-plan.md#available-instance-skus).<br/>A JavaScript-függvények prémium csomagon való futtatásakor olyan példányt válasszon, amelynek kevesebb vCPU van. További információ: az [egymagos prémium csomagok kiválasztása](../articles/azure-functions/functions-reference-node.md#considerations-for-javascript-functions).  |

    ![Üzemeltetési oldal](./media/functions-premium-create/function-app-premium-create-hosting.png)

1. Válassza a **Tovább: figyelés** lehetőséget. A **figyelés** lapon adja meg a következő beállításokat:

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Alapértelmezett | Létrehoz egy Application Insights erőforrást ugyanahhoz az *alkalmazáshoz* a legközelebbi támogatott régióban. A beállítás kibontásával módosíthatja az **új erőforrás nevét** , vagy kiválaszthat egy másik **helyet** az [Azure-földrajzban](https://azure.microsoft.com/global-infrastructure/geographies/) az adatai tárolásához. |

    ![Figyelési oldal](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget az alkalmazás-konfiguráció kiválasztásának áttekintéséhez.

1. A **felülvizsgálat + létrehozás** oldalon tekintse át a beállításokat, majd válassza a **Létrehozás** lehetőséget a Function alkalmazás kiépítéséhez és üzembe helyezéséhez.

1. Válassza az **értesítések** ikont a portál jobb felső sarkában, és figyelje meg az **üzembe helyezés sikeres** üzenetét.

1. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget. Kiválaszthatja **a rögzítés az irányítópulton** lehetőséget is. A rögzítéssel egyszerűbbé válik a Function app-erőforráshoz való visszatérés az irányítópultról.

    ![Üzembe helyezési értesítés](./media/functions-premium-create/function-app-create-notification2.png)
