---
title: Irányítópultok létrehozása és megosztása Azure Log Analytics-adatokból | Microsoft Docs
description: Ez az oktatóanyag segít megérteni, hogy Log Analytics irányítópultok hogyan láthatják el az összes mentett napló lekérdezését, így egyetlen lencsével megtekintheti a környezetét.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 05/28/2020
ms.custom: mvc
ms.openlocfilehash: 84c710e44fbbccdefd5bf811477e1fa4c3989114
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043456"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Irányítópultok létrehozása és megosztása Log Analytics-adatokból

Log Analytics irányítópultok képesek megjeleníteni az összes mentett napló lekérdezését, így a szervezeten belül megkeresheti, korrelálhatja és megoszthatja informatikai működési adatait.  Ez az oktatóanyag egy olyan log-lekérdezés létrehozását ismerteti, amelyet az informatikai operatív támogatási csapat által elérhető megosztott irányítópult támogatásához fog használni.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Megosztott irányítópult létrehozása az Azure Portalon
> * Teljesítménynapló-lekérdezés megjelenítése 
> * Log-lekérdezés hozzáadása egy megosztott irányítópulthoz 
> * Csempe testreszabása megosztott irányítópulton belül

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre, amely [a Log Analytics-munkaterülethez csatlakozik](../vm/quick-collect-azurevm.md).  
 
## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 

## <a name="create-a-shared-dashboard"></a>Megosztott irányítópult létrehozása
Válassza az **irányítópult** lehetőséget az alapértelmezett [irányítópult](../../azure-portal/azure-portal-dashboards.md)megnyitásához. Az irányítópult az alábbi példától eltérően fog megjelenni.

![Azure Portal irányítópultja](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Itt összeállíthatók az informatikai szempontból legfontosabb működési adatok az összes Azure-erőforrásra vonatkozóan, beleértve az Azure Log Analyticsből származó telemetriákat.  Mielőtt megkezdené a naplózási lekérdezés megjelenítését, először hozzon létre egy irányítópultot, és ossza meg azt.  Ezután a példában szereplő Teljesítménynapló-lekérdezésre koncentrálunk, amely diagramként fog megjelenni, és hozzáadja az irányítópulthoz.  

> [!NOTE]
> A következő diagramtípusok támogatottak az Azure-irányítópultokon a naplók használatával:
> - areachart
> - columnchart
> - piechart (az irányítópulton fánkként jelenik meg)
> - scatterchart
> - idődiagramját

Egy irányítópult létrehozásához kattintson az **Új irányítópult** gombra a jelenlegi irányítópult neve mellett.

![Új irányítópult létrehozása az Azure Portalon](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Ez a művelet létrehoz egy új, üres, privát irányítópultot, amely rögtön testreszabási módba lép, ahol elnevezheti az irányítópultot, valamint hozzáadhat csempéket vagy átrendezheti azokat. Szerkessze az irányítópult nevét, és adja meg a *minta irányítópultot* ehhez az oktatóanyaghoz, majd válassza a **Testreszabás kész** lehetőséget.<br><br> ![Testreszabott Azure-irányítópult mentése](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Amikor létrehoz egy irányítópultot, az alapértelmezetten privát lesz, vagyis csak Ön láthatja. Ahhoz, hogy mások számára is láthatóvá tegye, használja az egyéb irányítópult-parancsok mellett megjelenő **Megosztás** gombot.

![Új irányítópult megosztása az Azure Portalon](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

A rendszer felkéri, hogy válasszon ki egy előfizetést és egy erőforráscsoportot, ahová az irányítópultot közzé kívánja tenni. A kényelmesebb használat érdekében a portál közzétételi folyamata egy olyan mintához vezet, amellyel az irányítópultokat egy **irányítópultok** elnevezésű erőforráscsoportba helyezi.  Ellenőrizze a kiválasztott előfizetést, majd kattintson a **Közzététel** elemre.  Az irányítópulton megjelenő információk elérését az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)vezérli.   

## <a name="visualize-a-log-query"></a>Napló lekérdezésének megjelenítése
A [log Analytics](../logs/log-analytics-tutorial.md) egy dedikált portál, amely a naplózási lekérdezésekkel és azok eredményeivel való együttműködésre szolgál. Szolgáltatásai közé tartozik a lekérdezés több sorban való szerkesztése, a szelektív kódvégrehajtás, a kontextusfüggő Intellisense és az intelligens elemzés. Ebben az oktatóanyagban a Log Analytics használatával grafikus formában hozza létre a teljesítmény nézetet, mentse egy későbbi lekérdezéshez, majd rögzítse azt a korábban létrehozott megosztott irányítópultra.

Nyissa meg Log Analytics a Azure Monitor menüben a **naplók** lehetőség kiválasztásával. Egy új üres lekérdezéssel kezdődik.

![Kezdőlap](media/tutorial-logs-dashboards/homepage.png)

Adja meg a következő lekérdezést a processzor-kihasználtsági rekordok Windows és Linux rendszerű számítógépekre történő visszaadásához, a számítógép-és TimeGenerated szerint csoportosítva, valamint egy vizualizációs diagramon. Kattintson a **Futtatás** gombra a lekérdezés futtatásához és az eredményül kapott diagram megtekintéséhez.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Mentse a lekérdezést a lap tetején található **Save (Mentés** ) gombra kattintva.

![Lekérdezés mentése](media/tutorial-logs-dashboards/save-query.png)

A **lekérdezés mentése** panelen adjon meg egy olyan nevet, mint például *Az Azure virtuális gépek – processzor kihasználtsága* és egy kategória, például *irányítópultok* , majd kattintson a **Mentés** gombra.  Így létrehozhatja a használható és módosítható gyakori lekérdezések tárait.  Végül a lap jobb felső sarkában lévő **rögzítés az irányítópultra** gombra kattintva rögzítse a korábban létrehozott megosztott irányítópultra, majd válassza ki az irányítópult nevét.

Most, hogy rögzítettünk egy lekérdezést az irányítópulton, láthatja, hogy az irányítópult kapott egy általános címet és egy cím alatti megjegyzést.

![Azure-irányítópult – példa](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Adjunk neki egy pontosabb nevet, hogy más felhasználók számára is érthető legyen.  Kattintson a Szerkesztés gombra a csempe címének és alcímének testreszabásához, majd kattintson a **frissítés** gombra.  Egy szalag jelenik meg, ahol választania kell, hogy közzéteszi vagy elveti a módosításokat.  Kattintson **a másolat mentése** gombra.  

![A minta-irányítópult kész konfigurációja](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtanulta, hogyan hozhat létre irányítópultot a Azure Portalban, és hogyan adhat hozzá egy napló-lekérdezést.  Folytassa a következő oktatóanyaggal, amely a napló lekérdezési eredményei alapján megvalósítható különböző válaszokat ismerteti.  

> [!div class="nextstepaction"]
> [Eseményekre való válaszadás Log Analytics-riasztásokkal](../alerts/tutorial-response.md)