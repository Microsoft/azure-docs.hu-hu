---
title: A Azure Maps API használati metrikáinak megtekintése | Microsoft Azure térképek
description: 'Megtudhatja, hogyan tekintheti meg Azure Maps API használati metrikáit, például a kérelmek teljes számát, a teljes hibákat és a rendelkezésre állást. Lásd: az Adatszűrés és az eredmények felosztása.'
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/06/2018
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c9b732bd25e7ef8aa084c98d5b059d422f86a4b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003514"
---
# <a name="view-azure-maps-api-usage-metrics"></a>Az Azure Maps API használati metrikáinak megtekintése

Ez a cikk bemutatja, hogyan tekintheti meg Azure Maps-fiókja API-használati metrikáit a [Azure Portal](https://portal.azure.com). A metrikák kényelmes gráf formátumban jelennek meg, a testreszabható idő időtartama alatt.

## <a name="view-metric-snapshot"></a>Metrika pillanatképének megtekintése

A Maps-fiók **áttekintő** lapján néhány gyakori mérőszámot láthat. Jelenleg a *teljes kérelmeket*, a *teljes hibákat* és a *rendelkezésre állást* jeleníti meg egy kijelölhető időtartamon keresztül.

![Azure Maps használati metrikák áttekintése](media/how-to-view-api-usage/portal-overview.png)

Folytassa a következő szakasszal, ha testre kell szabnia ezeket a diagramokat az adott elemzéshez.

## <a name="view-detailed-metrics"></a>Részletes mérőszámok megtekintése

1. Jelentkezzen be az Azure-előfizetésbe a [portálon](https://portal.azure.com).

2. Kattintson a **minden erőforrás** menüpontra a bal oldali oldalon, és navigáljon a *Azure Maps-fiókjához*.

3. Miután megnyitotta a Maps-fiókját, kattintson a bal oldali **mérőszámok** menüre.

4. A **metrikák** ablaktáblán válasszon a következő lehetőségek közül:

   1. **Rendelkezésre állás** – az API rendelkezésre állásának *átlagát* mutatja egy adott időszakban.
   2. **Használat** – ez azt mutatja, hogy a fiók használati *száma* hogyan használható.

      ![Azure Maps használati metrikák panel](media/how-to-view-api-usage/portal-metrics.png)

5. Ezután kiválaszthatja az *időtartományt* az **elmúlt 24 óra (automatikus)** lehetőségre kattintva. Alapértelmezés szerint az időtartomány 24 órára van beállítva. A gombra kattintva megjelenik az összes választható időtartomány. Kiválaszthatja az *idő részletességét* , és kiválaszthatja, hogy a *helyi* vagy a *GMT* időpontot jelenítse meg ugyanabban a legördülő listában. Kattintson az **Alkalmaz** gombra.

    ![Azure Maps metrikák időtartománya](media/how-to-view-api-usage/time-range.png)

6. Miután hozzáadta a metrikát, **szűrőt adhat hozzá** az adott metrikához tartozó tulajdonságok közül. Ezután válassza ki a diagramon megjeleníteni kívánt tulajdonság értékét.

    ![Azure Maps használati metrikák szűrője](media/how-to-view-api-usage/filter.png)

7. A mérőszám felosztását a kiválasztott metrikai tulajdonság alapján is **alkalmazhatja** . Lehetővé teszi, hogy a gráf több gráfra legyen felosztva az adott tulajdonság minden értékéhez. Az alábbi képen az egyes diagramok színe a gráf alján látható tulajdonság értékének felel meg.

    ![Azure Maps használati metrikák felosztása](media/how-to-view-api-usage/splitting.png)

8. Ugyanezen a gráfon több mérőszám is megfigyelhető, egyszerűen kattintson a felül található **metrika hozzáadása** gombra.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a Azure Maps API-kkal, amelyeken nyomon szeretné követni a használatot:
> [!div class="nextstepaction"] 
> [Azure Maps web SDK – útmutató](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Azure Maps Android SDK – útmutató](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Azure Maps REST API dokumentációja](/rest/api/maps)