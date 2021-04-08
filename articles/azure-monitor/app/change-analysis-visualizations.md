---
title: Az alkalmazás változási elemzésének vizualizációi – Azure Monitor
description: Megtudhatja, hogyan használhatja a vizualizációkat az Application Change Analysis Azure Monitorban.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 8319885de26bf79f5e402c4d06b29e9dd94894de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655851"
---
# <a name="visualizations-for-application-change-analysis-preview"></a>Az Application Change Analysis (előzetes verzió) vizualizációi

## <a name="standalone-ui"></a>Önálló felhasználói felület

Azure Monitor a változások elemzésére szolgáló önálló ablaktábla a változásoknak az alkalmazás függőségeibe és erőforrásaiba való betekintéssel történő megtekintésére szolgál.

A felhasználói élmény elindításához keresse meg a Change Analysis kifejezést a Azure Portal található keresési sávon.

![Képernyőkép a keresési változások elemzéséről Azure Portal](./media/change-analysis/search-change-analysis.png)

A kiválasztott előfizetéshez tartozó összes erőforrás az elmúlt 24 órában történt változásokkal jelenik meg. Az összes módosítás a régi értékkel és új értékkel jelenik meg, hogy egyetlen pillantással szolgáltasson betekintést.

![Képernyőkép a Change Analysis panelről Azure Portal](./media/change-analysis/change-analysis-standalone-blade.png)

Egy módosításra kattintva megtekintheti a teljes Resource Manager-kódrészletet és egyéb tulajdonságokat.

![Képernyőkép a változás részleteiről](./media/change-analysis/change-details.png)

Bármilyen Visszajelzésért használja a visszajelzés küldése gombot vagy e-mailt changeanalysisteam@microsoft.com .

![Képernyőkép – visszajelzés gomb az elemzés módosítása lapon](./media/change-analysis/change-analysis-feedback.png)

### <a name="multiple-subscription-support"></a>Több előfizetés támogatása

A felhasználói felület támogatja több előfizetés kiválasztását az erőforrások változásainak megtekintéséhez. Használja az előfizetés szűrőt:

![A több előfizetés kijelölését támogató előfizetés-szűrő képernyőképe](./media/change-analysis/multiple-subscriptions-support.png)


## <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Alkalmazás-változási elemzés a problémák diagnosztizálása és megoldása eszközben

Az Application Change Analysis egy önálló detektor a webalkalmazásban a problémák diagnosztizálásához és megoldásához. Az **alkalmazás összeomlik** és a **webalkalmazás-leállási érzékelők** is összesítve vannak. A problémák diagnosztizálása és megoldása eszköz beírása után a **Microsoft. ChangeAnalysis** erőforrás-szolgáltató automatikusan regisztrálva lesz. Kövesse az alábbi utasításokat a webalkalmazás vendégen belüli módosítás-követésének engedélyezéséhez.

1. Válassza ki **a rendelkezésre állást és a teljesítményt**.

    ![Képernyőkép a "rendelkezésre állás és teljesítmény" hibaelhárítási lehetőségeiről](./media/change-analysis/availability-and-performance.png)

2. Válassza az **alkalmazás módosítása** lehetőséget. A funkció az **alkalmazások összeomlása** során is elérhető.

   ![Képernyőkép az "alkalmazás-összeomlások" gombról](./media/change-analysis/application-changes.png)

3. A hivatkozás az Application Change Analysis felhasználói felületének a webalkalmazásra hatókörét eredményezi. Ha a Web App in-Guest Change Tracking nincs engedélyezve, a fájl-és Alkalmazásbeállítások módosításának beolvasásához kövesse a szalagcímet.

   ![Képernyőkép az "alkalmazás-összeomlások" lehetőségeiről](./media/change-analysis/enable-changeanalysis.png)

4. Kapcsolja be az **elemzést** , és válassza a **Mentés** lehetőséget. Az eszköz az összes webalkalmazást megjeleníti App Service csomag alatt. A csomag szintje kapcsoló használatával bekapcsolhatja a tervben szereplő összes webalkalmazáshoz tartozó változások elemzését.

    ![Képernyőfelvétel: a Change Analysis felhasználói felületének engedélyezése](./media/change-analysis/change-analysis-on.png)

5. Az adatváltozás a **webalkalmazás kiválasztása lefelé** és az **alkalmazás-összeomlási** érzékelők között is elérhető. Ekkor megjelenik egy gráf, amely összefoglalja a változások típusát a változások részleteivel együtt. Alapértelmezés szerint az elmúlt 24 órában történt változások segítenek az azonnali problémák megoldásában.

     ![Képernyőkép a Change diff nézetről](./media/change-analysis/change-view.png)

## <a name="diagnose-and-solve-problems-tool"></a>Problémák diagnosztizálása és megoldása eszköz
A Change Analysis betekintési kártyaként érhető el a probléma diagnosztizálása és megoldása eszközben. Ha egy erőforrás problémákba ütközik, és az elmúlt 72 órában észlelt változások történtek, az észlelési kártya megjeleníti a módosítások számát. A Change Details (változások megtekintése) hivatkozásra kattintva a szűrt nézet jelenik meg a Change Analysis önálló KEZELŐFELÜLETén.

![Képernyőkép a változások észleléséről a problémák diagnosztizálására és megoldására szolgáló eszközben.](./media/change-analysis/change-insight-diagnose-and-solve.png)



## <a name="virtual-machine-diagnose-and-solve-problems"></a>Virtuális gépek diagnosztizálása és megoldása

Nyissa meg a problémák diagnosztizálása és megoldása eszközt a virtuális gépen.  Nyissa meg a **hibaelhárítási eszközöket**, keresse meg az oldalt, és válassza a **legutóbbi módosítások elemzése** lehetőséget a módosítások megtekintéséhez a virtuális gépen.

![Képernyőfelvétel a virtuális gép diagnosztizálásáról és megoldásáról](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Az analizátor módosítása a hibaelhárítási eszközökben](./media/change-analysis/analyze-recent-changes.png)

## <a name="activity-log-change-history"></a>Tevékenység naplójának változási előzményei

A tevékenység naplójában megjelenő változási [előzmények megtekintése](../essentials/activity-log.md#view-change-history) funkció az Analysis Service-háttér változásával lekéri a művelethez kapcsolódó módosításokat. Az [Azure Resource Graph](../../governance/resource-graph/overview.md) közvetlen meghívásához használt **korábbi** változások, de a háttér meghívása az alkalmazás változási elemzésének meghívására, így a visszaadott változások tartalmazzák az [Azure Resource Graph](../../governance/resource-graph/overview.md)-ból származó erőforrás-szintű változásokat, a [Azure Resource Manager](../../azure-resource-manager/management/overview.md)erőforrás-tulajdonságait, valamint a további, a Pásti-szolgáltatásokból, például app Services webalkalmazásból származó Ahhoz, hogy az alkalmazás megváltoztatja az Analysis Service-t, hogy ellenőrizni tudja a felhasználói előfizetések módosításait, regisztrálni kell egy erőforrás-szolgáltatót. Amikor először adta meg az **Előzmények** fület, az eszköz automatikusan elkezdi regisztrálni a **Microsoft. ChangeAnalysis** erőforrás-szolgáltatót. A regisztrációt követően az **Azure Resource Graph** változásai azonnal elérhetővé válnak, és az elmúlt 14 napra kiterjednek. A más forrásokból származó változások az előfizetés után 4 órával azután lesznek elérhetők.

![A műveletnapló változási előzményeinek integrációja](./media/change-analysis/activity-log-change-history.png)

## <a name="vm-insights-integration"></a>A VM-alapú adatáttekintések integrációja

Azok a felhasználók, akiknél [engedélyezve van a virtuális gépek](../vm/vminsights-overview.md) elemzése, megtekinthetik, hogy milyen változások történtek az egyes mérőszámok, például a processzor vagy a memória által okozott tüskék. Az adatváltozások integrálva vannak a virtuálisgép-elemzések oldalsó navigációs sávjában. A felhasználó megtekintheti, hogy történtek-e változások a virtuális gépen, és válassza a **változások vizsgálata** lehetőséget a módosítás részleteinek megtekintéséhez az Application Change Analysis önálló felhasználói felületén.

[![A VM-alapú adatáttekintések integrációja](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [változások elemzésével kapcsolatos problémák elhárításáról](change-analysis-troubleshoot.md)
