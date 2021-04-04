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
ms.openlocfilehash: cdeba09c91bee72508632b8468126150017df7cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98187352"
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
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. A portálon belüli szerkesztés jelenleg nem támogatott a [Python-fejlesztéshez](../articles/azure-functions/functions-reference-python.md).|
    |**Régió**| Előnyben részesített régió | Válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) vagy a funkciókhoz tartozó egyéb szolgáltatások közelében. |

1. Válassza a **Tovább: üzemeltetés** lehetőséget. Az **üzemeltetés** lapon adja meg a következő beállításokat:

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Storage-fiók](../articles/storage/common/storage-account-create.md)** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat, amelynek meg kell felelnie a [Storage-fiókra vonatkozó követelményeknek](../articles/azure-functions/storage-considerations.md#storage-account-requirements). |
    |**Operációs rendszer**| Előnyben részesített operációs rendszer | Az operációs rendszer előre ki van választva a futásidejű verem kiválasztása alapján, de szükség esetén módosíthatja a beállítást. A Python csak Linux rendszeren támogatott. A portálon belüli szerkesztés csak Windows rendszeren támogatott.|
    | **[Felkészülés](../articles/azure-functions/functions-scale.md)** | Prémium | Szolgáltatási csomag, amely meghatározza az erőforrások lefoglalását a függvényalkalmazáshoz. Válassza a **prémium** lehetőséget. Alapértelmezés szerint a rendszer új App Service tervet hoz létre. Az alapértelmezett **SKU és size** érték a **EP1**, ahol az EP a _rugalmas prémium szintű támogatást_ nyújtja. További információért lásd a [prémium SKU-ket tartalmazó listát](../articles/azure-functions/functions-premium-plan.md#available-instance-skus).<br/>A JavaScript-függvények prémium csomagon való futtatásakor olyan példányt válasszon, amelynek kevesebb vCPU van. További információ: az [egymagos prémium csomagok kiválasztása](../articles/azure-functions/functions-reference-node.md#considerations-for-javascript-functions).  |

1. Válassza a **Tovább: figyelés** lehetőséget. A **figyelés** lapon adja meg a következő beállításokat:

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Alapértelmezett | Létrehoz egy Application Insights erőforrást ugyanahhoz az *alkalmazáshoz* a legközelebbi támogatott régióban. A beállítás kibontásával módosíthatja az **új erőforrás nevét** , vagy kiválaszthat egy másik **helyet** az [Azure-földrajzban](https://azure.microsoft.com/global-infrastructure/geographies/) az adatai tárolásához. |

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget az alkalmazás-konfiguráció kiválasztásának áttekintéséhez.

1. A **felülvizsgálat + létrehozás** oldalon tekintse át a beállításokat, majd válassza a **Létrehozás** lehetőséget a Function alkalmazás kiépítéséhez és üzembe helyezéséhez.

1. Válassza az **értesítések** ikont a portál jobb felső sarkában, és figyelje meg az **üzembe helyezés sikeres** üzenetét.

1. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget. Kiválaszthatja **a rögzítés az irányítópulton** lehetőséget is. A rögzítéssel egyszerűbbé válik a Function app-erőforráshoz való visszatérés az irányítópultról.

    ![Üzembe helyezési értesítés](./media/functions-premium-create/function-app-create-notification2.png)
