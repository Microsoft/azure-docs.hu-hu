---
title: Azure Functions diagnosztika áttekintése
description: Ismerje meg, hogy miként lehet elhárítani a Function alkalmazással kapcsolatos problémákat Azure Functions diagnosztikát.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: fb329273d442c023233fee2e7e01aabe5f5bff8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "83122365"
---
# <a name="azure-functions-diagnostics-overview"></a>Az Azure Functions diagnosztikai áttekintése

Ha egy Function alkalmazást futtat, elő kell készítenie az esetlegesen felmerülő problémákra a hibák 4xx. A Azure Functions Diagnostics egy intelligens és interaktív megoldás, amellyel a Function-alkalmazás nem konfigurálható és nem használható fel külön díj nélkül. Ha a Function alkalmazással kapcsolatos problémákba ütközik, Azure Functions diagnosztika kimutatja, hogy mi a probléma. A probléma megoldásához a megfelelő információkkal segít, hogy könnyebben és gyorsan hárítsa el a problémát. Ebből a cikkből megtudhatja, hogyan használhatja a Azure Functions diagnosztikát a gyakori Function app-problémák gyors diagnosztizálásához és megoldásához.

## <a name="start-azure-functions-diagnostics"></a>Azure Functions diagnosztika elindítása

Azure Functions diagnosztika elindítása:

1. Navigáljon a Function alkalmazáshoz a [Azure Portal](https://portal.azure.com).
1. Azure Functions diagnosztika megnyitásához válassza a **diagnosztizálás és megoldás problémák** elemet.
1. Válassza ki azt a kategóriát, amely a legjobban leírja a függvény alkalmazásának hibáját a Kezdőlap csempén található kulcsszavak használatával. Olyan kulcsszót is beírhat, amely a legjobban leírja a problémát a keresősávban. Beírhatja például, `execution` hogy megjelenjen a Function app-végrehajtáshoz kapcsolódó diagnosztikai jelentések listája, és közvetlenül a kezdőlapról nyissa meg őket.

   :::image type="content" source="./media/functions-diagnostics/functions-app-search-azure-functions-diagnostics.png" alt-text="Keressen Azure Functions diagnosztikát." border="true":::

## <a name="use-the-interactive-interface"></a>Az interaktív felület használata

Miután kiválasztott egy olyan Kezdőlap kategóriát, amely a legjobban megfelel a Function app-problémájának, Azure Functions Diagnostics interaktív felülete, a Genie nevű interaktív kezelőfelület végigvezeti Önt az alkalmazás problémáinak diagnosztizálásán és megoldásán. A Genie által biztosított csempe-parancsikonok segítségével megtekintheti a probléma kategóriájának teljes diagnosztikai jelentését. A csempék parancsikonjai közvetlen módot biztosítanak a diagnosztikai metrikák elérésére.

:::image type="content" source="./media/functions-diagnostics/genie.png" alt-text="A Genie Azure Functions diagnosztikai felület." border="false":::

A csempék kiválasztása után megtekintheti a csempén bemutatott problémával kapcsolatos témakörök listáját. Ezek a témakörök a teljes jelentésből származó jelentős információk részleteit tartalmazzák. Válassza ki a témakörök bármelyikét a további problémák kivizsgálásához. Azt is megteheti, hogy a **teljes jelentés megtekintése** lehetőségre kattintva egyetlen oldalon tekintheti meg az összes témakört.

:::image type="content" source="./media/functions-diagnostics/preview-of-diagnostic-report.png" alt-text="Diagnosztikai jelentés előnézete" border="false":::

## <a name="view-a-diagnostic-report"></a>Diagnosztikai jelentés megtekintése

Miután kiválasztott egy témakört, megtekintheti a Function alkalmazásra jellemző diagnosztikai jelentést. A diagnosztikai jelentések állapotjelző ikonokkal jelzik, hogy van-e konkrét probléma az alkalmazással kapcsolatban. Megtekintheti a probléma részletes leírását, az ajánlott műveleteket, a kapcsolódó mérőszámokat és a hasznos dokumentumokat. A rendszer a testreszabott diagnosztikai jelentéseket a Function alkalmazásban futó ellenőrzések sorozatából hozza létre. A diagnosztikai jelentések hasznos eszköz lehet a Function-alkalmazásban felmerülő problémák azonosításához és a probléma megoldásához.

## <a name="find-the-problem-code"></a>A probléma kódjának megkeresése

A parancsfájl-alapú függvények esetében a **függvény végrehajtása és a hibák** **függvényalkalmazás legördülő menüben, illetve a hibák bejelentése** a kódban a kivételeket vagy hibákat okozó sorokra szűkíthető. Ezzel az eszközzel beolvashatja a kiváltó okot, és kipróbálhatja a kód adott sorában felmerülő problémákat. Ez a beállítás nem érhető el előre lefordított C#-és Java-függvényekhez.

:::image type="content" source="./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png" alt-text="Diagnosztikai jelentés a függvények végrehajtásával kapcsolatos hibákról" border="false":::

:::image type="content" source="./media/functions-diagnostics/function-exception.png" alt-text="Kivétel részleteinek megtekintése." border="false":::

## <a name="next-steps"></a>Következő lépések

Kérdéseket tehet fel, és visszajelzést küldhet Azure Functions diagnosztikát a következő címen: [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Belefoglalás a `[Diag]` visszajelzés címébe.

> [!div class="nextstepaction"]
> [A Function apps figyelése](functions-monitoring.md)
