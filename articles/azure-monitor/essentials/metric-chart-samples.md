---
title: Példa Azure Monitor metrikus diagramra
description: Tudnivalók a Azure Monitor adatainak megjelenítéséről.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 882c9e50c344b792d70d09017aeca3a2874cb106
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700745"
---
# <a name="metric-chart-examples"></a>Metrikus diagram – példák 

Az Azure platform [több mint ezer mérőszámot](./metrics-supported.md)kínál, amelyek közül sok dimenziót tartalmaz. A [dimenzió szűrők](./metrics-charts.md)használatával a [felosztás](./metrics-charts.md), a diagramtípus szabályozása és a diagram beállításainak módosítása lehetővé teszi, hogy hatékony diagnosztikai nézeteket és irányítópultokat hozzon létre, amelyek betekintést nyújtanak az infrastruktúra és az alkalmazások állapotára. Ez a cikk néhány példát mutat be a [Metrikaböngésző](./metrics-charts.md) használatával felépíthető diagramokra, és ismerteti az egyes diagramok konfigurálásához szükséges lépéseket.

Szeretné megosztani a nagyszerű diagramok példáit a világgal? Járuljon hozzá ehhez az oldalhoz a GitHubon, és ossza meg a saját diagramon megjelenő példákat!

## <a name="website-cpu-utilization-by-server-instances"></a>Webhely CPU-kihasználtsága kiszolgálói példányok szerint

Ez a diagram azt mutatja, hogy egy App Service PROCESSZORa az elfogadható tartományon belül van-e, és a példányok lebontásával megállapítja, hogy a terhelés megfelelően van-e elosztva. A diagramon láthatja, hogy az alkalmazás egy adott kiszolgálói példányon fut a 6. előtt, majd egy másik példány hozzáadásával felskálázást végez.

![A CPU átlagos százalékos arányának diagramja kiszolgálópéldány szerint](./media/metrics-charts/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Hogyan konfigurálható a diagram?

Válassza ki a App Service erőforrást, és keresse meg a **CPU százalékos** metrikáját. Ezután kattintson a **felosztás alkalmazása** elemre, és válassza ki a **példány** dimenziót.

## <a name="application-availability-by-region"></a>Alkalmazások elérhetősége régiónként

Megtekintheti az alkalmazás elérhetőségét régiónként, és meghatározhatja, hogy mely földrajzi helyeknél van probléma. Ez a diagram a Application Insights rendelkezésre állási metrikáját mutatja be. Láthatja, hogy a figyelt alkalmazás nem jelent problémát az USA keleti adatközpontjának rendelkezésre állása miatt, de az USA nyugati régiójában lévő, részlegesen rendelkezésre álló probléma tapasztalható, és Kelet-Ázsia.

![Helyek közötti átlagos elérhetőség diagramja](./media/metrics-charts/availability-by-location.png)

### <a name="how-to-configure-this-chart"></a>Hogyan konfigurálható a diagram?

Először be kell kapcsolni [Application Insights rendelkezésre állásának](../app/monitor-web-app-availability.md) figyelését a webhelyén. Ezt követően válassza ki Application Insights erőforrását, és válassza ki a rendelkezésre állási metrikát. Alkalmazzon felosztást a **futtatási hely** dimenzión.

## <a name="volume-of-failed-storage-account-transactions-by-api-name"></a>Sikertelen tárolási fiókok tranzakcióinak mennyisége API-név szerint

A Storage-fiók erőforrása túlzott mennyiségű sikertelen tranzakciót észlelt. A tranzakciók metrikájának használatával meghatározhatja, hogy melyik API felelős a felesleges meghibásodásért. Figyelje meg, hogy a következő diagram ugyanazzal a dimenzióval (API-névvel) van konfigurálva a sikertelen válasz típusa szerinti felosztás és szűrés során:

![API-tranzakciók oszlopdiagram](./media/metrics-charts/split-and-filter-example.png)

### <a name="how-to-configure-this-chart"></a>Hogyan konfigurálható a diagram?

A metrika-választóban válassza ki a Storage-fiókot és a **tranzakciók** metrikáját. Diagram típusának váltása **oszlopdiagram**. Kattintson a **felosztás alkalmazása** elemre, és válassza a dimenzió **API neve** elemet. Ezután kattintson a **szűrő hozzáadása** lehetőségre, majd válassza ki az **API-név** dimenziót újra. A szűrő párbeszédpanelen válassza ki a diagramon ábrázolni kívánt API-kat.

## <a name="next-steps"></a>Következő lépések

* Tudnivalók a Azure Monitor- [munkafüzetek](../visualize/workbooks-overview.md) használatáról
* További információ a [metrika-kezelőről](metrics-charts.md)