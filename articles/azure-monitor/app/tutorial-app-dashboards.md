---
title: Egyéni irányítópultok létrehozása az Azure Application Insightsban | Microsoft Docs
description: Oktatóanyag egyéni KPI-irányítópultok Azure Application Insights használatával való létrehozásához.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/30/2020
ms.custom: mvc, contperf-fy21q1
ms.openlocfilehash: 701480300101b019830c57d9aa000534fa63bb6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100627619"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Egyéni KPI irányítópultok létrehozása az Azure Application Insights használatával

Több irányítópultot is létrehozhat az Azure Portalon, amelyeknek a csempéi különböző erőforráscsoportokban és előfizetésekben található Azure-erőforrások adatait jelenítik meg.  Különböző Azure Application Insights-diagramok és -nézetek rögzítésével egyéni irányítópultokat hozhat létre, amelyek átfogó képet mutatnak az alkalmazás állapotáról és teljesítményéről. Ez az oktatóanyag bemutatja, hogyan lehet az Azure Application Insightsból származó többféle adatot és vizualizációt tartalmazó egyéni irányítópultokat létrehozni.

 Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Egyéni irányítópult létrehozása az Azure-ban
> * Csempe hozzáadása a csempekatalógusból
> * Standard Application Insights-metrikák hozzáadása az irányítópulthoz
> * Egyéni Application Insights-metrikadiagramok hozzáadása az irányítópulthoz
> * Naplók (Analytics) lekérdezés eredményeinek hozzáadása az irányítópulthoz

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Telepítsen egy .NET-alkalmazást az Azure-hoz, és [engedélyezze az Application Insights SDK](../app/asp-net.md)-t.

> [!NOTE]
> Az irányítópultok használatához szükséges engedélyeket az [irányítópultok hozzáférés-vezérlésének megismerését ismertető](../../azure-portal/azure-portal-dashboard-share-access.md#understanding-access-control-for-dashboards)cikkben tárgyaljuk.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-new-dashboard"></a>Új irányítópult létrehozása

> [!WARNING]
> Ha áthelyezi a Application Insights erőforrást egy másik erőforráscsoporthoz vagy előfizetésbe, manuálisan kell frissítenie az irányítópultot, ha eltávolítja a régi csempéket, és új csempéket szeretne kipróbálni ugyanabból a Application Insights erőforrásból.

Egyazon irányítópult több alkalmazáshoz, erőforráscsoporthoz és előfizetéshez tartozó erőforrásokat is tartalmazhat.  Az oktatóanyag első lépéseként hozzon létre egy új irányítópultot az alkalmazásához.  

1. A Azure Portal bal oldali legördülő menüjében válassza az **irányítópult** lehetőséget.

    ![Az Azure Portal menüjének legördülő listája](media/tutorial-app-dashboards/dashboard-from-menu.png)

2. Az irányítópult ablaktáblán válassza az **új irányítópult** , majd az **üres irányítópult** elemet.

   ![Új irányítópult](media/tutorial-app-dashboards/new-dashboard.png)

3. Írja be az irányítópult nevét.
4. Tekintse át a **Csempekatalógust**, amelyben az irányítópultra rögzíthető számos különböző csempe található.  A csempék katalógusból való hozzáadásán kívül a diagramokat és egyéb nézeteket is rögzíthet közvetlenül Application Insightsról az irányítópultra.
5. Keresse meg a **Markdown** csempét, és húzza az irányítópultra.  Ez a csempe lehetővé teszi a Markdown-ben formázott szöveg hozzáadását, amely ideális a leíró szöveg az irányítópulthoz való hozzáadásához. További információ: [Markdown csempe használata az Azure-irányítópultokon az egyéni tartalmak megjelenítéséhez](../../azure-portal/azure-portal-markdown-tile.md).
6. Adjon hozzá szöveget a csempe tulajdonságaiban, és méretezze át a csempét az irányítópult vásznán.

    [![Markdown-csempe szerkesztése](media/tutorial-app-dashboards/markdown.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

7. A képernyő felső részén válassza a **kész Testreszabás** lehetőséget a csempe testreszabási módjának kilépéséhez.

## <a name="add-health-overview"></a>Állapotáttekintés hozzáadása

A statikus szöveget tartalmazó irányítópultok nem nagyon érdekesek, ezért az alkalmazással kapcsolatos információk megjelenítéséhez vegyen fel egy csempét a Application Insightsból. Az Application Insights-csempéket hozzáadhatja a csempekatalógusból, vagy közvetlenül az Application Insights-képernyőkről is rögzítheti őket. Így a már ismert diagramokat és nézeteket konfigurálhatja, mielőtt rögzítené őket az irányítópulton.  Először adja hozzá az alkalmazás standard állapotáttekintését.  Ez nem igényel konfigurálást, és minimális testreszabási lehetőségeket biztosít az irányítópulton.


1. Válassza ki az **Application Insights** erőforrást a kezdőképernyőn.
2. Az **Áttekintés** ablaktáblán válassza a rögzítés ikon ![ rögzítése ikont a ](media/tutorial-app-dashboards/pushpin.png) csempe irányítópulthoz való hozzáadásához.
3. A "rögzítés az irányítópulton" lapon válassza ki, hogy melyik irányítópulton szeretné felvenni a csempét, vagy hozzon létre egy újat.
 
3. A jobb felső sarokban egy értesítés jelenik meg, amelyet a csempe rögzített az irányítópulton.  Az értesítésben válassza a rögzített elemet az **irányítópultra** , hogy visszatérjen az irányítópulthoz, vagy használja az irányítópult ablaktáblát.
4. Ezzel a csempével most már hozzá lesz adva az irányítópulthoz. A csempe elhelyezésének módosításához válassza a **Szerkesztés** lehetőséget. Jelölje ki és húzza a pozícióba, majd válassza a **kész Testreszabás** lehetőséget. Az irányítópulton ezzel már van egy olyan csempe, amely hasznos információkat tartalmaz.

    [![Irányítópult szerkesztési módban](media/tutorial-app-dashboards/dashboard-edit-mode.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

## <a name="add-custom-metric-chart"></a>Egyéni metrikadiagram hozzáadása

A **Metrika** panel segítségével az Application Insights által gyűjtött metrikák időbeli alakulását mutató diagramokat készíthet választható szűrőkkel és csoportosítással.  Ahogy az Application Insights minden más elemét, ezt a diagramot is felveheti az irányítópultra.  Ehhez előbb szükség van némi testreszabásra.

1. Válassza ki a **Application Insights** erőforrást a kezdőképernyőn.
1. Válassza a **Metrikák** lehetőséget.  
2. Már létrejött egy üres diagram, és most a rendszer kéri, hogy adjon hozzá egy metrikát.  Adjon hozzá egy metrikát a diagramhoz, valamint igény szerint szűrőt és csoportosítást is.  Az alábbi példában a kiszolgálókérelmek száma látható sikeresség szerint csoportosítva.  Ez a sikeres és sikertelen kérelmek akkumulált nézetét mutatja.

    [![Metrika hozzáadása](media/tutorial-app-dashboards/metrics.png)](media/tutorial-app-dashboards/metrics.png#lightbox)

4. A jobb oldalon válassza a **rögzítés az irányítópulton** lehetőséget.

3.  A jobb felső sarokban egy értesítés jelenik meg, amelyet a csempe rögzített az irányítópulton. Az értesítésben válassza a rögzített elemet az **irányítópultra** , hogy visszatérjen az irányítópulthoz, vagy használja az irányítópult lapot.

4. Ezzel a csempével most már hozzá lesz adva az irányítópulthoz. A csempe elhelyezésének módosításához válassza a **Szerkesztés** lehetőséget. Jelölje ki és húzza a csempét a pozícióba, majd válassza a **Testreszabás kész** lehetőséget.

## <a name="add-logs-query"></a>Naplók hozzáadása lekérdezés

Az Azure Application Insights-naplók részletes lekérdezési nyelvet biztosítanak, amely lehetővé teszi az összes összegyűjtött adatok elemzését Application Insights. A diagramokhoz és egyéb nézetekhez hasonlóan a naplók kimenetét is hozzáadhatja az irányítópulthoz.

1. Válassza ki a **Application Insights** erőforrást a kezdőképernyőn.
2. A naplók lap megnyitásához **kattintson a bal oldalon a "** figyelés" elemre.
3. Írja be a következő lekérdezést, amely a 10 leggyakrabban lekérdezett lapot és a kérelmek számát adja vissza:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. A lekérdezés eredményeinek ellenőrzéséhez válassza a **Futtatás** lehetőséget.
5. Válassza a gombostű ikont, ![Gombostű ikon](media/tutorial-app-dashboards/pushpin.png) majd válassza ki az irányítópult nevét.

5. Mielőtt visszalépjen az irányítópultra, adjon hozzá egy másik lekérdezést, de diagramként jelenítse meg, így láthatja, hogyan jelenítheti meg a naplók lekérdezéseit egy irányítópulton. Indítsa el a következő lekérdezést, amely a 10 legtöbb kivétellel járó műveletet összesíti.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Válassza a **Diagram** lehetőséget, majd váltson a **Perec** típusra a kimenet megjelenítéséhez.

    [![Perecdiagram a fenti lekérdezéssel](media/tutorial-app-dashboards/logs-doughnut.png)](media/tutorial-app-dashboards/logs-doughnut.png#lightbox)

6. Válassza a gombostű ikont, ![Gombostű ikon](media/tutorial-app-dashboards/pushpin.png) a jobb felső sarokban rögzítse a diagramot az irányítópulton, majd térjen vissza az irányítópultra.
7. A lekérdezések eredményei most megjelennek az irányítópulton a választott formátumban. Jelölje ki és húzza az egyeseket pozícióba, majd válassza a **Testreszabás kész** lehetőséget.
8. Válassza a ceruza ikont ![Ceruza ikon](media/tutorial-app-dashboards/pencil.png) minden címben adjon meg egy leíró címet.

## <a name="share-dashboard"></a>Irányítópult megosztása

1. Az irányítópult tetején válassza a **megosztás** lehetőséget a módosítások közzétételéhez.
2. Meghatározhat adott felhasználókat, akiknek hozzáférést kíván biztosítani az irányítópulthoz. További információ: Azure- [irányítópultok megosztása az Azure szerepköralapú hozzáférés-vezérlés használatával](../../azure-portal/azure-portal-dashboard-share-access.md).
3. Válassza a **Közzététel** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerkedett az egyéni irányítópultok létrehozásával, tekintse át az Application Insights dokumentációját, amely egy esettanulmányt is tartalmaz.

> [!div class="nextstepaction"]
> [Részletes diagnosztika](../app/devops.md)
