---
title: Azure Application Insights – áttekintés irányítópult | Microsoft Docs
description: Alkalmazások figyelése az Azure Application Insights és az áttekintő irányítópult funkcióinak segítségével.
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 1b0708fa70d3a3ecb406f1d974bb1f2b47e55b40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97504100"
---
# <a name="application-insights-overview-dashboard"></a>Application Insights áttekintése irányítópult

A Application Insights mindig biztosította az összefoglalás áttekintés paneljét, amely lehetővé teszi az alkalmazás állapotának és teljesítményének gyors, dióhéjban történő értékelését. Az új áttekintési irányítópult gyorsabb rugalmasabb élményt nyújt.

## <a name="how-do-i-test-out-the-new-experience"></a>Hogyan kipróbálni az új felhasználói élményt?

Alapértelmezés szerint az új áttekintő irányítópult most elindul:

![Áttekintő előnézet panel](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Jobb teljesítmény

Az időtartomány kiválasztása egyszerűsített egy egyszerű, egykattintásos illesztőfelületre.

![Időtartomány](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

A teljes teljesítmény jelentősen növekedett. Egy kattintással elérhetők a népszerű funkciók, például a **Search** és az **Analytics**. Mindegyik alapértelmezett, dinamikusan frissülő KPI-csempe betekintést nyújt a megfelelő Application Insights szolgáltatásaiba. Ha többet szeretne megtudni a sikertelen kérelmekről, válassza ki a **hibák** a **vizsgálat** fejléc alatt:

![Hibák](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Alkalmazás irányítópultja

Az alkalmazás irányítópultja a meglévő irányítópult-technológiát használja az Azure-ban az alkalmazás állapotának és teljesítményének teljes mértékben testreszabható egyablakos nézetének megadásához.

Az alapértelmezett irányítópult eléréséhez válassza az _alkalmazás irányítópult_ lehetőséget a bal felső sarokban.

![Képernyőfelvétel: az alkalmazás irányítópult gombja kiemelve.](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Ha első alkalommal fér hozzá az irányítópulthoz, a rendszer elindítja az alapértelmezett nézetet:

![Irányítópult nézet](./media/overview-dashboard/0001-dashboard.png)

Ha szeretné, megtarthatja az alapértelmezett nézetet is. Emellett hozzáadhat és törölhet is az irányítópultról, hogy az megfeleljen a csapat igényeinek.

> [!NOTE]
> Az Application Insights erőforráshoz hozzáféréssel rendelkező összes felhasználó ugyanazzal az alkalmazás-irányítópulttal rendelkezik. Az egyik felhasználó által végrehajtott módosítások az összes felhasználó nézetét módosítják.

Ha vissza szeretne térni az áttekintő felületre, csak válassza a következőt:

![Áttekintés gomb](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Az irányítópulton megjelenített adatmennyiségnek jelenleg legfeljebb 30 napos adatértéke van. Ha 30 napon túli időszűrőt választ, vagy ha a **csempe beállításainak megadása** lehetőséget választja, és az egyéni időtartományt 30 napon belül meghaladóra állítja, az irányítópult nem jeleníti meg a 30 napos adatmennyiséget, még az alapértelmezett 90 napos adatmegőrzési időtartamot is. Ehhez a viselkedéshez jelenleg nincs Áthidaló megoldás.

## <a name="next-steps"></a>Következő lépések

- [Tölcsérek](./usage-funnels.md)
- [Visszatartás](./usage-retention.md)
- [Felhasználókövetés](./usage-flows.md)

