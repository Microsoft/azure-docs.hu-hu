---
title: Az Azure Application Insights frissítése Snapshot Debugger
description: A .NET-alkalmazások Snapshot Debugger frissítése az Azure App Services legújabb verziójára vagy Nuget-csomagokon keresztül
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "77671392"
---
# <a name="upgrading-the-snapshot-debugger"></a>A Snapshot Debugger frissítése

Az adatok lehető legjobb biztonságának biztosítása érdekében a Microsoft elmozdul a TLS 1,0 és a TLS 1,1 rendszertől, amelyek kimutatták, hogy sebezhetőek voltak a meghatározott támadók számára. Ha a hely bővítményének egy régebbi verzióját használja, akkor a működés folytatásához frissítés szükséges. Ez a dokumentum a Snapshot Debugger legújabb verzióra való frissítéséhez szükséges lépéseket ismerteti. Ha engedélyezte a Snapshot Debuggert a hely kiterjesztése alapján, vagy ha az alkalmazáshoz hozzáadott SDK/Nuget használta, két elsődleges frissítési útvonal van. A frissítési útvonalakat az alábbiakban tárgyaljuk. 

## <a name="upgrading-the-site-extension"></a>A hely kiterjesztésének frissítése

> [!IMPORTANT]
> A Application Insights régebbi verziói a _Azure App Service Application Insights bővítményét_ használták a Private site bővítménnyel. Az aktuális Application Insights-élmény engedélyezve van az Alkalmazásbeállítások megadásával egy előre telepített hely bővítményének megvilágításához.
> Az ütközések elkerülése érdekében, ami miatt előfordulhat, hogy a hely működése leáll, fontos, hogy először törölje a Private site bővítményt. Lásd az alábbi 4. lépést.

Ha engedélyezte a pillanatkép-hibakeresőt a hely bővítménnyel, a következő eljárással frissítheti az alkalmazást:

1. Jelentkezzen be az Azure Portalra.
2. Navigáljon az erőforráshoz, amelyen engedélyezve van a Application Insights és a Snapshot Debugger. Egy webalkalmazás esetében például navigáljon a App Service erőforráshoz:

   ![Képernyőkép a DiagService01 nevű egyéni App Service erőforrásról](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Miután megnyitotta az erőforrást, kattintson a bővítmények panelre, és várjon, amíg a bővítmények listája fel nem töltődik:

   ![Képernyőkép App Service-bővítményekről, amelyek a Azure App Service telepített Application Insights bővítményét mutatják](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. Ha a _Azure App Service Application Insights-bővítmény_ bármely verziója telepítve van, válassza ki, majd kattintson a Törlés gombra. Erősítse meg az **Igen** gombot a bővítmény törléséhez, és várjon, amíg a törlés befejeződik a következő lépésre való áttérés előtt.

   ![Képernyőkép App Service-bővítményekről, amelyek a Azure App Service Application Insights bővítményét jelenítik meg a törlés gomb kiemelésével](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. Lépjen az erőforrás áttekintés paneljére, és kattintson a Application Insights:

   ![Képernyőfelvétel három gombról. Application Insights nevű középső gomb van kiválasztva](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. Ha első alkalommal tekinti meg a App Service Application Insights paneljét, a rendszer felszólítja, hogy kapcsolja be Application Insights. Jelölje be **a Application Insights bekapcsolása** elemet.
 
   ![Képernyőfelvétel: a Application Insights panel első idejű élménye, a bekapcsolás Application Insights gomb kiemelve](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. Megjelenik az aktuális Application Insights beállítások. Hacsak nem szeretné módosítani a beállításokat, meghagyhatja őket. A panel alján található **Apply (alkalmaz** ) gomb alapértelmezés szerint nincs engedélyezve, és az egyik beállítást be kell váltania a gomb aktiválásához. Nem kell módosítania az aktuális beállításokat, hanem módosíthatja a beállítást, majd azonnal visszaállíthatja azt. Javasoljuk, hogy állítsa be a Profiler-beállítást, majd válassza az **alkalmaz** lehetőséget.

   ![Képernyőkép a Application Insights App Service konfigurációs oldaláról a piros színnel jelölt Apply (alkalmaz) gombbal](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. Miután rákattintott az **alkalmaz** gombra, a rendszer megkéri, hogy erősítse meg a módosításokat.

    > [!NOTE]
    > A rendszer a frissítési folyamat részeként újraindítja a helyet.

   ![Képernyőfelvétel: App Service alkalmazás-figyelési kérése. A szövegmező üzenet jelenik meg: "mostantól alkalmazjuk az Alkalmazásbeállítások módosításait, és a Application Insights erőforrás a webalkalmazáshoz való összekapcsolására szolgáló eszközöket telepítjük. Ekkor a rendszer újraindítja a helyet. Folytatja? "](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. A módosítások alkalmazásához kattintson az **Igen** gombra, és várjon, amíg a folyamat befejeződik.

A hely már frissítve lett, és készen áll a használatra.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Snapshot Debugger frissítése SDK/Nuget használatával

Ha az alkalmazás az `Microsoft.ApplicationInsights.SnapshotCollector` alábbi verziójú 1.3.1-es verziót használja, akkor a működés folytatásához frissíteni kell egy [újabb verzióra](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) .
