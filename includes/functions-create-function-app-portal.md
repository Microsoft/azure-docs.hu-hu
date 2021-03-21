---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9ec5920295117b1726a684bcd08534cd6917f7e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100569788"
---
1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

1. Az **új** lapon válassza a **számítási**  >  **függvényalkalmazás** elemet.

1. Az **alapvető** beállítások lapon használja az alábbi táblázatban megadott Function App-beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. |
    | **Függvényalkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Az érvényes karakterek az `a-z` (kis- és nagybetűk megkülönböztetése nélkül) `0-9`és az `-`.  |
    |**Közzététel**| Kód | Kódfájlok közzétételét teszi lehetővé egy Docker-tárolóban. |
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. A portálon történő szerkesztés csak a JavaScript, a PowerShell, a írógéppel és a C# szkriptek esetében érhető el. A C# Class Library, a Java és a Python funkciókat [helyileg kell fejleszteni](../articles/azure-functions/functions-develop-local.md#local-development-environments).  |
    |**Verzió**| Verziószám | Válassza ki a telepített futtatókörnyezet verzióját. |
    |**Régió**| Előnyben részesített régió | Válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) vagy a funkciókhoz tartozó egyéb szolgáltatások közelében. |

1. Válassza a **Tovább: Üzemeltetés** lehetőségre. Az **üzemeltetés** lapon adja meg a következő beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Storage-fiók](../articles/storage/common/storage-account-create.md)** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A Storage-fiókok nevének 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket tartalmazhat. Meglévő fiókot is használhat, amelynek meg kell felelnie a [Storage-fiókra vonatkozó követelményeknek](../articles/azure-functions/storage-considerations.md#storage-account-requirements). |
    |**Operációs rendszer**| Windows | Az operációs rendszer előre ki van választva a futásidejű verem kiválasztása alapján, de szükség esetén módosíthatja a beállítást. A portálon belüli szerkesztés csak Windows rendszeren támogatott. |
    | **[Felkészülés](../articles/azure-functions/functions-scale.md)** | **Felhasználás (kiszolgáló nélküli)** | Szolgáltatási csomag, amely meghatározza az erőforrások lefoglalását a függvényalkalmazáshoz. Az alapértelmezett **használatalapú csomagban** az erőforrások hozzáadása dinamikusan történik a függvények követelményeinek megfelelően. Ebben a [kiszolgáló](https://azure.microsoft.com/overview/serverless-computing/) nélküli üzemeltetésben csak a függvények futtatásának idejére kell fizetnie. Ha App Service-csomagot használ, foglalkoznia kell a [függvényalkalmazás méretezésével](../articles/azure-functions/functions-scale.md).  |

1. Válassza a **Tovább: figyelés** lehetőséget. A **figyelés** lapon adja meg a következő beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Alapértelmezett | Létrehoz egy Application Insights erőforrást ugyanahhoz az *alkalmazáshoz* a legközelebbi támogatott régióban. A beállítás kibontásával vagy az **új létrehozása** lehetőség kiválasztásával módosíthatja a Application Insights nevét, vagy választhat egy másik régiót egy olyan [Azure-földrajzban](https://azure.microsoft.com/global-infrastructure/geographies/) , ahol az adatait tárolni szeretné. |

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget az alkalmazás-konfiguráció kiválasztásának áttekintéséhez.

1. A **felülvizsgálat + létrehozás** oldalon tekintse át a beállításokat, majd válassza a **Létrehozás** lehetőséget a Function alkalmazás kiépítéséhez és üzembe helyezéséhez.

1. Válassza az **értesítések** ikont a portál jobb felső sarkában, és figyelje meg az **üzembe helyezés sikeres** üzenetét.

1. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget. Kiválaszthatja **a rögzítés az irányítópulton** lehetőséget is. A rögzítéssel egyszerűbbé válik a Function app-erőforráshoz való visszatérés az irányítópultról.

    ![Üzembe helyezési értesítés](./media/functions-create-function-app-portal/function-app-create-notification2.png)
